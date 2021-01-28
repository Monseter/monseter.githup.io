---
title: AJAX
date: 2021-01-28 10:39:01
tags: Ajax
categories: JavaScript
typora-root-url: ./
---
## AJAX

- AJAX是一种在无需重新加载整个网页的情况下，能够更新部分网页的技术。又称为单页面刷新。

- AJAX = 异步 JavaScript 和 XML。

- AJAX是一种用于创建快速动态网页的技术。通过在后台与服务器进行少量数据交换，AJAX可以使网页实现异步更新。着意味着可以在不重新加载网页更新。

- 传统的网页（不使用AJAX）如果更新内容，必须重新加载整个网页面。

- 有很多使用AJAX的应用案例：新浪微博、Goole地图、开心网页等等。

## AJAX是基于现有的Internet标准

- XMLHttpRequest对象（异步的与服务器交换数据）
- JavaScript/Dom（信息显示与交互）
- CSS（给数据定义样式）
- XML（作为转换数据的格式）

**注**：AJAX应用程序与浏览器和平台无关的！

```javascript
function ajax(params,callback) {
    // 1.创建XMLHttpRequest 对象实例
    let xhr = new XMLHttpRequest();
    // 2.服务器链接
    // 首先判断请求方式
    if (params['method'].toLowerCase() == 'get') }{
        // 判段是否有拼接的参数
        if (params['data']) {
            params['server'] = addURLParams(params['server'],params['data']);
        }
        // true 表示异步
        xhr.open(params['method'], params['server', true]);
        // 设置请求头部配置
        xhr.setRequestHeader(
            'content-type',params['content-type']
        );
        // 3.发送请求
        xhr.send(null)
    } else {
        xhr.open(params['method'],params['content-type']);
        xhr.setRequestHeader('content-type',params['content-type']);
        xhr.send(Json.stringfy(params['data']))
    }
    // 4.接收服务器响应返回的数据
    // readyState 返回的状态码（0-4）
    /*
    0 （未初始化）open还没有调用
    1 （载入）已经调用了send() 处于正在请求中。。。
    2 （载入完成）send()方法已经完成，已经收到了全部的响应数据
    3 （解析）解析响应数据
    4 （完成）响应内容解析完成，可以返给客户端使用
    **/
    xhr.readystatechange = function() {
        if (xhr.readyState == 4) {
            if (xhr.status == 200 && xhr.status < 300 || xhr.status == 304) { // 响应ok
                callback(JSON.parse(xhr.responseText));
                // 上面是将json对象序列化，如果是字符串新式的话用下面
                callback(xhr.responseText);
            } else { // 响应出错情况下
                callback({"status_code": xhr.status})
            }
        }
    }
}

// 拼接浏览器地址
// url 一般是我们要传的接口地址，data 是我们请求数据参数
function addUrlParams(url,data) {
    for (var key in data) {
        url += (url.indexOf('?') == -1) ? '?' : '&';
        url += encodeURIComponent(key) + '=' + encodeURIComponent(data[key])
    }
    return url
}
```