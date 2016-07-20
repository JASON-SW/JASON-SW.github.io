---
layout: post
title:  "css3实现带半圆缺口的代金券"
date:   2016-07-19 14：47
categories: css
tags:  css  
excerpt: 利用CSS3的transform下面的属性
---

* content
{:toc}

#### 前言

最近在进行产品的UI改版，其中有一个关于代金券，要做成中间带圆角缺口的效果，其实这个很常见，上次见还是糯米发的看影券,如下如效果：

![代金券](/img/daijinquan.png)

以下是Demo实现：

#### HTML结构

``` html
<body>
    <div class="line-left"></div>
    <div class="line-left line-right"></div>        
</body>
```

#### CSS 代码

``` css
body{margin:0;padding:0;padding-top: 100px;padding-left: 100px;}
.line-left{background:gray ;width: 10px;height: 60px;border-right: 1px dashed red;position: relative;float: left;}
.line-left:before{position: absolute;right:-1px;top:0;content: ' ';width:4px;height:4px;border-radius:0  0 4px;background: #fff;transform: rotateY(180deg);}
.line-left:after{position: absolute;right:-1px;bottom:0;content: ' ';width:4px;height:4px;border-radius:4px 0  0 ;background: #fff;}
.line-right{-webkit-transform:scaleX(-1);transform:scaleX(-1);float: left;}
```

#### 效果图

![代金券](/img/circle-gap.png)

最后放到里面拼接就好。

【Tips】 transform:scaleX(-1)是为实现 镜像效果。

#### 后记

*思考怎样用一个标签实现？？？*

学生不才，望道友看到后有想法可以在下面留言哟