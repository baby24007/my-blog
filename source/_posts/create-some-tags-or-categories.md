---
title: create some tags or categories
date: 2016-05-31 11:04:58
tags:
- Front-matter
- YAML
---
## 如何正确的添加tags和categories
在.md文件上面的那一部分内容是Front-matter,用于指定个别文件的变量.
**需要注意**
1. 必须在最前面:must be the first thing in the file
2. 必须要三条杠!三条杠=.=  醉了:must take the form of valid YAML set between triple-dashed lines
3. UTF-8编码的时候不能有BOM:make sure that no BOM header characters exist in your files 
书归正传:
```bash
categories:
- Diary
tags:
- PS3
- Games
```
在Hexo中其实还可以用json格式:
>JSON Front-matter,除了 YAML 外，你也可以使用 JSON 来编写 Front-matter，只要将 --- 代换成 ;;; 即可。
```bash
"title": "Hello World",
"date": "2013/7/13 20:46:25"
;;;
```

## 参考文档
[Front-matter的文档](http://jekyllrb.com/docs/frontmatter/)
[YAML-Lists](https://en.wikipedia.org/wiki/YAML#Lists)
[在Hexo中使用front-matter](https://hexo.io/zh-cn/docs/front-matter.html#分类和标签)
[Hexo文档还没有一个主题配置文档写得详细,真是够了=.=](http://theme-next.iissnan.com/theme-settings.html#duoshuo-ua)