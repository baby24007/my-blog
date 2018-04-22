---
title: getter 和 setter 方法有什么意义？
date: 2018-04-14 17:12:21
tags:
---

今天在看vue中使用axios的时候发现，axios提供了vue的版本。但是之前看了使用axios的一般做法是这样的，
```
import axios from 'axios'
Vue.prototype.$ajax = axios
```

那么为什么还需要vue-axios呢？就要看看他的源码了。代码非常短
```
(function () {

/**
 * Install plugin
 * @param Vue
 * @param axios
 */

function plugin(Vue, axios) {

  if (plugin.installed) {
    return
  }
  plugin.installed = true

  if (!axios) {
    console.error('You have to install axios')
    return
  }

  Vue.axios = axios

  Object.defineProperties(Vue.prototype, {

    axios: {
      get() {
        return axios
      }
    },

    $http: {
      get() {
        return axios
      }
    }

  })
}

if (typeof exports == "object") {
  module.exports = plugin
} else if (typeof define == "function" && define.amd) {
  define([], function(){ return plugin })
} else if (window.Vue && window.axios) {
  Vue.use(plugin, window.axios)
}

})();
```

输出不同类型的模块和挂载vue上的东东先不说，这个使用Object.defineProperties的getter的方式让我有些疑惑，最开始那种直接写入prototype的方式多简洁，为什么还要费劲吧啦的写个get呢？


> 使用“方法”来表达对象的能力，同使用“变量”相比，从宏观上来说，没有什么区别，只是形式上的不同。但使用“方法”来表达接口，更容易体现OOP的一个核心理念“隐藏细节”。    [@Levski](http://levskiweng.me)

下面直接上一个[stackoverflow](https://stackoverflow.com/questions/1568091/why-use-getters-and-setters-accessors)上的答案，

> There are actually many good reasons to consider using accessors rather than directly exposing fields of a class - beyond just the argument of encapsulation and making future changes easier.
  
  Here are the some of the reasons I am aware of:
  
  - Encapsulation of behavior associated with getting or setting the property - this allows additional functionality (like validation) to be added more easily later.
  方便增加额外功能（比如验证）
  - Hiding the internal representation of the property while exposing a property using an alternative representation.
  Insulating your public interface from change - allowing the public interface to remain constant while the implementation changes without affecting existing consumers.
  可以保持外部接口不变的情况下，修改内部存储方式和逻辑
  - Controlling the lifetime and memory management (disposal) semantics of the property - particularly important in non-managed memory environments (like C++ or Objective-C).
  任意管理变量的生命周期和内存存储方式
  - Providing a debugging interception point for when a property changes at runtime - debugging when and where a property changed to a particular value can be quite difficult without this in some languages.
  可以打断点
  - Improved interoperability with libraries that are designed to operate against property getter/setters - Mocking, Serialization, and WPF come to mind.
  能够和模拟对象、序列化乃至WPF库等融合
  - Allowing inheritors to change the semantics of how the property behaves and is exposed by overriding the getter/setter methods.
  允许继承者通过重写getter/setter改变语义和实现方式
  - Allowing the getter/setter to be passed around as lambda expressions rather than values.
  可以将getter、setter用于lambda表达式
  - Getters and setters can allow different access levels - for example the get may be public, but the set could be protected.
  允许getter/setter有不同级别的访问。比如get可以对外，set就是私有的
