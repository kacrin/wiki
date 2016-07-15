---
title: "phpcms"
layout: page
date: 2016-07-15 00:00
---

## phpcms Intro ##

一个web框架,基于php+mysql,[官方网站](http://bbs.phpcms.cn),制作网站完全只用内容模块来实现,目前我也只懂用内容模块来搭建网站,[下载地址](http://www.phpcms.cn/index.php?m=content&c=down&a_k=2e5etaQ49b2hwW0zzVUq1l1d1MKwa5_xe46eTAuWkwtbZ1vQBvoLW3F_KxE6uNReFww6zrdG0YK6syrne7OUah3MziV2TOcLjjc5syLrKlnlYhOEC51Ceg0o0AFGmjkMp9cZHMmgbvfEE6t9jyBpji3cPebdGLtylw)在百度云网盘下thinkphp/目录下也有程序源码


## phpcms 安装 ##

在window环境下 xampp 一键环境内(apache+mysql+php),在本地xampp 下 首先搭建一个域名
-第一种配置域名方法
```
<VirtualHost *:80>
        ServerName zxwh.huishi365.cn
        DocumentRoot /var/www/zxwh
        ErrorLog /var/log/apache2/error-zxwh.huishi365.log
</VirtualHost>
```

-第二种配置端口方法
```
Listen 12551
NameVirtualHost *:12551
<VirtualHost *:12551>

        DocumentRoot "E:\BanGong\zentaopms\www"
        <Directory "E:\BanGong\zentaopms\www">
                AllowOverride All
                Allow from all
        </Directory>
</VirtualHost>
```

打开域名/端口号,到/install/目录下 ,安装phpcms


## phpcms 标签使用##


利用循环 获取内容标签,加自定义php判断,调用不同css样式显示内容(原因每个css样式不同)
pc 标签调用获取内容模块内容
```
{pc:content action="lists" catid="13" num="6"  order="listorder ASC"}
<?php
    $n =1;
    foreach($data as $r) {
        if( $n == 1 | $n==4 ){
            echo "<a href=".$r[url]."><img src=".$r[thumb]."></a>";
        }
        if( $n==2 | $n==5 ){
            echo "<a href=".$r[url]." class='two'><img src=".$r[thumb]."></a>";
        }
        if( $n==3 | $n==6 ){
            echo "<a href=".$r[url]." class='six'><img src=".$r[thumb]."></a>";
        }
        $n++;
    }
?>
{/pc}
```

标准标签调用方法
```
{pc:content action="lists" catid="17" num="8" order="listorder ASC"}
{loop $data $key $r}
/** 循环标签内容 **/
{loop}
{pc}
```

引用模板
```
{template "content","header"}
```


## jquery 动画展示图层插件 ##

[superslide](http://www.surperslide2.com),解决网站特效问题

```

<div class="loopdiv">
    <div class="hd">
       <a class="jiantou prev"><img src=""></a>
       <a class="jiantou next"><img src=""></a>
    </div>
    <div class="bd">
        <ul>
            <li></li>
            <li></li>
        </ul>
    </div>
</div>
<script type="text/javascript">
    jQuery(".loopdiv").slide({mainCell:".bd ul",autoPage:true,effect:"leftLoop",autoPlay:true,vis:5,delayTime:700});
</script>

'''

调用特效要先定义总的div,然后定义到div 下 .bd ul 下, 会特效显示<li>标签下内容
总div下 .hd 下是 调用下一个<li>标签样式 class下加 prev 是上一个, next 是下一个


调用独立标签
```
{$CATEGORYS[catid][url]}
```


## css 知识 ##


### img 标签约定像素 ###

```
<img height="200px" width="200px" src="/public/images/111.png">
```

### 引用视频 ###

{$VideoUrl}是引用video地址,从youku/ku6网址分享视频swf地址即可
```
object type="application/x-shockwave-flash" data="{$VideoUrl}" width="100%" height="500px" id="youku-player"><param name="allowFullScreen" value="true"><param name="allowScriptAccess" value="always"><param name="movie" value="{$VideoUrl}"><param name="flashvars" value="imglogo=&amp;paid=0&amp;partnerId=c097e4bfcaaf53f4&amp;styleid=9"></object>	
```

### IE 兼容css3 ###

引用pie.js 具体支持哪些样式还要研究,阴影是可以

```

<!--[if IE]>
<script type="text/javascript" src="/public/js/PIE.js"></script>
<script type="text/javascript" src="/public/js/PIE_uncompressed.js"></script>
<![endif]-->
<script>
$(function() {
    if (window.PIE) {
        $('.js_kc ul').each(function() {
            PIE.attach(this);
        });
    }
});
</script>

```

### 右侧固定悬浮栏 ###

```

<style>
commonposition{_position:absolute;_top: expression(documentElement.scrollTop + 200 + "px");position:fixed; top:20%; right:0px; z-index:999999; display:none;}
.commonposition .HomeQQ{display:block; width:45px; height:45px; position:relative; background-color:#e20000; background-image:url(/public/images/icon.png);background-repeat:no-repeat;}
.commonposition span{display:none; position:absolute; right:0px; top:0px; height:45px; padding-left:50px; color:#fff;line-height:45px; font-size:14px;background-color:#262626;}

.QQ{display:block; width:45px; height:45px; position:relative; cursor:pointer;}

.HomeQQ{ background-position:-362px -186px;}
.Homedianhua{ background-position:-362px -231px;}
.Homexuqiu{ background-position:-362px -275px;}
.Homeweixin{display:block; width:45px; height:45px; position:relative; cursor:pointer; padding-top:1px; }
.weixin{ background-position:-362px -322px;display:block; width:45px; height:45px; position:relative; background-color:#e20000; background-image:url(/public/images/icon.png);background-repeat:no-repeat;}
.top{display:block; width:45px; height:45px; position:relative; padding-top:1px; overflow:hidden;}
.Hometop{ background-position:-362px -370px; background-color:#4e4e4e; display:block; width:45px; height:45px; position:relative; background-image:url(/public/images/icon.png);background-repeat:no-repeat; overflow:hidden; margin-top:1px; cursor:pointer;}

.QQbggray{background-position:-360px -186px;background-image:url(/public/images/icon.png);background-repeat:no-repeat; width:90px; position:absolute; margin-top:1px; height:45px;}
.commonposition span.weixinerweima{right:47px; top:-12px; padding:0px;}
.dianhuabggray{background-position:-362px -231px;background-image:url(/public/images/icon.png);background-repeat:no-repeat;width:120px;}
.Homexuqiu{ background-position:-362px -275px;background-image:url(/public/images/icon.png);background-repeat:no-repeat;width:120px;}
</syle>
<div class="commonposition" style="display: block;">
    <div class="QQ" style="margin-top:0px;">
        <div class="HomeQQ" style="display:block;"></div>
        <a href="http://wpa.qq.com/msgrd?v=3&amp;uin=542608640&amp;site=www.huishi365.com&amp;menu=yes" target="_blank"><span class="QQbggray" style="display:none; ">业务咨询</span></a>
    </div>

    <div class="Homeweixin">
        <span class="weixinerweima" style="display:none;"><img src="/public/images/erweima2.png" width="210" height="210" ></span>
        <div class="weixin" ></div>
    </div>
        <div class="Hometop">
        </div>
</div>

<script type="text/javascript" >
    $(document).ready(function (){
        $(".HomeQQ").mouseover(function(){
            $(".HomeQQ").hide();
            $(".QQbggray").show(300);
        });
        $(".QQbggray").mouseout(function(){
            $(".QQbggray").hide();
            $(".HomeQQ").show(300);
        });
        $(".weixin").mouseover(function(){
            $(".weixinerweima").show(300);
        });
        $(".weixin").mouseout(function(){
            $(".weixinerweima").hide(300);
        });
        $(".Hometop").click(function(){
            $('html').animate({scrollTop:$('body').offset().top},500,'swing');
        });
    })
</script>

```

## 