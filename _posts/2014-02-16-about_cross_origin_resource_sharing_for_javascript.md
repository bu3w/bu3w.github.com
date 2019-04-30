---
layout: post
title: Javascript 的跨域访问
---

众所周知，在 Javascipt 中做跨域的访问，通常会出现问题。注意哦，是“通常”～

故事的由来是这个样子滴～

寻常的 AJAX 是使用 Javascript 的 XMLHttpRequest 对象访问远程获得数据，并操作 DOM 。但这里如果访问的资源在同一个域就肯定没有问题了。

在有些时候，你会得到一个错误：
* 在 Firefox 中会得到一个异常，错误号 0x80004005 (NS_ERROR_FAILRUE)。
* 在 Chrome 中会清晰一点，直接就提示被访问的服务的头中没有 "Access-Control-Allow-Origin"。

这样我就有了第一个 Javascript 跨域访问的方法，即在被访问的服务的 HTTP 头里加上相关的信息。
可以以“CORS”或者“Cross-Origin Resource Sharing”或者相关词为关键字搜索。比如说：[http://www.w3.org/TR/cors/](http://www.w3.org/TR/cors/)

这种方法需要改服务，有其他方法么？

有！JSONP！

简单的说，传统访问同域的服务的方法返回的 JSON 串是一个裸的 JSON 串。而 JSONP 返回的是一个 Javascript的函数调用，这个函数的参数是原来的“裸 JSON 串”。

怎么执行呢？新建一个 script 节点，src 是 JSONP 的地址，嵌入到当前页面中。

OK！

请看例子～

如果知乎对外提供服务的话，可以做一个静态页的知乎日报。
[http://bu3w.github.io/zhihudaily.jsapp/](http://bu3w.github.io/zhihudaily.jsapp/)

这里没有用 CSS 只是个例子，可以用在手机上，点开几个文章在路上看。  
这里用到的 API 是在知乎帖子里写的，应该不是对外公开的。  
也使用到了 [http://jsonp.jit.su/](http://jsonp.jit.su/) 这个 JSON Proxy 服务。

在此对用到的服务表示感谢～
