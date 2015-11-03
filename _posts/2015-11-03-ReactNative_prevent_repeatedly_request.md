---
layout: 	  blog
title:		  ReactNative 防止重复请求
subtitle:   学习笔记
categories: App
tags: 		  ReactNative iOS
redirect_from:
  - /web/ReactNative_prevent_repeatedly_request.html
  - /2015/11/03/ReactNative_prevent_repeatedly_request/
---

* Demo地址: https://github.com/tokinonagare/ReactNative-PropertyFinder

当点击按钮进行`url``loading`的请求过程时，再次点击按钮，会出现重复请求，代码如下：

```js
<TouchableHighlight     
  style         = {styles.searchButton}
  onPress       = {this.onSearchPressed}
  underlayColor = 'gray'>
  <Text style   = {styles.searchButtonText}>Go</Text>
</TouchableHighlight>
```
```js
onSearchPressed: function() {
      this.setState({
        message: ''
      });
      var query = urlForQueryAndPage('place_name', this.state.searchString, 1);
      this._executeQuery(query);
},
```
```js
_executeQuery: function(query) {
  console.log(query);
  this.setState({
    isLoading: true
  });
  fetch(query)
    .then(response => response.json())
    .then(json     => this._handleResponse(json.response))
    .catch(error   =>
      this.setState({
        isLoading: false,
        message:  'Something bad happend ' + error
      }));
},
```
<!-- more -->

```js
_handleResponse: function(response) {
  this.setState({
		isLoading: false
  });	
  if (response.application_response_code.substr(0, 1) === '1') {
      this.props.navigator.push({
        title: 'Results',
        component: SearchResults,
        passProps: {listings: response.listings}
      }); 
      } else {
        this.setState({
          message:  'Location not recognized; please try again.',
        });
  };
},
```

这样将导致点击几次按钮，就会请求多少次，出现多少次`SearchResults`的页面加载。
很容易就能想到对'onSearchPressed'进行一个锁定判断：

```js
onSearchPressed: function() {
  if (this.state.isLoading == false) {
      this.setState({
        message: ''
      });
      var query = urlForQueryAndPage('place_name', this.state.searchString, 1);
      this._executeQuery(query);
  };     
},
```
看起来已经解决问题了，然而在`_handleResponse`这个方法中，页面在切换到`SearchResults`的过程中，如果这个时候点击按钮依然会进行一次重复请求，由于刚接触Javascript不久，卡了挺长一段时间，解决方法如下：

```js
isLoadingComplete: function() {
  this.setState({
    isLoading: false
  });
},

_handleResponse: function(response) {
  if (response.application_response_code.substr(0, 1) === '1') {
      this.props.navigator.push({
        title: 'Results',
        component: SearchResults,
        passProps: {listings: response.listings}
      }); 
      // Prevent onPress request while switch to PropertyDetail page.
      setTimeout(this.isLoadingComplete, 1000);
      } else {
        this.setState({
          message:  'Location not recognized; please try again.',
          isLoading: false
        });
  };
},
```
使用`setTimeout`进行一个延时的解锁, 因为目的仅仅需要在页面切换的这个时间段内能够防止用户点击按钮即可。
_（注：别忘了将`isLoading: false`添加到`else`的情况）_


