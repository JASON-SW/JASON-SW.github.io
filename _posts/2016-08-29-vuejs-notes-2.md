---
layout: post
title:  "VueJs学习札记(二)"
date:   2016-08-29 10:13
categories: js
tags:  Vue  
excerpt: 自学VueJS的入门知识(组件、指令)
---

* content
{:toc}

### 前言

组件的概念由来已久，我想可能从JQ插件而来，也可能是这些年`JS`模块化开发的热潮而发，当然这都是臆想，vue.js的组件最强大的功能之一 ，它可以扩展`HTML`，封装可重用的代码，实现业务代码的松耦合。

### 组件

#### 注册组件

最基本的方式使用`Vue.extend()`创建一个组件的构造器：

```js
  var MyComponent = vue.extend({
    //options
    template: '<div>A custom component</div>',
    components: ChildComponent //【组件局部注册】 此组件只能用在父组件模板里使用
  });

  var ChildComponent= Vue.extend({ /* ...  */ });
```

上面是定义，要使用时，还要用`vue.component(tagID,constructor)`注册：

```js
    // 全局注册组件,全局的模板都可使用
    Vue.component('my-component',MyComponet);
```

这时候就可以在父实例中以自定义元素的形式使用：

```
    <div id="example">
        <my-component></my-component>
    </div>

    // 创建实例
    new Vue({
        el: '#example'
    })
```

最后会渲染为：

<div id="example">
  <div>A custom component!</div>
</div><br>

注意：

+ 组件的模板 **替换**了自定义元素，自定义元素的作用只是作为一个挂载点。可以用实例选项`replace`决定是否替换。
+ 注册时可以直接传入选项对象而不是构造器给`Vue.component()`和`component`选项。Vue.js在背后会自动调用`Vue.extend()：

```
    // 在一个步骤中扩展与注册
    Vue.component('my-component', {
      template: '<div>A custom component!</div>'
    })  
```
+ 组件选项 `data`和 `el` 的问题：如果给实例选项属性`data`或者`el`直接赋值，那么组件的所有实例将共享同一个`data`或者`el`对象，显然这并不是我们想要的，解决办法就是赋值一个函数，让其返回一个新的对象：

```js
    var MyComponent = Vue.extend({
        el: function(){
            return '.demo'
        },
        data: function(){
            return {value:'jason shi'}
        }
    });
```

#### 模板解析

Vue的模板是DOM模板，使用浏览器原生的解析器，效率比字符串高，但也有其不足之处：它必须是有效的HTML片段。一些HTML元素对什么元素可以放在它里面有限制。

常见的限制：

-  `a`不能包含其他的交互元素（如按钮，链接）
-  `ul`和`ol`只能直接包含`li`
-   `select`只能包含`option`和`optgroup`
-   `table`只能包含`thead`,`tbody`,`tfoot`,`tr`,`caption`,`col`,`colgroup`
-   `tr`只能直接包含`th`和`td`

有了这些糟心的限制就很容易出错，从而导致渲染不正确。不过幸好作者又给我们提供了一神奇特性`is`：

```html
    <table>
        <tr is="MyComponent"></tr>
    </table>
```

#### Props传值

我们定义好了每个组件，那么各个组件之间怎么传递数据呢（组件实例的作用域是孤立的），Vue.js提供了利用Props属性可以实现从父组件向子组件传值。

“props”其实是组件数据的一个字段，使用时 子组件需要显示地声明：


```js
    Vue.component('Child', {
        //声明props
        props: ['msgInfo'], 
        //props可以用在模板内
        //可以用 `this.msg`设置
        template: '<span>{%raw%}{{ msg }}{%endraw%}</span>'
    })
```

然后向它传入一个普通的字符串

```html
    <Child msg-info="你是魔鬼中的天使"></Child>
```

结果：

<p style="background:#ccc">你是魔鬼中的天使</p>

注意：在用props传值时，尤其是传递一个Number类型的数字时，例如：

```html
    <!-- 传递了一个字符串 -->
    <MyComponent some-prop="1"></MyComponent>
```
上面例子传递了"1",因为它是一个字面prop。如果想传递数字1，这时候就要用动态prop语法，从而让它的值被当做JavaScript表达式计算：

```html
    <!-- 传递了实际的数字 -->
    <MyComponent v-bind:some-prop="1"></MyComponent>
```

#### Prop绑定类型

prop默认是**单向**绑定：当父组件的属性变化时，将传导给子组件，但是反过来不会。官方说是为了防止子组件无意修改的父组件的状态；不过好幸好还有其他的选择，可以使用`.sync`或`.once` **绑定修饰符**显式地强制双向或者单次绑定：

比较语法：

```html
    <!-- 默认为单向绑定 -->
    <child :msg="parentMsg"></child>

    <!-- 双向绑定 -->
    <child :msg.sync="parentMsg"></child>

    <!-- 单次绑定 -->
    <child :msg.once="parentMsg"></child>
```

双向绑定会把子组件的 `msg` 属性同步回父组件的 `parentMsg` 属性。单次绑定在建立之后不会同步之后的变化。

要注意的是，如果 prop 是一个对象或数组，是按引用传递。在子组件内修改它会影响父组件的状态，不管是使用哪种绑定类型。

#### Prop验证

组件可以为 props 指定验证要求。当组件给其他人使用时这很有用，因为这些验证要求构成了组件的 API，确保其他人正确地使用组件。此时 props 的值是一个对象，包含验证要求：

```js
    Vue.component('example', {
      props: {
        // 基础类型检测 （`null` 意思是任何类型都可以）
        propA: Number,
        // 多种类型 (1.0.21+)
        propM: [String, Number],
        // 必需且是字符串
        propB: {
          type: String,
          required: true
        },
        // 数字，有默认值
        propC: {
          type: Number,
          default: 100
        },
        // 对象/数组的默认值应当由一个函数返回
        propD: {
          type: Object,
          default: function () {
            return { msg: 'hello' }
          }
        },
        // 指定这个 prop 为双向绑定
        // 如果绑定类型不对将抛出一条警告
        propE: {
          twoWay: true
        },
        // 自定义验证函数
        propF: {
          validator: function (value) {
            return value > 10
          }
        },
        // 转换函数（1.0.12 新增）
        // 在设置值之前转换值
        propG: {
          coerce: function (val) {
            return val + '' // 将值转换为字符串
          }
        },
        propH: {
          coerce: function (val) {
            return JSON.parse(val) // 将 JSON 字符串转换为对象
          }
        }
      }
    })
```

当prop验证失败了，Vue将拒绝在子组件上设置此值，如果使用的开发版本就会抛出一条警告。

#### 子-父传值

Vue实例实现了一个自定义事件接口，用于在组件树种通信。这个事件系统独立于原生DOM事件，用法也不同。

每个Vue实例都是一个事件触发器：

* 使用`$on()` 监听事件
* 使用`$emit()` 在它上面触发事件
* 使用`$dispatch()` 派发事件，事件沿着父链冒泡
* 使用`$broadcast()` 广播事件，事件向下传导给所有的后代

*注意：不同于 DOM 事件，Vue 事件在冒泡过程中第一次触发回调之后自动停止冒泡，除非回调明确返回 true。*

下面是个简单的例子：

HTML ↓

```html
    <!-- 子组件模板 -->
    <template id="child-template">
      <input v-model="msg">
      <button v-on:click="notify">Dispatch Event</button>
    </template>

    <!-- 父组件模板 -->
    <div id="events-example">
      <p>Messages: {{ messages | json }}</p>
      <child></child>
    </div>
```

JS ↓

```js
    // 注册子组件
    Vue.component('child', {
      template: '#child-template',
      data: function () {
        return { msg: 'hello' }
      },
      methods: {
        notify: function () {
          if (this.msg.trim()) {
            this.$dispatch('child-msg', this.msg) // 将当前消息派发出去
            this.msg = ''
          }
        }
      }
    })

    // 初始化父组件
    // 将收到消息时将事件推入一个数组
    var parent = new Vue({
      el: '#events-example',
      data: {
        messages: []
      },
      // 在创建实例时 `events` 选项简单地调用 `$on`
      events: {
        'child-msg': function (msg) {
          // 事件回调内的 `this` 自动绑定到注册它的实例上
          this.messages.push(msg)
        }
      }
    })
```
输出 ↓

<template id="child-template">
      <input v-model="msg">
      <button v-on:click="notify">Dispatch Event</button>
</template>

<div id="events-example">
  <p>Messages: {%raw%}{{ messages | json }}{%endraw%}</p>
  <child></child>
</div>

<script src="/libs/vue.js"></script>

<script>
    Vue.component('child', {
      template: '#child-template',
      data: function () {
        return { msg: 'hello' }
      },
      methods: {
        notify: function () {
          if (this.msg.trim()) {
            this.$dispatch('child-msg', this.msg) 
            this.msg = ''
          }
        }
      }
    })


    var parent = new Vue({
      el: '#events-example',
      data: {
        messages: []
      },
      
      events: {
        'child-msg': function (msg) {
          this.messages.push(msg)
        }
      }
    })
</script>

### 自定义指令

除了内置指令，Vue.js 也允许注册自定义指令。自定义指令提供一种机制将数据的变化映射为 DOM 行为。
可以用` Vue.directive(id, definition) `方法注册一个全局自定义指令，它接收两个参数指令 ID 与定义对象。也可以用组件的 `directives `选项注册一个局部自定义指令。

#### Hook函数

定义对象可以提供几个钩子函数（都是可选的）：

+ bind：只调用一次，在指令第一次绑定到元素上时调用。
+ update： 在 bind 之后立即以初始值为参数第一次调用，之后每当绑定值变化时调用，参数为新值与旧值。
+ unbind：只调用一次，在指令从元素上解绑时调用。

**实例**

```js
   Vue.directive('my-directive', {
      bind: function () {
        // 准备工作
        // 例如，添加事件处理器或只需要运行一次的高耗任务
      },
      update: function (newValue, oldValue) {
        // 值更新时的工作
        // 也会以初始值为参数调用一次
      },
      unbind: function () {
        // 清理工作
        // 例如，删除 bind() 添加的事件监听器
      }
    }) 
```

在注册之后，便可以在 Vue.js 模板中这样用（记着添加前缀 `v-`）：

```
    <div v-my-directive="someValue"></div>
```

当只需要 `update` 函数时，可以传入一个函数替代定义对象：

```js
    Vue.directive('my-directive', function (value) {
      // 这个函数用作 update()
    })
```

#### 指令实例属性

所有的钩子函数将被复制到实际的指令对象中，钩子内 `this `指向这个指令对象。这个对象暴露了一些有用的属性：

+ el: 指令绑定的元素。
+ vm: 拥有该指令的上下文 ViewModel。
+ expression: 指令的表达式，不包括参数和过滤器。
+ arg: 指令的参数。
+ name: 指令的名字，不包含前缀。
+ modifiers: 一个对象，包含指令的修饰符。
+ descriptor: 一个对象，包含指令的解析结果。

*注意*

你应当将这些属性视为只读的，不要修改它们。你也可以给指令对象添加自定义属性，但是注意不要覆盖已有的内部属性。

#### 对象字面量

如果指令需要多个值，可以传入一个 `JavaScript` 对象字面量。记住，指令可以使用任意合法的 `JavaScript` 表达式：

```html
    <div v-demo="{ color: 'white', text: 'hello!' }"></div>
```

```js
    Vue.directive('demo', function (value) {
    console.log(value.color) // "white"
    console.log(value.text) // "hello!"
})
```

#### 字面修饰符

当指令使用了字面修饰符，它的值将按普通字符串处理并传递给 `update` 方法。`update` 方法将只调用一次，因为普通字符串不能响应数据变化。

```html
    <div v-demo.literal="foo bar baz">
```

```js
    Vue.directive('demo', function (value) {
    console.log(value) // "foo bar baz"
})
```

#### 元素指令

有时我们想以自定义元素的形式使用指令，而不是以特性的形式。这与 Angular 的 “E” 指令非常相似。元素指令可以看做是一个轻量组件。可以像下面这样注册一个自定义元素指令：

```js
    Vue.elementDirective('my-directive', {
      // API 同普通指令
      bind: function () {
        // 操作 this.el...
      }
})
```

元素指令不能接受参数或表达式，但是它可以读取元素的特性从而决定它的行为。

迥异于普通指令，元素指令是**终结性**的，这意味着，一旦 Vue 遇到一个元素指令，它将跳过该元素及其子元素——只有该元素指令本身可以操作该元素及其子元素。

#### 高级选项

##### param

自定义指令可以接收一个 `params` 数组，指定一个特性列表，Vue 编译器将自动提取绑定元素的这些特性。例如：

```html
    <div v-example a="hi"></div>
```

```js
    Vue.directive('example', {
      params: ['a'],
      bind: function () {
        console.log(this.params.a) // -> "hi"
      }
    })
```

此 API 也支持动态属性。`this.params[key]`会自动保持更新。另外，可以指定一个回调，在值变化时调用：

```html
    <div v-example v-bind:a="someValue"></div>
```

```js
    Vue.directive('example', {
      params: ['a'],
      paramWatchers: {
        a: function (val, oldVal) {
          console.log('a changed!')
        }
      }
    })
```

**注意**

类似于 props，指令参数的名字在 JavaScript 中使用 camelCase 风格，在 HTML 中对应使用 kebab-case 风格。例如，假设在模板里有一个参数 `disable-effect`，在 JavaScript 里以 `disableEffect `访问它。

##### deep

如果自定义指令用在一个对象上，当对象内部属性变化时要触发 `update`，则在指令定义对象中指定 `deep: true`。

```html
    <div v-my-directive="obj"></div>
```

```js
    Vue.directive('my-directive', {
      deep: true,
      update: function (obj) {
        // 在 `obj` 的嵌套属性变化时调用
      }
    })
```

##### twoWay

如果指令想向 Vue 实例写回数据，则在指令定义对象中指定`twoWay: true `。该选项允许在指令中使用`this.set(value)`:

```js
    Vue.directive('example', {
      twoWay: true,
      bind: function () {
        this.handler = function () {
          // 将数据写回 vm
          // 如果指令这样绑定 v-example="a.b.c"
          // 它将用给定值设置 `vm.a.b.c`
          this.set(this.el.value)
        }.bind(this)
        this.el.addEventListener('input', this.handler)
      },
      unbind: function () {
        this.el.removeEventListener('input', this.handler)
      }
    })
```

##### acceptStatement

传入 `acceptStatement:true` 可以让自定义指令接受内联语句，就像 `v-on` 那样：

```html
    <div v-my-directive="a++"></div>
```

```js
    Vue.directive('my-directive', {
      acceptStatement: true,
      update: function (fn) {
        // 传入值是一个函数
        // 在调用它时将在所属实例作用域内计算 "a++" 语句
      }
})
```

##### priority

可以给指令指定一个优先级。如果没有指定，普通指令默认是 1000， `terminal `指令默认是 2000。同一个元素上优先级高的指令会比其它指令处理得早一些。优先级一样的指令按照它在元素特性列表中出现的顺序依次处理，但是不能保证这个顺序在不同的浏览器中是一致的。

可以在 API 中查看内置指令的优先级。另外，流程控制指令 `v-if `和 `v-for `在编译过程中始终拥有最高的优先级。