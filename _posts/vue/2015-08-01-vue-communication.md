---
layout: post
title: vue 父子组件通信
categories: Vue
description: vue 父子组件通信
keywords: vue,通信
---

父子组件通信
父链

子组件可以用 this.$parent 访问它的父组件。根实例的后代可以用 this.$root 访问它。父组件有一个数组 this.$children，包含它所有的子元素。

尽管可以访问父链上任意的实例，不过子组件应当避免直接依赖父组件的数据，尽量显式地使用 props 传递数据。另外，在子组件中修改父组件的状态是非常糟糕的做法，因为：

    这让父组件与子组件紧密地耦合；

    只看父组件，很难理解父组件的状态。因为它可能被任意子组件修改！理想情况下，只有组件自己能修改它的状态。

自定义事件

Vue 实例实现了一个自定义事件接口，用于在组件树中通信。这个事件系统独立于原生 DOM 事件，用法也不同。

每个 Vue 实例都是一个事件触发器：

    使用 $on() 监听事件；

    使用 $emit() 在它上面触发事件；

    使用 $dispatch() 派发事件，事件沿着父链冒泡；

    使用 $broadcast() 广播事件，事件向下传导给所有的后代。

不同于 DOM 事件，Vue 事件在冒泡过程中第一次触发回调之后自动停止冒泡，除非回调明确返回 true。

简单例子：
```
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

```
// 注册子组件
// 将当前消息派发出去
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

使用 v-on 绑定自定义事件

上例非常好，不过从父组件的代码中不能直观的看到 "child-msg" 事件来自哪里。如果我们在模板中子组件用到的地方声明事件处理器会更好。为此子组件可以用 v-on 监听自定义事件：
```
<child v-on:child-msg="handleIt"></child>
```

这样就很清楚了：当子组件触发了 `”child-msg”` 事件，父组件的 `handleIt` 方法将被调用。所有影响父组件状态的代码放到父组件的 `handleIt` 方法中；子组件只关注触发事件。 

子组件索引

尽管有 props 和 events，但是有时仍然需要在 JavaScript 中直接访问子组件。为此可以使用 v-ref 为子组件指定一个索引 ID。例如：

```
<div id="parent">
  <user-profile v-ref:profile></user-profile>
</div>
```

```
var parent = new Vue({ el: '#parent' })
// 访问子组件
var child = parent.$refs.profile
```
v-ref 和 v-for 一起用时，ref 是一个数组或对象，包含相应的子组件。