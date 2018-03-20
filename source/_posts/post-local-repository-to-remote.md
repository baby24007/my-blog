---
title: post local repository to remote
date: 2018-03-20 22:12:04
tags: 
- git
---------
> 哎，之前的blog不小心被我删除了，现在本地文件还在，怎么把本地代码到github呢，傻瓜式操作

## 如何清除之前的git信息
```
rm -rf .git
```
## 首先要有个本地目录 比如 /my-blog ，然后
```
git init
git add *
git commit -m '之前丢了的git repository重新提到remote'
```
## 然后去github上创建自己的Repository，找到新仓库的https地址，然后
```
git remote add origin https://github.com/baby24007/my-blog.git 
```
* 此时`git pull origin master`是不好使的，因为git是不能管理空的文件夹的,文件夹里必须有文件才能上传
* 所以可以直接push
```
git push -u origin master
```
## 好的，现在github上有文件了


## 什么是原型链，原型链的作用是什么呢？
简单来说，因为用构造函数生成实例对象，有一个缺点，那就是无法共享属性和方法。每一个实例对象，都有自己的属性和方法的副本。这不仅无法做到数据共享，也是极大的资源浪费。
考虑到这一点，Brendan Eich决定为构造函数设置一个prototype属性。
这个属性包含一个对象（以下简称"prototype对象"），所有实例对象需要共享的属性和方法，都放在这个对象里面；那些不需要共享的属性和方法，就放在构造函数里面。
实例对象一旦创建，将自动引用prototype对象的属性和方法。也就是说，实例对象的属性和方法，分成两种，一种是本地的，另一种是引用的。
> prototype对象的属性和方法其实都是同一个内存地址，指向prototype对象，因此就提高了运行效率。

使用prototype属性实现继承时，需要手动纠正，将Child.prototype对象的constructor值从Father改为Child
这是很重要的一点，编程时务必要遵守。下文都遵循这一点，即如果替换了prototype对象，
```
o.prototype = {};
```
那么，下一步必然是为新的prototype对象加上constructor属性，并将这个属性指回原来的构造函数。
```
o.prototype.constructor = o;
```

More info: [Javascript面向对象编程（二）：构造函数的继承](http://www.ruanyifeng.com/blog/2010/05/object-oriented_javascript_inheritance.html)

