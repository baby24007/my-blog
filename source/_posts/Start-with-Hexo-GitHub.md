---
title: Start with Hexo & GitHub
date: 2016-05-30 15:07:15
---

## 如何在github上搞一个个人主页

* 要有一个命名规则为`username.github.io` 的仓库，里面放的是这个网站的code
**乱起名字不乖，不生效的，还要删掉＝。＝**

* 上一步疑惑了我很久，是因为我没看文档，so，继续丢文档-[github创建gh-pages的文档](https://help.github.com/articles/creating-pages-with-the-automatic-generator/)

* 然后就可以部署一个hexo了,先丢官网：足以完成环境的部署及所需要做的准备-[Hexo官网](https://hexo.io/)

* 修改配置(_config.yml)
```bash
deploy:
  type: git
  repo: https://github.com/baby24007/baby24007.github.io.git
  branch: master
```
* 接着在 Git Bash 中依次输入：
```bash
npm install hexo-deployer-git --save    # 安装使用 git 方式进行部署所需要的插件
hexo d                # 部署到 Github 上，按照提示输入自己 Github 的用户名和密码。
```
* 部署会强制覆盖掉你之前生成的页面，在博客的目录下会产生 .deploy_git文件夹，不要删除，否则你的部署记录就会不见。之后就可以通过 http://baby24007.github.io 访问你的网站啦。在之后的部署时，建议输入以下代码:
```bash
hexo clean  # 清除之前 public 文件夹的内容
hexo g      # 生成静态的 public 文件夹，部署时候也是直接拷贝此文件夹里的文件。
hexo d      # 部署到 Github 上，按照提示输入自己 Github 的用户名和密码。
# hexo g 和 hexo d 这两条命令可以合并成 hexo d --g 或者 hexo g --d
```
* 想将theme的language设置为中文,始终没有成功
    应该在哪里配置呢?主题配置文件 or 站点配置文件?明明写的是站点配置文件,但设置完了会报错,神马情况
    反正都不行
* 换了一个主题:[next](http://theme-next.iissnan.com/getting-started.html#download-next-theme),也是不知道如何设置语言

* 创建一个新的页面
```bash
   hexo new page 'about'
```
* 调试的时候记得开debug模式
```bash
    hexo s -debug
```
* 想要增加评论功能,尝试使用[多说](http://dev.duoshuo.com/docs)
1.  next主题中配置多说
```bash
        duoshuo_info:
          ua_enable: true
          admin_enable: false
          user_id:
          admin_nickname:
```
2.  登录并访问「我的主页」获取 user_id ， 此 ID 是 网址最后那串数字
3.  然后自己去翻多说的文档?
* 想要贴图的话需要外链啦,我需求不大,可以使用[七牛图床](http://qiniupicbed.sinaapp.com)
![贴图](http://qiniupicbed.qiniudn.com/upload/7a960021f8d82ee05d5e43c265175584.png)
其他的推荐有[FarBox](https://www.farbox.com)，[Dropbox](https://www.dropbox.com)，[又拍云](https://www.upyun.com/zh/index.html)
* 参考文章:
> [Hexo + Git 搭建免费的个人博客](http://www.cylong.com/blog/2016/04/19/hexo-git/)
> [hexo的出生地呦!](https://zespia.tw/blog/2012/10/11/hexo-debut/)
> [hexo爸爸的github](https://github.com/tommy351)
