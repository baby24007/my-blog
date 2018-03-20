---
title: Resolved issues
date: 2016-05-31 14:49:08
tags: issues
---
## Deploy or 启动server时出错
### 错误信息
```console
bogon:tryHexo admin$ hexo server
 INFO  Start processing
 ERROR Theme config load failed.
 ERROR Process failed: _config.yml
 YAMLException: can not read a block mapping entry; a multiline key may not be an implicit key at line 245, column 1:
     # Disqus
     ^
     at generateError (/Users/admin/PhpstormProjects/test/BonitaOnTheWay/tryHexo/node_modules/hexo/node_modules/js-yaml/lib/js-yaml/loader.js:162:10)
     at throwError (/Users/admin/PhpstormProjects/test/BonitaOnTheWay/tryHexo/node_modules/hexo/node_modules/js-yaml/lib/js-yaml/loader.js:168:9)
     at readBlockMapping (/Users/admin/PhpstormProjects/test/BonitaOnTheWay/tryHexo/node_modules/hexo/node_modules/js-yaml/lib/js-yaml/loader.js:1040:9)
     at composeNode (/Users/admin/PhpstormProjects/test/BonitaOnTheWay
 ```
 ### 解决问题
 > _config.yml的每个属性配置时，:后边一定要有一个空格
 > 找到报错的行，改掉
 > 之前language设置不了就是因为报错了，完全没有编译成功，我竟然没发现，真是够了
