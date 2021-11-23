---
title: dva learn
date: 2019-04-11 13:52:30
tags: learn
categories: dva
---



dva = React-Router + Redux + Redux-saga

- 路由： [React-Router](https://github.com/ReactTraining/react-router/tree/v2.8.1)
- 架构： [Redux](https://github.com/reactjs/redux)
- 异步操作： [Redux-saga](https://github.com/yelouafi/redux-saga)



![](./img/PPrerEAKbIoDZYr.png)



### State

tate 表示 Model 的状态数据

### Action

Action 是一个普通 javascript 对象，它是改变 State 的唯一途径。

- action 必须带有 `type` 属性指明具体的行为，其它字段可以自定义，如果要发起一个 action 需要使用 `dispatch` 函数；需要注意的是 `dispatch` 是在组件 connect Models以后，通过 props 传入的。

### dispatch

dispatching function 是一个用于触发 action 的函数

- action 是改变 State 的唯一途径，但是action只描述了一个行为，而 dipatch 可以看作是触发这个行为的方式，而 Reducer 则是描述如何改变数据的。

- connect Model 的组件通过 props 可以访问到 dispatch，可以调用 Model 中的 Reducer 或者 Effects.

### Reducer

Reducer函数接受两个参数：之前已经累积运算的结果和当前要被累积的值，返回的是一个新的累积结果。该函数把一个集合归并成一个单值。

- 在 dva 中，reducers 聚合积累的结果是当前 model 的 state 对象。通过 actions 中传入的值，与当前 reducers 中的值进行运算获得新的值（也就是新的 state）。

### Effect

Effect 被称为副作用，在我们的应用中，最常见的就是异步操作。

- 它来自于函数编程的概念，之所以叫副作用是因为它使得我们的函数变得不纯，同样的输入不一定获得同样的输出。

- dva 为了控制副作用的操作，将异步转成同步写法，从而将effects转为纯函数

### Subscription

Subscriptions 是一种从 **源** 获取数据的方法，它来自于 elm。

- Subscription 语义是订阅，用于订阅一个数据源，然后根据条件 dispatch 需要的 action。



### 数据流图

![](./img/hUFIivoOFjVmwNXjjfPE.png)

- State 是储存数据的地方，收到 Action 以后，会更新数据。

- View 就是 React 组件构成的 UI 层，从 State 取数据后，渲染成 HTML 代码。只要 State 有变化，View 就会自动更新。

- Action 是用来描述 UI 层事件的一个对象。

  ```js
  dispatch({
    type: 'click-submit-button',
    payload: this.form.data
  })
  ```

- connect 是一个函数，绑定 State 到 View。

  ```js
  import { connect } from 'dva';
  
  function mapStateToProps(state) {
    return { todos: state.todos };
  }
  connect(mapStateToProps)(App);
  ```

  connect 方法返回的也是一个 React 组件，通常称为容器组件。因为它是原始 UI 组件的容器，即在外面包了一层 State。

- dispatch 是一个函数方法，用来将 Action 发送给 State。

  **被 connect 的 Component 会自动在 props 中拥有 dispatch 方法。**



### 数据流图二

![](./img/pHTYrKJxQHPyJGAYOzMu.png)

### Model 对象的属性

> 每个model，实际上都是普通的JavaScript对象

- namespace: 当前 Model 的名称。整个应用的 State，由多个小的 Model 的 State 以 namespace 为 key 合成
- state: 该 Model 当前的状态。数据保存在这里，直接决定了视图层的输出
- reducers: Action 处理器，处理同步动作，用来算出最新的 State
- effects：Action 处理器，处理异步动。dva 提供多个 effect 函数内部的处理函数，比较常用的是 `call` 和 `put`。
  - call：执行异步函数
  - put：发出一个 Action，类似于 dispatch

- subscriptions

