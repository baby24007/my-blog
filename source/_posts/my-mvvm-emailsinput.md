---
title: my-mvvm-emailsinput
date: 2018-06-08 22:15:20
tags:
- mvvm
- jqueryUI
---
> 一个mvvm的简单实现
# 简单思路
## 想要完成的控件效果
- 输入框里输入一个email地址，输入正确的话回车时候将输入的email添加'；'后显示在div里；输入错误的话就提示输入错误
- 点删除的时候高亮前一条email，第二次点击删除的时候再删除前一整条数据；
- 双击某条email可以直接编辑，编辑后点击回车或者input blur的时候保存编辑后的结果并直接render在div里；

## 如何利用Object.defineProperty来借助一个object对象实现email的list数组与UI直接同步
- 希望每次输入完email可以直接通过 `inputModel.newVal = value` 的方式来添加一条数据
- 希望编辑的以后直接根据现有的list来render html

> 那么我可以借用一个object对象，设置其newVal的setter来达到目的，并且在add之后执行一个回调方法来render新的html

```javascript
var inputModel = {
    list: [],  //这里用来保存所有的email
    ...
}
// 这里为其增加一个newVal，在setter的时候将新数据push到list，这样每设定一个新值的时候，都会被推入list
Object.defineProperties(inputModel, {
            newVal: {
                set: function(value){
                    this.add(value);
                },
                get: function(){
                    console.log('get new val')
                    return this.newVal;
                }
            }})
```

然后在输入字符的时候，点击回车，只要执行
`inputModel.newVal = value;`
就添加了一条新数据。

当然，实现方式有很多种，目前只是想试用Object.defineProperty，所以选取了这种方式。还可以直接给element直接Object.defineProperties，比如：

```html
...html 
<input type="text" id="inputModel">
<p id='display'>111</p>
```

```javascript
...js
var inputModel = document.getElementById('inputModel');
var display = document.getElementById('display');
Object.defineProperty(display,'width',{
    set: function(value){
      display.style.width = value + 'px';
    }
  })

inputModel.addEventListener('input',function(){
    // 输入的数字直接可以控制p的width
  display.width = this.value
},false)
```

```css
...css
#display{
  height:30px;
  background:red;
}
```

# 完整代码
## js文件

```javascript
 require('./emailsInputPlus.css');
    /**
     * 服务邮件的收件人的input
     * 支持多邮件地址输入
     * 可对整条email地址进行删除
     * 当输入完整的邮箱后，按回车键，自动显示“；”
     */
    ;
    (function(){
        var inputModel = {
            list: [],
            add: function(value){
                console.log('add new val', value)
                this.list.push(value);
                $.isFunction(this.onAdd) && this.onAdd.call(null, value);
            },
            replace: function(index, value){
                if(typeof value === 'undefined'){
                    this.list.splice(index, 1);
                }else{
                    this.list.splice(index, 1, value);
                }
                $.isFunction(this.onReplace) && this.onReplace.call(null, index, value);
            },
            delete: function(index){
                if(typeof index === 'undefined'){
                    index = this.list.length - 1;
                }
                this.replace(index);
            },
            onAdd: null,
            onReplace: null
        };
        Object.defineProperties(inputModel, {
            newVal: {
                set: function(value){
                    this.add(value);
                },
                get: function(){
                    console.log('get new val')
                    return this.newVal;
                }
            }})
        $.widget('rk.emailsInputPlus',{
            options:{
                maxLength: 9999 ,//支持输入多少个email
                style:null, //想改输入框样式就传style进来
                validataReg: /^[\w-|\u4E00-\u9FA5]+(\.[\w-|\u4E00-\u9FA5]+)*@([\w-|\u4E00-\u9FA5]+)(\.[\w-|\u4E00-\u9FA5]+)+(;){0,1}$/,
                originalList: []
            },
            _create: function(){
                var me = this;
                var styles = this.options.style;
                me.render();
                me.bindEvent();
                if(styles){
                    if($.isPlainObject(styles)){
                        this.element.css(styles)
                    }
                }
            },
            bindEvent: function(){
                var me = this;
                // var $input = me.element.find('#newEmailVal');
                var _onAdd = function(){
                    this.render();
                    this.element.find('input.add')[0].focus();
                };
                var _onReplace = function(index, value){
                    //只是删除的话就重新渲染
                    if(!value){
                        _onAdd.call(me);
                    };
                    var _renderSingleLi = function(oLi){
                        oLi.find('span').text(value);
                        oLi.find('input').val(value);
                        oLi = null;
                    }
                    _renderSingleLi(this.element.find('li.item').not('.disabled').eq(index), value);
                }
                inputModel.onAdd = _onAdd.bind(me);
                inputModel.onReplace = _onReplace.bind(me);

                me.element.on('keydown','input', function(ev){
                    if(ev.keyCode === 13){
                       $(this).blur();
                    }
                    // deleteItem
                    if(ev.keyCode === 8){
                        if(this.value)return;
                        if(me.element.find('li:last').hasClass('active')){
                            me.deleteItem();
                        }else{
                            me.heightLightItem();
                        }
                    }
                }).
                on('blur','.valInput', function(ev){
                    var act = $(this).attr('name'),
                        index = $(this).attr('index'),
                        value = this.value;
                    if(value === '')return;
                    value = me.formatVal(value);
                    if(!value){
                        this.focus();
                        return rk.noticeError('收件人邮箱格式错误，请重新填写');
                    }
                    // 编辑或者添加email
                    // modifyItem or addItem
                    if($.isFunction(me[act])){
                        me[act](value, index);
                    }
                    me.getValue();
                    $(this).closest('.editing').removeClass('editing');
                })
                .on('dblclick','li.item',function(ev){
                    me._triggerEditingMode(this);
                });
            },
            _triggerEditingMode: function(el){
                //变成编辑框，计算一下input的width
                var el = $(el);
                if(el.hasClass('editing'))return;
                el.find('input').width(el.width() + 10);
                el.addClass('editing');
            },
            /* 每次数据变化都render会不会很消耗性能？ */
            render: function(){
                var emailList = inputModel.list;
                var tpl = rk.templateText(require('./emailsInputPlus.tpl'), {items: emailList, originalList:this.options.originalList});
                this.element.html(tpl);
            },
            addItem: function(value){
                inputModel.newVal = value;
            },
            deleteItem: function(index){
                var last = this.element.find('li:last');
                if(last.hasClass('active')){
                    inputModel.delete();
                }
            },
            modifyItem: function(value, index){
                inputModel.replace(index, value);
            },
            heightLightItem: function(index){
                var el = index ? this.element.find('li').eq(index) : this.element.find('li:last');
                el.addClass('active');
            },
            getValue: function(){
                console.log(inputModel.list.join(''))
            },
            formatVal: function(value){
                //可以有个;,有了;就不再添加了，没有就添加一个然后返回
                var validate = this.options.validataReg.exec($.trim(value));
                if(validate === null){
                    return false;
                }
                if(typeof validate[4] === 'undefined'){
                    value += ';';
                }
                return value;
            }
        })
    })()
```

## 引用的tpl
```html
<div class="emailsInputPlus">
    <ul>
        {{if originalList.length}}
            {{each originalList as item}}
            <li class="item disabled">{{item}}</li>
            {{/each}}
        {{/if}}
        {{each items as item}}
        <li class="item">
            <span>{{item}}</span>
            <input type="text" name="modifyItem" class="valInput modify" value="{{item}}" index={{$index}}></li>
        {{/each}}
    </ul>
    <input type="text" name="addItem" class="valInput add">
</div>
```

## css
```css
.emailsInputPlus{
    background: none repeat scroll 0 0 #FFFFFF;
    border: 1px solid #DDDDDD;
    border-radius: 3px;
    min-height: 30px;
    line-height: 30px;
    padding-left: 5px;
    width: 253px;
    overflow: hidden;
}
.emailsInputPlus .item{
    float: left;
    margin-right: 10px;
    cursor: pointer;
}
.emailsInputPlus .item.active{
    background-color: aliceblue;
    border-radius: 3px;
}

.emailsInputPlus .item span,
.emailsInputPlus .item.editing input{
    display: inline;
}
.emailsInputPlus .item input,
.emailsInputPlus .item.editing span{
    display: none;
}
.emailsInputPlus .valInput{
    float: left;
    border: 1px solid #ccc;
    /* border: none; */
    line-height: 28px;
    display: inline-block;
    background-color: transparent;
    
}
```