---
layout: post
title:  "css 优化"
date:   2016-07-04 15：13
categories: css
tags:  css 性能优化 
excerpt: css优化小结
---

* content
{:toc}

#### 前言

之前一直想要写一些css优化的文章，又不知从何写起，怕写的零散没营养，但是如今看来是真的比较零散，所以我就写一些总结吧。

#### 一、工具检测`css` code，如   [CSS lint](http://csslint.net/)

#### 二、命名简短知意

#### 三、合并代码，压缩代码发布

#### 四、尽量重用样式 (注意选择符的 优先级)

#### 五、减少甚至避免深层次嵌套 （不超过三层）

#### 六、避免空的样式定义 、表达式 


#### 后记

在做最近项目时一直纠结于：多个css文件合并成一个好，还是分模块写依次引入好？举个栗子：

![lizi](/img/lizi.jpg)

在写css之前我们总要重置一些元素的默认样式，这些代码显然是可以共用的，pc站的话还可以借鉴 CSS Reset或者 Normalize.css来统一默认样式。

最终我还是选择了合并，我想这还是要根据项目来，我现在的项目只有4,5个页面，所以我选择了直接写在我的业务代码里，而且没有引用上面说的那些高逼格的重置，当然也没有使用简单粗暴的：

``` css
body{margin:0;padding:0}
ul{list-style:none}
/* ...  */
```

我的项目是重功能的web app，所以在我只需要重置页面中用到的标签即刻，如下：

``` css
@charset "UTF-8";
html,body{-webkit-text-size-adjust:100%;-ms-text-size-adjust:100%;-webkit-tap-highlight-color:transparent;-ms-user-select:none;-webkit-user-select:none;user-select:none;-webkit-touch-callout:none;}
body,div,dl,dt,dd,ul,ol,li,h1,h2,h3,h4,h5,h6,pre,code,form,input,textarea,p,th,td,hr,button,article,aside,footer,header,menu,nav,section{margin:0;padding:0}
article,aside,footer,header,menu,nav,section{display:block}
body,button,input,select,textarea{font:14px/1.5 arial,\5b8b\4f53}
input,select,textarea{font-size:100%;-webkit-appearance:none}
select,fieldset,img{border:0}
table{border-collapse:collapse;border-spacing:0}
address,caption,cite,code,em,th,var{font-style:normal;font-weight:500}
ol,ul{list-style:none}
h1,h2,h3,h4,h5,h6{font-size:100%;font-weight:500}
a:hover,a:active,ins,a{text-decoration:none}
textarea:focus,input:focus,button:focus{outline:0}
```

之后就可以愉快的码css了，其中遇到过一些奇葩的样式问题，印象较深的是安卓2.3及其以下机型，排查下来主要是对部分CSS3 属性支持不好所致，这时候就要优雅降级了。


*Tips：css知识点零碎，无逻辑，只有不断的踩坑，不断累积，方能大成。*

*So ，你我共勉，前端淫*