---
layout: 	  blog
title:		  "Blog誕生"
subtitle:   "GitHub 博客搭建问题记录"
categories: Web
tags: 		  Github Markdown Jekyll
redirect_from:
  - /web/Blog_TanJyou.html
  - /2015/07/30/Blog_TanJyou/
---

> 首先感谢[Harttle](http://harttle.github.io)这套blog代码.
如何使用Github和Jekyll搭建Blog的文章网上已经很多, 就不赘述了, 姑且记录一些自己遇到的问题.

# 域名DNS问题

> 由于是第一次使用非ip解析域名, 当使用`CNAME`记录将`tokinonagare.com`解析到`tokinonagare.github.io`时, [Cloudflare](https://www.cloudflare.com)上出现一个"!"的警告, 并且无法连接`tokinonagare.com.`

1. 需在项目文件夹中新建`CNAME`文件, 写入`tokinonagare.com`;
2. 因为是顶级域名, 需在DNS使用`ALIAS`记录, 然而[Cloudflare](https://www.cloudflare.com)没有这个记录选项, 所以需直接用`A`记录到两个ip:`192.30.252.153`&`192.30.252.154`**注: ip会更改**;
3. 如果不使用`A`记录, 使用`CNAME`记录到`tokinonagare.com`访问正常, 按照Github的说法会出现邮件的解析问题(未验证);

* 参考: https://help.github.com/articles/my-custom-domain-isn-t-working/
* 参考: http://justcoding.iteye.com/blog/1959737

<!-- more -->

# 目录摘要显示

> 源代码中目录页面中的每篇Blog文章占据了太大的篇幅, 秉着使用尽量少的操作浏览尽量多的内容, 故进行修改.

①首先尝试CSS, 添加行高限制, 隐藏溢出内容, 然而会出现部分最后一行行显示半截, 故舍弃;

```cpp
#posts > div.post.paper{
	height-line: 150%;
	height:  40em;
	overflow: hidden;
}
```

②考虑`jekyll`对于这种问题应该会做处理, 于是去查看源码;

```cpp
  {% raw %}
	{{ post.content | split:"<!-- more -->" | first }}
  {% endraw %}
```

`<!-- more -->`是一个分割标签, 该段代码含义: 显示从文章开始到该标签的内容;

③用字数进行限制, 然而这样会导致HTML的内容也会显示出来;

```cpp
	{% raw %}
	{{ post.content | truncatewords: 300 }}
	{% endraw %}
```

④使用摘要显示

```cpp
	{% raw %}
	{{ post.excerpt }}
	{% endraw %}
```
需在`_config.yml`中添加: `excerpt_separator: "<!-- more -->"`

```cpp
---
excerpt: "摘要内容"
---
```
需要手动添加内容, 对于懒人来说太困难了_(:з」∠)...

* 参考: http://mikeygee.com/blog/truncate.html
* 参考: https://blog.omgmog.net/post/adding-support-for-more-tag-to-jekyll-without-plugins/
* 参考: http://jekyllrb.com/docs/posts/#post-excerpts
* 参考: https://truongtx.me/2013/05/01/jekyll-read-more-feature-without-any-plugin/

# 敏感词处理

> Liquid Exception: undefined method `split' for nil:NilClass

如果直接写入{% raw %}`{{ post.content | split:'<!-- more -->' | first }}`{% endraw %}会出现报错, 需要添加`{% raw %}{% raw %}{% endraw %}`和`{% raw %}{% {% endraw %}endraw %}`;

```cpp
  {% raw %}{% raw %}{% endraw %}
	{% raw %}{{ post.content | split:"<!-- more -->" | first }}{% endraw %}
  {% raw %}{% {% endraw %}endraw %}
```
然而我在写入`{% raw %}{% raw %}{% endraw %}`和`{% raw %}{% {% endraw %}endraw %}`自身时, 又遇到了问题, 方法是拆分掉`{% raw %}{%{% endraw %}`;

`{% raw %}{% raw %}{% endraw %}`:

```cpp
	{% raw %}{% raw %}{% raw %}{% {% endraw %}endraw %}
```

`{% raw %}{% {% endraw %}endraw %}`:

```cpp
	{% raw %}{% raw %}{%{% {% endraw %}endraw %}endraw %}
```

* 参考: https://github.com/jekyll/jekyll/issues/3381
* 参考: http://blog.slaks.net/2013-06-10/jekyll-endraw-in-code/

# Gitignore

> 由于每次本地jekyll serve 的时候都会在_site/文件夹中生成网站文件, 而push的时候需要git commit的话显得就很多余, 故想到能否屏蔽掉这一个文件夹

根目录新建文件`.gitignore`写入

```
_site/
.sass-cache/
.jekyll-metadata
```
传送门: [Github/Jekyll.gitignore](https://github.com/github/gitignore/blob/master/Jekyll.gitignore){:target="_blank"}

# 新窗口中打开URL链接

> 使用`{% raw %}[Tokinonagare](http://tokinonagare.com){% endraw %}`打开的URL只能在当前窗口打开, 对于一些参考链接的话非常不方便

添加jQuery`_layouts/default.html`

```cpp
<script>
//Url在新窗口中打开
  $(document.links).filter(function() {
    return this.hostname != window.location.hostname;
  }).attr('target', '_blank');
</script>
```


* 参考: http://www.chengxusheji.com/archives/121.html
* 参考: https://github.com/github/gitignore