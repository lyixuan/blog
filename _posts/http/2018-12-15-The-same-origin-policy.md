---
layout: post
title: 同源策略
categories: HTTP
description: 同源策略
keywords: 同源策略
---

同源策略是一种约定，它是浏览器最核心也是最基本的安全功能。
浏览器的同源策略，**限制了来自不同源的”document”或脚本，对当前“document”读取或设置某些属性。**

------

对于影响到“源”的因素有：

* host（域名或IP地址，如果是IP地址则看做一个根域名）
* 子域名
* 端口
* 协议

-----
在浏览器中，对于标签 `<script>、<img>、<iframe>、<link> `等标签都可以跨域加载资源，而不受同源策略的限制。这些带有“src”属性的标签每次加载的时候，实际上是由浏览器发起一个get请求。

不同于XMLHttpRequest的是，通过src属性加载的资源，浏览器是限制了javascript的权限，使其不能够读写返回的内容。

对于 XMLHttpRequest来说，它可以访问来自同源对象的内容。但是不能够访问跨域访问资源，所有在ajax开发中尤其需要注意这点在w3c委员会 制定了XMLHttpRequest跨域访问标准。他需要通过目标域返回的HTTP头授权是否允许跨域访问，因为HTTP头对于javascript来说一 般是无法控制的，所以认为这个方案是可行的。

对于浏览器来说：除了DOM、Cookie、XMLHttprequest会受到同源策略的限制外，浏览器加载的第三方插件也有各自的同源策略。

需要跨任意域肯定是关注浏览器层面的，比如ie是res协议跨域、特权区域
