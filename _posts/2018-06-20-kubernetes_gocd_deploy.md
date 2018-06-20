---
layout: 	  blog
title:		  kubernetes 部署 gocd 记录
subtitle:     学习笔记
categories:   Linux
tags: 		  kubernetes gocd cd
redirect_from:
  - /web/kubernetes_gocd_deploy.html
  - /2018/06/20/kubernetes_gocd_deploy/
---

最近由于公司项目需要, 上了 kubernetes, 所以需要实现一套 cd 方案, 最终选择了 Thoughtworks 开发的 gocd, 记录一些开发中遇到的坑.

# CD 流程简述

`github` 提交 `commit` => `Jenkins`  打包 (`Egret` 开发的`html5`游戏) => 提交 `github` 修改(新的仓库) 
=> `gocd` 制作 `docker` 并部署到 `kubernetes`

# 坑
## 什么是 Minikube
* 区别于 `cluster`(集群), 用 `kubernetes` 的话肯定是为了集群(最少3台 服务器), 
那么这个 `minikube` 有什么用呢? 
在我看来是给用户一个快速测试的环境, 毕竟在一台 服务器 上就可以完成了.
* 顺便这里吐槽一下 `scaleway` 的内核, 由于 `minikube` 推荐使用 `vm` 环境, 然而在安装过程中发现,
内核不支持, 尝试了很久去更换, 最终发现 `scaleway` 在开机启动脚本中写死了他自己定制的内核 -- 
按官方的说法: 无法更换! 很坑 ort

## kubernetes 的构成 pod deployment service ingress
* deployment: 核心业务逻辑, 创建过程中生成 `pod` (类似 `docker` 的 `container`);
* service: 将 `deployment` 与 `kubernetes` 建立联系;
* ingress: 将 `service` 与 外部 建立联系;

```bash
# -n gocd 意思是: namespace 为 gocd
# 获取在 deployment 列表
kubectl get deployment -n gocd

# 获取 名为 gocd-server 的 dployment 详情
kubectl describe deployment gocd-server -n gocd

# 删除 deployment
kubectl delete deployment gocd-server -n gocd
```
* 注: 删除 pod 类似重启 deployment, 而要完全删除一个 pod, 需要删除 deployment;

## 调用 API 403
`
curl: (22) The requested URL returned error: 403 Forbidden
`

需要创建 `clusterrolebinding`

```bash
kubectl create clusterrolebinding clusterRoleBinding \
--clusterrole=cluster-admin \
--serviceaccount=kube-system:default
```

## gocd 官方 Ingress 就是个坑!
官方给的 `helm` 安装方法:
```bash
helm repo add stable https://kubernetes-charts.storage.googleapis.com
helm install stable/gocd --name gocd --namespace gocd
```
看起来很美好, 然而事实上没法用!
目前的结论是 `https` 的锅.
> https://github.com/kubernetes/charts/blob/master/stable/gocd/templates/ingress.yaml

源码中配置如下, 但是无论如何就是不行
```yaml
spec:
  backend:
    serviceName: \{\{ template "gocd.fullname" . \}\}-server
    servicePort: \{\{ .Values.server.service.httpPort \}\}
  rules:
    \{\{- range $host := .Values.server.ingress.hosts \}\}
    - host: \{\{ $host \}\}
    \{\{- end -\}\}
  \{\{- if .Values.server.ingress.tls \}\}
  tls:
\{\{ toYaml .Values.server.ingress.tls | indent 4 \}\}
  \{\{- end -\}\}
```

> https://github.com/kubernetes/ingress-nginx/blob/master/docs/examples/tls-termination/ingress.yaml

最后参考了上面的连接后, 如下配置即可 ╮(╯_╰)╭ 迷
```yaml
spec:
  tls:
    - hosts:
      - www.example.com
      # This assumes tls-secret exists and the SSL
      # certificate contains a CN for gocd.laiwan.io
      secretName: gocd-tls
  rules:
    - host: www.example.com
      http:
        paths:
        - path: /
          backend:
            # This assumes http-svc exists and routes to healthy endpoints
            serviceName: gocd-server
            servicePort: 8153
```

使用自定义的 `values.yaml` 安装:
```bash
# 彻底删除
helm delete --purge gocd

# 使用 fork 的 url, 注意是 raw 文件
# 同时 ./gocd 是本地 pull 下来的文件(包含自行修改的 ingress)
helm install -f https://raw.githubusercontent.com/tokinonagare/charts/master/stable/gocd/values.yaml\
 ./gocd --name gocd --namespace gocd
 
# 也可以
helm install -f values.yaml stale/gocd --name gocd --namespace gocd
```

## 端口转发
在调试 `Ingress` 前, 为了验证 `pod` 是否已经正常跑起来, 可以使用端口转发的方法:
```bash
# 服务器
kubectl port-forward --namespace gocd gocd-server-6cd5c448c9-kp844 8153

# 本地
ssh root@51.23.233.23 -L 8153:localhost:8153
```
浏览器即可打开 `localhost:8153`

## pvc pv 
* pv: 在集群中挂载一个硬盘空间(没有 `namespace` 的区分);
* pvc: 将 `pv` 和 `pods` 起来;

由于不是 `AWS` `GCE` 在没有上 `Ceph` 之前, 使用在 `host` 上挂载一个硬盘空间, 重装过程如下:
```bash
# 删除 pv 和 pvc
kubectl delete pv gocd-pv && kubectl delete pvc gocd-pv -n gocd

# 删除 host 上的文件
rm -rf /data/gocd

# 重新创建文件
mkdir /data/gocd

# 重新创建 pv 和 pvc
kubectl apply -f pv.yaml && kubectl apply -f pvc.yaml -n gocd
```

## Ingress loadbalance 里面 IP 怎么是空的?
说好的 ip 通过下面的命令获取, 然而并卵用 ort
```bash
1. Get the GoCD server URL by running these commands:
    It may take a few minutes before the IP is available to access the GoCD server.
         echo "GoCD server public IP: http://$(kubectl get ingress gocd-server --namespace=gocd  -o jsonpath='{.status.loadBalancer.ingress[0].ip}')"
```
很显然, gocd 把用他们服务的都想成了是 `AWS` `GCE` 的高富帅,`scaleway` 这种屌丝, 
并没有自带 `loadbalance`, 当然没有咯 =_=

## 参考
* [gocd 官方 安装 & 配置 教程](https://docs.gocd.org/current/gocd_on_kubernetes/gocd_helm_chart/setup.html
)