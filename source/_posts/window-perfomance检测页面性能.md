---
title: window.perfomance检测页面性能
date: 2016-07-01 10:37:31
tags: 
- es5
- 学习js
---
```javascript
var performanceReport = function(){
    var host = 'www.xxx.com',
        protocol = window.location.href.match(/^http(s)?:\/\//g) || "http://",
        reportUrl = protocol + host + '/analysis.jpg',
        doc = document;
    var getTimingInfo = function(){
        var performance = window.performance;
        var t = performance.timing || {};
        //performance.measure('domReady','responseEnd' , 'domComplete');
        var time =  {
            lookupDomain : t.domainLookupEnd - t.domainLookupStart,
            //t.loadEventEnd - t.navigationStart,那么采用埋点方式的话直接使用performance.now()的时间即可喽
            //performance.now()输出的是相对于 performance.timing.navigationStart(页面初始化)
            loadPage : performance.now(),
            domReady : t.domComplete - t.responseEnd,
            ttfb : t.responseStart - t.navigationStart,
            request : t.responseEnd - t.requestStart,
            appcache : t.domainLookupStart - t.fetchStart,
            connect : t.connectEnd - t.connectStart
        };
        return time;
    }
    var composeParam = function(item){
        var param = [];
        for(var k in item){
            var l = k+'\='+ item[k];
            param.push(l);
        }
        return param.join('&');
    }
    var p = $.extend({},getTimingInfo(),{
        referrer : encodeURIComponent(doc.referrer) || '',
        request_uri : encodeURIComponent(window.location.href) //是要这个么？
    })
    var  getEntryTiming = function(entry) {
        var t = entry;
        var times = {};
        times.redirect = t.redirectEnd - t.redirectStart;
        times.lookupDomain = t.domainLookupEnd - t.domainLookupStart;
        times.request = t.responseEnd - t.requestStart;
        times.connect = t.connectEnd - t.connectStart;
        times.name = entry.name;
        times.entryType = entry.entryType;
        times.initiatorType = entry.initiatorType;
        times.duration = entry.duration;
        return times;
    }
    var entries = window.performance.getEntries();
    entries.forEach(function (entry) {
         var times = getEntryTiming(entry);
         console.log(times);
    });
    var i = new Image();
    //a.type = "text/javascript";
    //a.async = !0;
    i.src = reportUrl + '?' + composeParam(p);
    doc.getElementsByTagName("body")[0].appendChild(i);
    //http://logcenter.ingageapp.com/analysis.jpg?lookupDomain=0&loadPage=6425&domReady=4341&ttfb=2064&request=2055&appcache=8&connect=3&referrer=http://crm-dev10.xiaoshouyi.com/global/login.action&request_uri=http%3A%2F%2Fcrm-dev10.xiaoshouyi.com%2Findex.action
}

//执行的时候放在onload里加个异步就好喽，不然domComplete & loadEventEnd都是0
window.addEventListener('load',function(){
    setTimeout(rk.performanceReport,1);
})
```
* 参考文章:
> [TAT.felix初探 performance – 监控网页与程序性能](http://www.alloyteam.com/2015/09/explore-performance/)
> [Performance API](http://javascript.ruanyifeng.com/bom/performance.html)
> [Measuring Page Load Speed with Navigation Timing](http://www.html5rocks.com/en/tutorials/webperformance/basics/)
> [window.performance 详解](https://github.com/fredshare/blog/issues/5])

