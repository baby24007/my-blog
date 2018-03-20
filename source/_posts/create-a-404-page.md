---
title: create a 404 page
date: 2016-05-31 10:19:42
tags: 
- 404
- hexo
---
## 如何在hexo中建立一个404页面
Github是可以直接在根目录建立404页面的,详见[自定义404](https://help.github.com/articles/creating-a-custom-404-page-for-your-github-pages-site/)
但是自定义404页面仅对绑定顶级域名的项目才起作用，GitHub默认分配的二级域名是不起作用的。
这该如何是好?
参考> [hexo你的博客](http://ibruce.info/2013/11/22/hexo-your-blog/)
可以创建一个公益404,我准备先试一下,然后成功了.
当然在本地服务器是不生效的,需要push后在线上才能访问到:[这里是一个错误地址](http://baby24007.github.io/error)
很简单:
1. 在/source下创建一个html,named:404.html
2. html中的内容就是你想展示的404内容
3. 腾讯公益404页面代码:
```html
<!DOCTYPE html>
<html>
<head>
</head>
<body>
<script type="text/javascript" src="http://www.qq.com/404/search_children.js" charset="utf-8" homePageUrl="http://baby24007.github.com" homePageName="回到主页"></script>
</body>
</html>
```
4. 然后就用这个吧.懒得写了.

### 参考文章
> [md的header?-Front Matter](http://jekyllrb.com/docs/frontmatter/)
> [hexo你的博客](http://ibruce.info/2013/11/22/hexo-your-blog/)
> [自定义404](https://help.github.com/articles/creating-a-custom-404-page-for-your-github-pages-site/)
