---
title: 由于不停被不稳定的后端接口阻塞所引发的对前后端分离的思考
date: 2018-05-11 20:58:47
tags: 
- nodejs 
- mock 
- nginx
---

## 初想：基于nodejs搭建一个mock环境

> 最近在做开发的时候开发环境的接口频频出现问题，自己本地启动的java环境里，接口一报错我就一脸懵逼，然后就得去找相关的人解决异常，而小部件又属于那种只要有一个环节没有打通的就无法往后进行的这么个类型，一天到晚都在解决各种环境问题，一天时间里自己的代码没写多少，一半天儿的处理zookeeper问题，两半天儿的处理服务起不来的情况，感觉整个人都不好了。至此，我突然明白了什么才叫真正意义上的前后端分离。

曾经一直以为前端所用到的数据全都通过ajax等异步形式获取，前端的人只管展现，后端的人只管数据，就叫做前后端分离。但是这只是前后端分离的其中一部分。这样相对于那种前端切图写静态页面，然后交给后端变成jsp/php/vm模版的模式已经强太多了。但是仍然会存在一些问题：

- 后端接口数据不稳定，开发效率难以保证
- 自己搭建后端环境很痛苦，试过都会懂的

那么怎么才能最快速的解决这个问题呢？我有一个初步的设想，目前自己本地环境的静态资源是通过nginx反向代理到本地路径的，那么我只需要把所有的action请求做一层代理，代理到一个无需走服务器，但是也能返回相应格式的数据即可。那么画一个图，流程大概是这样的：
![A flow](http://chuantu.biz/t6/309/1526047711x-1404781186.png)

### 如何搭建

#### **准备mock数据**
根据[Rap](http://rap.ingageapp.com)(RAP是一个可视化接口管理工具)上的接口生成mock数据，这样做就要求接口严格按照Rap上的返回参数格式来实现接口，否则前端写半天搞什么分离都是白搭。
#### **搭建nodejs服务**
`我先去看书了...`
#### **根据action读取mock数据并返回**
`TODO...`
#### **配置Nginx**
找到nginx配置路径 `/usr/local/etc/nginx/`
找到配置接口location的地方 `/usr/local/etc/nginx/servers/ingage.conf`
增加一个location把/widget/路径下的action代理到nodejs服务上


> **相关文章 ：**
[淘宝前端团队FED：前后端分离的思考与实践（一）](http://taobaofed.org/blog/2014/04/05/practice-of-separation-of-front-end-from-back-end/)