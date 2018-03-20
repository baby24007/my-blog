---
title: Working Temporary
date: 2016-06-09 11:21:22
tags:
---
## Ctrl
```javascript
/**
 * Created by admin on 16/6/3.
 * 用途：
 *  获取组合查询条件；
 *  加载概要数据；
 *  调用相应组件绘制图形；
 */
define(function (require, exports, module) {
    'use strict';    
    var rk = require('rk');
    //temp
    var urlList = {
        getInfo:'get-statistics-status.action',
        getChart:'get-statistics-chart.action'
    }
    require('./systemStatisticsChartCtrl');
    require('./systemStatisticsPieCtrl');
    require('./systemStatisticsGridCtrl');

    //
    $.widget('rk.systemStatisticsCtrl', {
        options: {
            //默认显示week&logged面板
            defaultParam:{
                view:'w',
                vtype:'logged'
            }
        },
        _chartContainer:null,
        _gridContainer:null,
        _create: function () {
            var me = this;
            var elem = me.element;
            var statisticsTpl = require('page/tmpl/statistics/tpl_statistics_ctrl.tpl'),
                statisticsHtml = rk.templateText(statisticsTpl);
            elem.html(statisticsHtml);
        },
        _init: function () {
            var me = this;
            var elem = me.element;
            var opt = me.options;


            me._chartContainer = elem.find('.js-chart');
            me._gridContainer = elem.find('.js-grid');

            me._chartContainer.height(elem.height()-280);
            me._bind();
            me._loadHeaderData();
        },
        _bind:function(){
            var me = this;
            var elem = me.element;
            var opt = me.options;
            var timer;
            //切换 周／月
            elem.on('click','.js-stati-view-type > li',function(){
                var that = $(this);
                if(me._isAble(that)){
                    console.log(that.attr('view-type'))
                    me._loadHeaderData({
                        view:that.attr('view-type')
                    });
                }
            })
            //切换 已登陆／未登录／行为总量
            elem.on('click','.js-stati-status-type > li',function(){
                var that = $(this);
                var chart = elem.find('.js-chart');

                if(me._isAble(that)){
                    //me.destroyChart();//好像不需要destroy
                    var type = that.attr('status-type');
                    if(type == 'logged'){
                        me._getLoggedData();
                    }
                    if(type == 'unlogged'){
                        me.hideChart();
                        me._getlUnloggedData();
                    }
                    if(type == 'total'){
                        me._getTotalData();
                    }
                }
            })
            me.onResize();
            $(window).resize(function(){
                clearTimeout(timer);
                timer = me._delay(function(){
                    me.onResize();
                },100)
            })
        },
        _isAble:function(obj){
            if(obj.hasClass('active')){
                return false;
            }else{
                obj.siblings('li').removeClass('active');
                obj.addClass('active');
                return true;
            }
        },
        //切换时间纬度的时候更新数据
        renderHeaderData:function(data){
            var me = this,
                elem = me.element,
                statusTypeLi = rk.templateText(require('page/tmpl/statistics/tpl_statistics_status_type_li.tpl'),data);
            elem.find('.js-stati-title').html(data.title);
            elem.find('.js-stati-status-type').html(statusTypeLi);
        },
        _loadHeaderData:function(addition){
            var me = this,
                elem = me.element;
            var param = me._getCondition(addition);
            var url = '';
            if(param.view == 'w'){
                var data = require('./status.json');
            }else{
                var data = require('./status_month.json');
            }

            me.renderHeaderData(data.data);
            elem.find('.js-stati-header').removeClass('loading-mask');
            //默认加载第一个
            elem.find('li[status-type="'+me.options.defaultParam.vtype+'"]').trigger('click');
           /* $.getJSON(url, param, function(json) {

            });*/
        },
        _getLoggedData:function(){
            var me = this,
                elem = me.element;
            var chart = me._chartContainer;
            var data = require('./chart.json');
            var condition = me._getCondition();
            elem.find('.js-stati-total-view-type').hide();
            console.log(chart.is( ":data('rk-systemStatisticsChartCtrl')" ))
            chart.systemStatisticsChartCtrl({
                customData:data.data,
                categoriesType:condition.view
            });
        },
        _getlUnloggedData:function(){
            var me = this,
                elem = me.element;
            var grid = me._gridContainer;

            var data = require('./grid.json');
            console.log(elem.width())
            grid.systemStatisticsGridCtrl({customData:data.data});
        },
        _getTotalData:function(){
            var me = this,
                elem = me.element;
            var chart = me._chartContainer;
            elem.find('.js-stati-total-view-type').removeClass('refer-con-unloading').fadeIn();
            //if(chart.is( ":data('rk-systemStatisticsPieCtrl')" )){
                chart.systemStatisticsPieCtrl();
            //}else{
                //chart.systemStatisticsPieCtrl('reload',data);
            //}
            //var data = require('./pie.json');
            //console.log(data)
        },
        _getCondition:function(addition){
            var opt = this.options;
            var param = opt.defaultParam;
            //或者增加一些其他的查询参数
            if(addition){
                param = $.extend({},param,addition);
            }
            return param;
        },
        hideChart:function(){
            this._chartContainer.hide();
        },
        showNoDate:function(){

        },
        destroyChart:function(){
            var me = this,
                elem = me.element;
            var chart = me._chartContainer;

            //console.log(chart.is(':data("ui-highcharts")'))
            //console.log(chart.highcharts('instance'))
            try{
                console.log('b-----'+chart.highcharts())
                chart.highcharts('destroy');
                console.log('a-----'+chart.highcharts())
            }catch(e){}
        },
        destroyGrid:function(){

        },
        onResize:function(){
            //看似没用却有用，神马情况？性能不是太好，要改
            var me = this,
                elem = me.element;
            var chart = me._chartContainer.highcharts();

            if(chart){
                var h = elem.outerHeight()-280;
                h = h>300?h:300;
                chart.setSize(elem.width(),h);
                console.log(h)
                //console.log(elem.outerHeight())
            }
        }
    });

    return $;
});
```
## Pie
```javascript
/**
 * Created by admin on 16/6/3.
 * Pie型图表
 * 行为总量的饼
 */
define(function (require, exports, module) {
    'use strict';

    //
    $.widget('rk.systemStatisticsPieCtrl', {
        options: {},
        _create: function () {
            var me = this;
            //var elem = me.element;

        },
        _init: function () {
            var me = this;
            var elem = me.element;
            me._draw();
            elem.show();
        },
        _draw:function(){
            var me = this,
                elem = me.element,
                opt = me.options;
            var data = me._composeData();
            // Build the chart
            this.element.highcharts({
                chart: {
                    plotBackgroundColor: null,
                    plotBorderWidth: null,
                    plotShadow: false,
                    marginTop:30
                },
                title: {
                    text: null
                },
                tooltip: {
                    pointFormat: '{series.name}: <b>{point.percentage:.1f}%</b>'
                },
                plotOptions: {
                    pie: {
                        allowPointSelect: true,
                        cursor: 'pointer',
                        dataLabels: {
                            enabled: true,
                            format: '<b>{point.name}</b>: {point.percentage:.1f} %',
                            style: {
                                color: (Highcharts.theme && Highcharts.theme.contrastTextColor) || 'black'
                            }
                        }
                    }
                },
                series: [{
                    type: 'pie',
                    data: [
                        {name: '办公', y: 45.0, color:'#3399ff'},
                        {name:'订单',y:16.8,color:'#33bbff'},
                        {name: '合同', y: 12.8,color:'#ff7559'
                          //sliced: true,
                            //selected: true
                        },
                        {name: '销售机会', y: 12.8,color:'#1fc695'},
                        {name:'活动记录',y:6.2,color:'#ffba59'},
                        {name:'联系人',  y: 10.7,color:'#9580ff'}
                    ]

                }]
            });
        },
        _composeData:function(){
            var me = this,
                opt = me.options,
                data = opt.customData;
            //这里组成图表需要的data格式

        },
        reload:function(data){
            var me = this,
                opt = me.options;
            me.options.customData = $.extend({},opt.customData,data);
            me._draw();
        }
    })
});

```
## Area Chart
```javascript
/**
 * Created by admin on 16/6/3.
 * Area型图表
 * 已登录的块
 */
define(function (require, exports, module) {
    'use strict';

    //
    $.widget('rk.systemStatisticsChartCtrl', {
        options: {
            customData:{},
            //categoriesType:''
        },
        _create: function () {
            var me = this;
            var elem = me.element;
            var opt = me.options;
        },
        _init: function () {
            var me = this;
            var elem = me.element;
            var opt = me.options;
            if(_.isEmpty(opt.customData)){
                me._loadData();
            }else{
                me._draw(opt.customData);
            }
            elem.show();
        },
        _draw:function(data){
            var me = this,
                elem = me.element;
            elem.highcharts({
                chart: {
                    type: 'area',
                    margin:[50,50,50,80],
                    spacingLeft:80,
                    reflow:true
                },
                title:{
                    text: null
                },
                xAxis: {
                    tickInterval:1,
                    startOnTick:false,
                    categories: ['周一','周二','周三','周四','周五','周六','周日']
                },
                yAxis: {
                    title: {
                        text: null
                    },
                    labels: {
                        formatter: function () {
                            return this.value
                        }
                    }
                },
                tooltip: {
                    crosshairs:{
                        width:.1,
                        color:'#3e4459',
                        dashStyle:'solid'
                    },
                    formatter:function(){
                        var that  = this;
                        var tplHtml = '<span>';
                        if(that.series.data.length > 7){
                            tplHtml += '上月'+that.x+'日：'+that.point.before+'人';
                            tplHtml += '<em>'+that.point.nowRange+'</em>';
                            tplHtml += '<br>';
                            tplHtml += '本月'+that.x+'日：'+that.point.now+'人';
                            tplHtml += '<em>'+that.point.beforeRange+'</em>';
                        }else{
                            tplHtml += '上'+that.x+'：'+that.point.before+'人';
                            tplHtml += '<em>'+that.point.nowRange+'</em>';
                            tplHtml += '<br>';
                            if(that.point.now){
                                tplHtml += '本'+that.x+'：'+that.point.now+'人';
                                tplHtml += '<em>'+that.point.beforeRange+'</em></span>';
                            }


                        }
                        tplHtml += '</span>';

                        return tplHtml;
                    },
                    followPointer:true    //tips框跟随鼠标位置
                },
                series: [{
                    name: '上次周期',
                    data: [{
                        y:12331,now:12331,before:2344,nowRange:'30%',beforeRange:'20%',color:'#00aaef'
                    },{
                        y:3322,now:3322,before:321,nowRange:'-30%',beforeRange:'20%'
                    },{
                        y:432, now:432,before:6784,nowRange:'30%',beforeRange:'20%'
                    },{
                        y:24323, now:24323,before:7897,nowRange:'60%',beforeRange:'20%'
                    },{
                        y:2432, now:2432,before:1321,nowRange:'30%',beforeRange:'20%'
                    },{
                        y:234, now:234,before:352,nowRange:'30%',beforeRange:'20%'
                    },{
                        y:33, now:'',before:555,nowRange:'30%',beforeRange:'20%'
                    }],
                    color:'#b2d9ff',
                    lineColor:'#b2d9ff',
                    lineWidth:2
                },{
                    name: '本次周期',
                    data: [{
                        y:2344,now:12331,before:2344,nowRange:'30%',beforeRange:'20%'
                    },{
                        y:321,now:3322,before:321,nowRange:'-30%',beforeRange:'20%'
                    },{
                        y:6784, now:432,before:6784,nowRange:'30%',beforeRange:'20%'
                    },{
                        y:7897, now:24323,before:7897,nowRange:'60%',beforeRange:'20%'
                    },{
                        y:1321, now:2432,before:1321,nowRange:'30%',beforeRange:'20%'
                    },{
                        y:352, now:234,before:352,nowRange:'30%',beforeRange:'20%'
                    }],
                    color:'#3399ff',
                    lineColor:'#3399ff',
                    lineWidth:2
                }],
                legend:{
                    align:'left',
                    verticalAlign:'top',
                    borderWidth: 0,
                    floating: true
                },
                plotOptions:{
                    series:{
                        marker:{
                            radius:0
                        }
                    }
                }
            })
        },
        _composeData:function(){
            var me = this,
                opt = me.options,
                data = opt.customData;
            //这里组成图表需要的data格式

        },
        reload:function(data){
            var me = this,
                opt = me.options;
            me.options.customData = $.extend({},opt.customData,data);
            me._draw();
        }/*,
        showNoData:function(){
            this.element.html('空的')
        }*/
    })
});

```
## Grid
```javascript
/**
 * Created by admin on 16/6/3.
 * Grid表格
 * 未登录的表&行为总量的表
 */
define(function (require, exports, module) {
    'use strict';

    //
    $.widget('rk.systemStatisticsGridCtrl', {
        options: {
            customData:{},
        },
        //source:{},
        //datafields:[],
        //columns:[],
        _create: function () {
            var me = this;
            var elem = me.element;
            var opt = me.options;

        },
        _init: function () {
            var me = this;
            var elem = me.element;
            var opt = me.options;
            me._draw();
        },
        _draw:function(){
            var me = this,
                elem = me.element,
                opt = me.options;
            var source = me._getSourceData(),
                dataAdapter = new $.jqx.dataAdapter(source);
            //console.log($('.container_content').outerWidth())
            var pagerrenderer = function(){
                //var elem = me.element;
                var element = $("<div style='margin-top: 5px; width: 100%; height: 100%;'></div>");
                var paginginfo = elem.jqxGrid('getpaginginformation');
                for (var i = 0; i < paginginfo.pagescount; i++) {
                    // add anchor tag with the page number for each page.
                    var anchor = $("<a style='padding: 5px;' href='#" + i + "'>" + i + "</a>");
                    anchor.appendTo(element);
                    anchor.click(function (event) {
                        // go to a page.
                        var pagenum = parseInt($(event.target).text());
                        elem.jqxGrid('gotopage', pagenum);
                    });
                }
                console.log(element.html())
                return element;
            }
            elem.jqxGrid({
                width:elem.width(),
                source:dataAdapter,
                columnsresize:true,
                rowsheight: 40,
                columnsautoresize: false,
                scrollbarsize: 8,
                pageable: true,
                //autoheight:true,
                pagerrenderer: pagerrenderer,
                pagerheight: 50,
                columns:me._getColumns(),
                rendered:function(){
                    elem.find('[role="row"]').addClass('jqx_grid_row');
                }
            });

            //elem.jqxGrid(gridOptions);
        },
        _getSourceData:function(){
            var me = this;
            var elem = me.element;
            var opt = me.options;
            var columnsInfo = opt.customData.columnsInfo;
            var datafields = $.map(columnsInfo,function(o){
                var type = o.itemPropertyName == 'lastTime'?'date':'string';

                return {
                    name: o.itemPropertyName,
                    type: type
                }
            })
            return {
                datatype:'json',
                datafields:datafields,
                localdata:opt.customData.groups,
                pagenum: 1,
                pagesize: 10
            }
        },
        //姓名 | 部门 | 职位 | 上次登陆时间
        _getColumns:function(){
            var me = this,
                opt = me.options,
                data = opt.customData;
            return $.map(data.columnsInfo,function(o){
                return {
                    text: o.itemName,
                    datafield: o.itemPropertyName,
                    width:'auto',//先auto看一看,
                    renderer: function(text, align, height) {
                        return '<div class="jqx_grid_header_td">' + text + '</div>';
                    },
                    cellsrenderer: function(row, column, value, elem, config, data) {
                        return '<div class="jqx_grid_td">' + $.htmlEncode(value) + '</div>';
                    },
                    cellsformat: o.itemPropertyName == 'lastTime' ? "D" : null  //报错？为毛
                }
            });
        },
        _composeData:function(){
            var me = this,
                opt = me.options,
                data = opt.customData;
            //这里组成图表需要的data格式

        },
        reload:function(data){
            var me = this,
                opt = me.options;
            me.options.customData = $.extend({},opt.customData,data);
            me._draw();
        }
    })
});
```