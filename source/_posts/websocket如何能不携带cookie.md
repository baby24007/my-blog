---
title: websocket如何能不携带cookie
date: 2018-03-22 18:08:33
tags:
- websocket
- cookie
---

## 背景：
   IM工程接口都迁移到另一个工程下，域名是channel.xiaoshouyi.com;同时部署了一个相同域名不同协议的websocket。此时ws请求中会携带所有.xiaoshouyi.com下的cookie，导致同时打开crm.xiaoshouyi.com的其他页面会因此被logout。
   在网上搜了一下貌似没有什么文章有介绍websocket如何能不携带cookie的，于是只能自己动手尝试。

### 1.尝试第一种，设置Access-Control-Allow-Credentials
> CORS请求默认不发送Cookie和HTTP认证信息。如果要把Cookie发到服务器，一方面要服务器同意，指定Access-Control-Allow-Credentials字段。
```
Access-Control-Allow-Credentials: true
```
另一方面，开发者必须在AJAX请求中打开withCredentials属性。
```
var xhr = new XMLHttpRequest();
xhr.withCredentials = true;
```
否则，即使服务器同意发送Cookie，浏览器也不会发送。或者，服务器要求设置Cookie，浏览器也不会处理。
需要注意的是，如果要发送Cookie，Access-Control-Allow-Origin就不能设为星号，必须指定明确的、与请求网页一致的域名。同时，Cookie依然遵循同源政策，只有用服务器域名设置的Cookie才会上传，其他域名的Cookie并不会上传，且（跨源）原网页代码中的document.cookie也无法读取服务器域名下的Cookie。

按以上说明，应该干掉nginx里配置的Access-Control-Allow-Credentials就可以
 
 ### Testing...