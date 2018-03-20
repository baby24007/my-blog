---
title: Professional JavaScript for Web Developers learning note
date: 2016-06-21 15:12:03
tags: 
- note
- 学习js
---
## 单体内置对象
### Global对象
#### URI编码方法
```javascript
//对URI进行编码
encodeURI()         //主要用于整个uir；不对特殊字符进行编码
encodeURIComponent()    //主要针对URI中的某一段；对特殊字符进行编码

decodeURI()
decodeURIComponent()
```
能够编码所有的Unicode字符

#### eval()
```javascript
eval('('+json+')');//I used to do that;解析json字符串；等同于$.parseJSON
```
#### Global对象属性
```undefined、NaN、Infinity
   Object、Array、Function、Boolean、String、Number
   Date、RegExp、Error、EvalError...
```
ES5明确禁止给undefined、NaN、Infinity赋值
#### windows对象（略）
#### Math对象
##### min()&max()
##### ceil()
    向上舍入
##### floor()
    向下舍入
##### round()
    标准舍入
##### random()
    返回0-1的随机数
    常用公式：
```
//值 = Math.floor(Math.random()*可能值的总数+第一个可能的值)
value  = Math.floor(Math.random()*amount+from);
```
### 只言片语
创建自定义类型，组合使用构造函数&原型模式：
**构造模式用于定义实例属性，原型模式用户定义方法和共享的属性**
**对原型进行的修改将在所有实例中得到反映**