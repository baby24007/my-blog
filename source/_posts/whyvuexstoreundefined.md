---
title: 引入vuex以后，子组件里调用$store的时候始终是undefined
date: 2018-04-22 23:32:29
tags: vuex
----------

最近在学习vue的时候，加入了vuex，在store里创建了index.js,分别引入了module化的store，然后在main.js里引入了store/index.js，引入成功，但是在子组件里调用this.$store的时候一直都是undefined，代码分别如下：
- store/index/js
``` javascript
import Vue from 'Vue'
import Vuex from 'Vuex'
import rm from './modules/rm'
Vue.use(Vuex)

export default new Vuex.Store({
  modules: {
    rm
  }
})
```

- main.js

``` javascript
// The Vue build version to load with the `import` command
// (runtime-only or standalone) has been set in webpack.base.conf with an alias.
import Vue from 'vue'
import store from './store'
import router from './router'
import App from './App'
import Loading from './components/Loading'

let rm = new Vue({
  el: '#app',
  router,
  store,
  template: '<App/>',
  // 这里一定是components，之前写成component，一直就报错了
  components: {App}
})
console.log(router)
console.log(store)
...
```

然后，不管再任何地方调用this.$store都是undefined，在我查了数个小时，
先是怀疑语法问题，看console并没有报错；
然后怀疑调用顺序的问题，而后也被排除；
然后看是不是忘记用Vue.use 挂载了，挂了啊；
然后看是不是在生命钩子以外的地方调用了this所以没有 $store，检查了都是created、mounted里调的，没有问题啊；
感觉像是store没有成功挂载到vue的实例上，可是明明用use挂载了呀
………
baidu google均找不到答案的时候，我一度都要怀疑人生了，直觉告诉我这一定是个非常低级的错误，直到我在无数次的翻看store/index.js的时候发现有那么一丝怪异，

``` javascript
import Vue from 'Vue'
import Vuex from 'Vuex'
```

。。。。。
。。。。
。。。
我懂了。
为什么自己手贱敲了个大写！！！！！！
两个多小时的宝贵人生因此而浪费了。。
这件事情给了我一个教训：
> 看孩子就好好看孩子，写毛代码