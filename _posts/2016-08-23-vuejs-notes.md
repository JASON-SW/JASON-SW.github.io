---
layout: post
title:  "VueJs学习札记(一)"
date:   2016-08-23 14:43
categories: js
tags:  Vue  
excerpt: 自学VueJS的入门知识
---

* content
{:toc}

#### 前言

前段时间VueJS很火，号称轻量高效，叫板Angular和React，其实我倒不是因为它火而去学习它，因为我们公司的其他团队在新项目上已用上了Vuejs，听说一个前端hold着那条小业务线，这是觉得是有必要去学习一下。

#### VueJS是什么
它是一个轻量级的MVVM前端(js)框架，通过简洁的API提供高效的数据绑定和灵活的组件系统，在纷繁的前端世界里占有一席之地。

它身怀六大优势：简洁、数据驱动(自动追踪依赖)、组件化(解耦，模块化开发)、轻量(无依赖 24kb min+gzip)、快速、模块友好(vue-cli脚手架快速构建项目)。 

它的作者是尤雨溪[博客](http://evanyou.me/)，一位华人，也正是因为如此，Vue的官网的中文文档翻译的很好，简明易懂，那我写这个入门还有什么意思呢，权当是自己的笔记和好久没有码文的锻炼吧。

#### Hello World

编程界，万事从hello world开始，这已经是约定俗成的了。

```html
<div id="app">
  {%raw%}{{ message }}{%endraw%}  
</div>
```

```js
new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue.js!'
  }
})
```

输出 ===>

```
Hello Vue.js!
```

乍一看，DOM跟ng一样，输出也一样，都可以用 `v-bind` 指令输出，不一样的就是js部分，ng是，实例化一个module，而vue则实例化Vue这个对象，写法上比React简单，不易出错，我想这条有点很中重要，可以让项目内不熟悉它的人快速上手。

#### 双向数据绑定

```html
<div id="demo">
    <p>{%raw%}{{ message }}{%endraw%}</p>
  <input v-model="message">
</div>
```

```js
var demo = new Vue({
  el: '#demo',
  data: {
    message: 'Hello Vue.js!'
  }
})
```

↓ ↓ ↓ ↓ ↓ ↓ 

<div id="demo">
    <p>{%raw%}{{ message }}{%endraw%}</p>
  <input v-model="message">
</div>
<br/>
<script src="/libs/vue.js"></script>

<script>
   var demo = new Vue({
      el: '#demo',
      data: {
        message: 'Hello Vue.js!'
      }
    }) 
</script>
  

您可以在上面的输入框尝试一下，它实现了把input的输入值绑定到数据层即model上，而且可以双向的(默认单向)，数据model的set或者赋值会驱动视图同步变化，是不是很神奇？好吧，早已不足为奇了，因为这个我们早在Angular的官网上看到过，不过，据vue作者介绍，两者实现双向数据绑定的原理并不一样，ng基于脏值检查，vue则基于是依赖驱动，它模板中的指令/数据绑定都有一个类似于ng的watcher对象，在计算过程中把属性记录为依赖。之后当依赖的setter被调用时，会出发watcher重新计算，也就会导致它的关联指令更新DOM。

以上文字介绍数据绑定略显枯燥，实为引用作者自己的介绍，我想这个概念要在实际项目中和翻阅源码中去理解，对，理论和实践相结合就是我们攻城略地的方法论。

#### 添加类和方法

##### v-bind:style

vue在输出和控制class添加上推荐使用`v-bind`一个对象或者数组，而不是Mustache标签绑定，比如:

```
class = "{%raw%}{{ className }}{%endraw%}"
```

数据驱动视图的理念教会我们随着model数据(数据的改变)，自动并动态地切换calss。这时也可以使用普通的class，拓展性更强。

```
    <div class="static" v-bind:class="{ 'class-a': isA, 'class-b': isB }"></div>
```

```html
    data: {
        isA: true,
        isB: false
    }
```

渲染为：

```html
    <div class="static class-a"></div>
```

也可以这样写：

```
    <div v-bind:class="classObject"></div>

    data: {
      classObject: {
        'class-a': true,
        'class-b': false
      }
}
```

抑或是这样写：

```
    <div v-bind:class="[classA, classB]">

    data: {
      classA: 'class-a',
      classB: 'class-b'
    }
```

甚至可以写三元表达式：

```
    <div v-bind:class="[classA, isB ? classB : '']">
```

怎么写的舒服就怎么写，任性！

有的时候我们会在页面或模板中写一些内联样式，vue可以这样搞：

```
    <div v-bind:style="{ color: red, fontSize: '16px' }"></div>
```

其中内联样式也可以使用数组语法，还有一个省心的事就是当`v-bind:style` 使用需要厂商前缀的`CSS`属性时,vuejs就会暖心地帮我们加上。

##### v-on:event

事件绑定是我们在Web开发中经常需要用到的。vuejs的事件绑定的语法和用法与ng类似，vuejs比ng更灵活，不但支持缩写形式，还可以使用修饰符。

```
    <div id="app" v-on:click='handler'></div>

    <!-- 缩写形式 -->

    <div id="app" @click='handler'></div>

    <!-- 按键修饰符 -->

    <input type="text" name='username' @keyup.enter>  <!-- @keyup.13 亦可 -->

    <!-- 事件修饰符 -->
    
    <a v-on:click.stop="doThis"></a>

    <form v-on:submit.prevent="onSubmit"></form>

```

这些好用的修饰符可以快速帮我们实现常用的一些需求，如阻止事件冒泡的方法：event.stopPropagation()，这时我们就可以使用`.stop`这个修饰符，方便快捷，另外值得一提的是vue对event做了封装，只要在方法中传递$event，就能在方法中获取并使用它。关于其他的修饰符的详细信息可以去vue的官网查看相应的API。

在DOM中绑定好事件以后，在`new Vue({});`的methods里定义方法体即可。至于为什么在`HTML`中监听事件，作者也给出了很详细的回答，我觉得最重要一点还是：这样做似的ViewModel里的代码几乎完全和DOM解耦，更易于测试。

vue绑定的事件跟ng一样，当一个ViewModel被销毁时，所有的事件处理器都会自动被删除，使用者无需手动清理它们。

#### 条件、列表渲染

###### 条件渲染

vue的条件判断跟ng的如出一辙，除了 `v-if`、`v-show`以外，还多了一个`v-else`，它紧跟在前两者后面使用。

`v-if` 和 `v-show`的区别就是：前者纯属逻辑判断，满足条件再行渲染，有后者的元素会始终渲染并保持在DOM中，也就是说`v-show`只是简单的css:display的切换。

###### 列表渲染

`v-for`指令基于一个数组渲染列表。使用方法跟ng一模一样。如下示例：

```html
<ul id="example">
  <li v-for="item in items">
    {%raw%}{{ item.message }}{%endraw%}
  </li>
</ul>
```

上例中的items直接定义在vue的data对象里。

在`v-for`块内可以完全访问父组件作用域内的属性，另有一个特殊变量`$index`,它指向当前数组元素的索引。

vue.js包装了被观察数组的编译方法，能触发视图更新。方法包括:

+    `push()`
+    `pop()`
+    `shift()`
+    `unshift()`
+    `splice()`
+    `sort()`
+    `reverse()`

以上都是遍历数组，那么对象怎么遍历呢，且看：

```html
    <div v-for="(key, val) in object">
      {%raw%}{{ key }}{%endraw%} : {%raw%}{{ val }}{%endraw%}
    </div>
```

在遍历对象时，是按`Object.keys()` 的结果遍历，但是不能保证它的结果在不同的JavaScript引擎下是一致的。

另外还可以在渲染列表或对象的时候使用过滤器，如`filterBy`和`orderBy`。

#### 表单控件

vue.js提供了丰富的表单控件，对常用的表单元素进行了封装。其实我在现在的开发中比较少用到表单，几乎都是`ajax`，这里就举一个多选`select`的例子吧。

```html
    <select v-model="selected">
      <option selected>A</option>
      <option>B</option>
      <option>C</option>
    </select>
    <span>Selected: {{ selected }}</span>
```

<div id="selectModel">
    <select v-model="selected">
      <option v-for="option in options" v-bind:value="option.value">
        {%raw%}{{ option.text }}{%endraw%}
      </option>
    </select>
    <br>
    <span>Selected: {%raw%} {{ selected }} {%endraw%}</span>
</div>


```js
    new Vue({
      el: '#selectModel',
      data: {
        selected: 'A',
        options: [
          { text: 'One', value: 'A' },
          { text: 'Two', value: 'B' },
          { text: 'Three', value: 'C' }
        ]
      }
    })
```
<script>
    new Vue({
      el: '#selectModel',
      data: {
        selected: 'A',
        options: [
          { text: 'One', value: 'A' },
          { text: 'Two', value: 'B' },
          { text: 'Three', value: 'C' }
        ]
      }
    })
</script>

相比ng来说，vue.js少了表单验证及错误提示，但我觉得这些倒是可以自己编写，毕竟现在的验证提示花样很多，最近比较流行的貌似是toast提示，不过h5里用要注意返回问题。
















