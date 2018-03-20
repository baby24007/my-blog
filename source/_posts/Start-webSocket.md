---
title: Start webSocket
date: 2016-07-08 18:26:43
tags:
---
## 现在系统采用的是comet长链接，要修改为websocket实现通信
```javascript
/**
 * 代替comet
 * 相关点：feed,newnotice,remind,at,announcement,notice,privatemsg
 */
define(function (require, exports, module) {
    'use strict';
    var regId = 1,
        handlerPoll = [];
//注册事件的部分还可以用,要不要改名字啊？
    $.registLongPulling = function(fn) {
        var $body = $('body:first');
        if (!$body.data(':data(rk-websocket)')) {
            $body.websocket({
                message: function(evt, msg) {
                    if (msg != 'timeout') {
                        if(msg && (typeof msg !== 'Object')){
                            msg = $.parseJSON(msg);
                        }
                        try {
                            for (var i = 0; i < handlerPoll.length; i++) {
                                handlerPoll[i].call(msg, msg);
                            }
                        } catch (ex) {
                            window.console && console.log('the callback of long pulling error: ' + String(handlerPoll[i]));
                        }
                    }
                }
            });
        }
        if ($.isFunction(fn)) {
            fn.rid = regId;
            regId += 1;
            handlerPoll.push(fn);
            return fn.rid;
        }
        return 0;
    };
    $.unregistLongPulling = function(fn) {
        if ($.isFunction(fn) && fn.rid) {
            for (var i = handlerPoll.length - 1; i > -1; i--) {
                if (handlerPoll[i].rid == fn.rid) {
                    handlerPoll.splice(i, 1);
                }
            }
        }
    };

    $.widget('rk.websocket', {
        options: {
            clientID: SESSION.cometClientId,
            userID: SESSION.user.id,
            message: $.noop,
            url:'wss://192.168.0.103:8989/ws?'//getHostName
        },
        _create: function(){
            var me = this;

            me._delay(function() {
                me.init();
            }, 10 * 1000);
        },
        init:function(){
            var me = this,
                opt = me.options;
            var url = [opt.url,'clientId=', opt.clientID,  '&alias=', opt.userID].join('');
            var browserWebSocket = window.WebSocket || window.MozWebSocket;
            var webSocket = browserWebSocket;
            if (webSocket && !me.connection) {
                try {
                    me.connection = new WebSocket(url);
                    me._bind();
                } catch (e) {
                    console.error('没有websocket怎么办')
                }
            }
        },
        _bind:function(){
            var me = this;
            var connection = me.connection;
            if(connection){
                connection.onopen = function() {
                    me.onOpen()
                };
                connection.onclose = function() {
                    me.onClose()
                };
                connection.onmessage = function(ev) {
                    console.log(ev)
                    me._trigger('message', null, ev.data);
                };
                connection.onerror = function(e) {
                    me.onError("websocket error", e)
                }
            }
        },
        onOpen:function(){
            console.info('websocket open');
        },
        onError:function(msg,e){
            console.info(msg);
        },
        onClose:function(){
            console.info('websocket closed');
            //this._destroy();
        },
        getHostName:function(){}
    });
});
```
## [戳这里－>comet实现的流程图](https://www.processon.com/view/link/577e06f7e4b04bc7eebd4973)
## 改成websocket以后的流程图
![改成websocket以后的流程图](http://thumbnail0.baidupcs.com/thumbnail/acad81fc97afa98e8747c8b6c7295909?fid=3422552496-250528-953740440445751&time=1467972000&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-HHs8YVzenPzlFkr5qJ%2BymQ24bI0%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=4401564112992226463&dp-callid=0&size=c710_u400&quality=100)
> 就是这么懒，可怎么整

## 参考文章
> [socket.io源码](https://cdn.socket.io/socket.io-1.4.5.js)
> [使用 HTML5 WebSocket 构建实时 Web 应用](http://www.ibm.com/developerworks/cn/web/1112_huangxa_websocket/index.html)
> [MDN - WebSocket](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket)

