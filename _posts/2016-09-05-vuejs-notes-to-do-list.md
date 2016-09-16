---
layout: post
title:  "VueJs学习札记(三)"
date:   2016-09-05 10:13
categories: js
tags:  Vue  
excerpt: 使用Vue做一个toDoList
---

* content
{:toc}

### 前言

在学习Backbone时，官网推荐了一个包含其基本用法的小例子：toDoList(记事本)，后来的框架出来以后也借此示例用自己的规范和实现做了类似的小例子，下面就用Vue来实现它，也算巩固了之前的基础学习。

### 开发环境的搭建

环境是基于如今流行的前端工程化NodeJS实现，命令行安装。首先是确保已安装了NodeJS，这个可以去官网下载安装包(.exe执行文件)，如果下载被墙，可以使用淘宝提供的镜像服务器(自行百度)。安装好之后，在cmd窗口或者bash窗口里执行：`node -v`，正确打印出版本号就代表安装成功。node的安装里包含了其包管理工具NPM，类似的测试指令：`npm -v`。


1.安装VueJS 

```bash
 npm install vue -g
```

*-g：全局安装*

2.创建一个基于`webpack`模板的新项目

```bash
  vue init webpack my-project 
```

3.切换目录

```bash
  cd my-project
```

4.项目初始化

```bash
  npm install
```

5.启动项目

```bash
  npm run dev
```

6.然后打开浏览器，在地址栏输入 http://localhost:8090/， 即可看到vue的欢迎页。

### 编写项目代码

回到项目文件下：

![vueProjTree](/img/vue-proj-tree.png)

在开发源目录src下，找到App.vue文件并打开，修改`template`里的文本，保存后可以看到网页自动刷新，这就是热更新。

在`script`标签里的exports关键字，返回的时候会转化为`new Vue{}`的形式。所以可以直接在内部写Vue的配置对象，其中有data，components，methods，watch等。

toDoList的主文件：App.vue

```html
<template>
  <div id="app">
      <h1>{{title}}</h1>
      <input type="text" v-model="newItem" @keyup.13="addNew"/>
      <ol>
        <li v-for="item in items" :class="{finished: item.isFinished}" @click="toggle(item)">
          {{item.lable | uppercase}}
          <button class="del-btn" @click="delItem(this,$index)">X</button>
        </li>
      </ol>
      <!-- <component-a msgformfather='main app:to-do-list' ></component-a>
      <p>child tell me:{{childWords}}</p> -->
  </div>
</template>

<script>
import Store from './store'
import componentA from './components/Hello'
export default {
  data: function () {
    return {
      title: 'this is a todo list',
      items: Store.fetch(),
      newItem: '',
      childWords: ''
    }
  },
  components: {componentA},
  events: {
    'child-tell-something': function (msg) {
      this.childWords = msg
    }
  },
  methods: {
    toggle: function (item) {
      // console.log(item)
      item.isFinished = !item.isFinished
    },
    delItem: function (el, index) {
      el.$event.stopPropagation()
      this.items.splice(index, 1)
    },
    addNew: function () {
      if (this.newItem) {
        this.items.push({lable: this.newItem, isFinished: false})
        this.newItem = ''
        this.$broadcast('parent-items', this.items)
      }
    }/*,
    listenToByChild: function (msg) {
      this.childWords = msg
    }*/
  },
  watch: {
    items: {
      handler: function (items) {
        Store.save(items)
      },
      deep: true
    }
  }
}
</script>

<style>
html {
  height: 100%;
}

body {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100%;
}

li {
  clear: both;
  margin-bottom: 15px;
}

li:hover{
  background-color: orange;
  cursor: pointer;
} 
.finished {
  text-decoration: line-through;
  color: #ccc;
  cursor: move;
}

#app {
  color: #2c3e50;
  margin-top: -300px;
  max-width: 600px;
  font-family: Source Sans Pro, Helvetica, sans-serif;
  text-align: center;
}

#app a {
  color: #42b983;
  text-decoration: none;
}

.logo {
  width: 100px;
  height: 100px
}

.del-btn{
  float: right;
  appearance:none;
}

.del-btn:hover{
  background-color: red;
  transform: scale(1.5,1.5);
  color: #fff;
}
</style>
```

其中需要导入的，实现本地存储的Store.js:

```js
const STORE_KEY = 'todos-vuejs'
export default {
  fetch: function () {
    return JSON.parse(window.localStorage.getItem(STORE_KEY))
  },
  save: function (items) {
    window.localStorage.setItem(STORE_KEY, JSON.stringify(items))
  }
}
```

这个例子还包括了父子组件相互传值，如上注释的部分，其中用到的子组件：

```html
<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <h2>{{ msgformfather }}</h2>
    <button @click='clickMe'>Open Mouse!</button>
  </div>
</template>

<script>
export default {
  data: function () {
    return {
      msg: 'Hello from Component-Hello!'
    }
  },
  props: ['msgformfather'], // 父组件向子组件传值
  methods: {
    clickMe: function () {
      // console.log(this.msgformfather)
      // this.$emit('child-tell-something', this.msg) // 子组件向父组件传值，通过自定义事件
      this.$dispatch('child-tell-something', this.msg)
    }
  },
  events: {
    'parent-items': function (msg) {
      console.log(msg)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1 {
  color: #42b983;
}
</style>
```

**Demo Result如下图:**

![todoListResult](/img/todolist.png)


### 结语

纸上得来终觉浅，绝知此事要躬行。   --- 陆游

理论和实践相结合。 --- 出自 唯物辩证的方法论




