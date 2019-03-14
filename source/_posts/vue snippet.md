---
title: vue snippet
date: 2017-12-07 21:50:30
tags: snippet
categories: vue
---

- vue实例初始化过程：

  - 设置数据监听
  - 编译模板
  - 将实例挂载到DOM并在数据变化时更新DOM等

- 生命周期钩子：提供用户在不同阶段添加自己的代码的机会
  - created，用来实例被创建之后执行代码
  - mounted,
  - updated,
  - destoryed

- 指令（Directives）是带有`v-`前缀的特殊特性。指令特性的值预期是单个JavaScript表达式（`v-for`是例外情况），指令的职责是：当表达式的值改变时，将其产生的连带影响，响应式地作用于DOM。

  - `v-bind`，可以接受一个参数，用于响应式地更新HTML特性。

    ```
    <!-- 完整语法 -->
    <a v-bind:href="url">...</a>
    
    <!-- 缩写 -->
    <a :href="url">...</a>
    ```

  - `v-on`，用于监听DOM事件

    ```
    <!-- 完整语法 -->
    <a v-on:click="doSomething">...</a>
    
    <!-- 缩写 -->
    <a @click="doSomething">...</a>
    ```

  - 修饰符`v-on:submit.prevent`,`.prevent` 修饰符告诉 `v-on` 指令对于触发的事件调用 `event.preventDefault()`。

------



- 计算属性

  ```
  <div id="example">
    <p>Original message: "{{ message }}"</p>
    <p>Computed reversed message: "{{ reversedMessage }}"</p>
  </div>
  ```
  ```
  var vm = new Vue({
    el: '#example',
    data: {
      message: 'Hello'
    },
    computed: {
      // 计算属性的 getter
      reversedMessage: function () {
        // `this` 指向 vm 实例
        return this.message.split('').reverse().join('')
      }
    }
  })
  ```

  - 计算属性缓存vs方法

    在方法中实现同计算属性同样的效果

    ```
    <p>Reversed message: "{{ reversedMessage() }}"</p>
    ```

    ```
    // 在组件中
    methods: {
      reversedMessage: function () {
        return this.message.split('').reverse().join('')
      }
    }
    ```

    **计算属性是基于它们的依赖进行缓存的**。只在相关依赖发生改变时它们才会重新求值。 

  - 计算属性vs侦听属性

    侦听属性的用法：

    ```
    <div id="demo">{{ fullName }}</div>
    ```

    ```
    var vm = new Vue({
      el: '#demo',
      data: {
        firstName: 'Foo',
        lastName: 'Bar',
        fullName: 'Foo Bar'
      },
      watch: {
        firstName: function (val) {
          this.fullName = val + ' ' + this.lastName
        },
        lastName: function (val) {
          this.fullName = this.firstName + ' ' + val
        }
      }
    })
    ```

    与计算属性的版本进行对比：

    ```
    var vm = new Vue({
      el: '#demo',
      data: {
        firstName: 'Foo',
        lastName: 'Bar'
      },
      computed: {
        fullName: function () {
          return this.firstName + ' ' + this.lastName
        }
      }
    })
    ```

  - 计算属性的setter

    计算属性默认只有 getter ，不过在需要时你也可以提供一个 setter ： 

    ```
    // ...
    computed: {
      fullName: {
        // getter
        get: function () {
          return this.firstName + ' ' + this.lastName
        },
        // setter
        set: function (newValue) {
          var names = newValue.split(' ')
          this.firstName = names[0]
          this.lastName = names[names.length - 1]
        }
      }
    }
    // ...
    ```

    现在再运行 `vm.fullName = 'John Doe'` 时，setter 会被调用，`vm.firstName` 和 `vm.lastName` 也会相应地被更新。 

- 侦听器

  使用场景：当需要在数据变化时执行异步或开销较大的操作时 

------

- 条件渲染

  `v-if`

- 展示元素

  `v-show`，它只是简单地切换元素的css属性`display`

  ***注意*：`v-show` 不支持 `<template>` 元素，也不支持 `v-else`。 

  - `v-if`vs`v-show`

    一般来说，`v-if` 有更高的切换开销，而 `v-show` 有更高的初始渲染开销。因此，如果需要非常频繁地切换，则使用 `v-show` 较好；如果在运行时条件很少改变，则使用 `v-if` 较好。 

- 列表渲染

  `v-for`

  - 实例一

    ```
    <ul id="example-1">
      <li v-for="item in items">
        {{ item.message }}
      </li>
    </ul>
    ```

    ```
    var example1 = new Vue({
      el: '#example-1',
      data: {
        items: [
          { message: 'Foo' },
          { message: 'Bar' }
        ]
      }
    })
    ```

  - 实例二

    ```
    <ul id="example-2">
      <li v-for="(item, index) in items">
        {{ parentMessage }} - {{ index }} - {{ item.message }}
      </li>
    </ul>
    ```

    ```
    var example2 = new Vue({
      el: '#example-2',
      data: {
        parentMessage: 'Parent',
        items: [
          { message: 'Foo' },
          { message: 'Bar' }
        ]
      }
    })
    ```

  - 可以用 of 替代 in 作为分隔符

    ```
    <div v-for="item of items"></div>
    ```

  - 实例三

    ```
    new Vue({
      el: '#v-for-object',
      data: {
        object: {
          firstName: 'John',
          lastName: 'Doe',
          age: 30
        }
      }
    })
    ```

    ```
    <ul id="v-for-object" class="demo">
      <li v-for="value in object">
        {{ value }}
      </li>
    </ul>
    ```

    ```
    <div v-for="(value, key) in object">
      {{ key }}: {{ value }}
    </div>
    ```

    ```
    <div v-for="(value, key, index) in object">
      {{ index }}. {{ key }}: {{ value }}
    </div>
    
    ```

- 数组更新检测

  - 变异方法，会改变被这些方法调用的原始数组 
    - `push()`
    - `pop()`
    - `shift()`
    - `unshift()`
    - `splice()`
    - `sort()`
    - `reverse()`
  - 也有非变异 (non-mutating method) 方法，例如：`filter()`, `concat()` 和 `slice()` 。这些不会改变原始数组，但**总是返回一个新数组**。

  ------

- vue组件实例

  ```
  // 定义一个名为 button-counter 的新组件
  Vue.component('button-counter', {
    data: function () {
      return {
        count: 0
      }
    },
    template: '<button v-on:click="count++">You clicked me {{ count }} times.</button>'
  })
  ```

  - prop可以在组件上注册的一些自定义特性

    ```
    Vue.component('blog-post', {
      props: ['title'],
      template: '<h3>{{ title }}</h3>'
    })
    ```

    ```
    <blog-post title="My journey with Vue"></blog-post>
    <blog-post title="Blogging with Vue"></blog-post>
    <blog-post title="Why Vue is so fun"></blog-post>
    ```

  - 调用内建的 [**$emit** 方法](https://cn.vuejs.org/v2/api/#vm-emit)并传入事件的名字，来向父级组件触发一个事件： 
    ```
    <button v-on:click="$emit('enlarge-text')">
      Enlarge text
    </button>
    ```
    然后我们可以用 `v-on` 在博文组件上监听这个事件，就像监听一个原生 DOM 事件一样 
    ```
    <blog-post
    ...
    v-on:enlarge-text="postFontSize += 0.1"
    ></blog-post>
    ```

------

### 组件注册

- 全局注册

  ```
  Vue.component('my-component-name', {
    // ... 选项 ...
  })
  ```

- 局部注册

  定义组件

  ```
  var ComponentA = { /* ... */ }
  var ComponentB = { /* ... */ }
  var ComponentC = { /* ... */ }
  ```

  使用组件
  ```
  new Vue({
  el: '#app',
  components: {
    'component-a': ComponentA,
    'component-b': ComponentB
  }
  })
  ```


**全局注册的行为必须在根 Vue 实例 (通过 new Vue) 创建之前发生** 

------

### 自定义事件