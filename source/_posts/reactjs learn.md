---
title: reactjs learn
date: 2019-03-29 13:53:30
tags: learn
categories: react
---

> Like any unfamiliar technology, React does have a learning curve. With practice and some patience, you *will* get the hang of it.

- React also streamlines how data is stored and handled, using **state** and **props**.

### create react app

- `npm install -g create-react-app`
- `npx create-react-app my-app` or `npm init react-app my-app`

## JSX: JavaScript + XML

> Babel compiles JSX down to `React.createElement()` calls.

jsx

```jsx
const heading = <h1 className="site-heading">Hello, React</h1>
```

non-jsx

```react
const heading = React.createElement('h1', { className: 'site-heading' }, 'Hello, React!')
```

the feature of jsx:

- `className` is used instead of `class` for adding CSS classes, as `class` is a reserved keyword in JavaScript.
- Properties and methods in JSX are camelCase - `onclick` will become `onClick`.
- Self-closing tags *must* end in a slash - e.g. `<img />`

## Components

> Whether you declare a component [as a function or a class](https://reactjs.org/docs/components-and-props.html#function-and-class-components), it must never modify its own props. 

### Function

The simplest way to define a component is to write a JavaScript function:

```js
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

### Class Components 

```js
import React, { Component } from 'react'

class Table extends Component {
  render() {
    return (
      <table>
        <thead>
          <tr>
            <th>Name</th>
            <th>Job</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td>Charlie</td>
            <td>Janitor</td>
          </tr>
        </tbody>
      </table>
    )
  }
}

export default Table
```

### Simple Components

> The other type of component in React is the **simple component**, which is a function. This component doesn't use the `class` keyword. Let's take our `Table` and make two simple components for it - a table header, and a table body.

```js
const TableHeader = () => {
  return (
    <thead>
      <tr>
        <th>Name</th>
        <th>Job</th>
      </tr>
    </thead>
  )
}
```

```js
const TableBody = () => {
  return (
    <tbody>
      <tr>
        <td>Charlie</td>
        <td>Janitor</td>
      </tr>
    </tbody>
  )
}
```

```js
class Table extends Component {
  render() {
    return (
      <table>
        <TableHeader />
        <TableBody />
      </table>
    )
  }
}
```

---

Everything should appear as it did before. As you can see, components can be nested in other components, and simple and class components can be mixed.

**A class component must include `render()`, and the `return` can only return one parent element.**

### Converting a Function to a Class

convert a function component to a class in five steps:

1. Create an [ES6 class](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes), with the same name, that extends `React.Component`.
2. Add a single empty method to it called `render()`.
3. Move the body of the function into the `render()` method.
4. Replace `props` with `this.props` in the `render()` body.
5. Delete the remaining empty function declaration.

## Props

> Props are an effective way to pass existing data to a React component, however the component cannot change the props - they're read-only. 

- *props* are a way of passing data from parent to child.

## State

State is similar to props, but it is private and fully controlled by the component.

State is reserved only for interactivity, that is, data that changes over time. 

> You can think of state as any data that should be saved and modified without necessarily being added to a database - for example, adding and removing items from a shopping cart before confirming your purchase.

- You must use `this.setState()` to modify an array. Simply applying a new value to `this.state.property` will not work.

   ie.

  ```js
  // Wrong
  this.state.comment = 'Hello';
  ```

  ```js
  // Correct
  this.setState({comment: 'Hello'});
  ```

tips:

Because `this.props` and `this.state` may be updated asynchronously, you should not rely on their values for calculating the next state.

ie.

```js
// Wrong
this.setState({
  counter: this.state.counter + this.props.increment,
});
```
use a second form of `setState()` that accepts a function rather than an object. That function will receive the previous state as the first argument, and the props at the time the update is applied as the second argument:

```js
// Correct
this.setState((state, props) => ({
  counter: state.counter + props.increment
}));
```

### The Data Flows Down

> If you imagine a component tree as a waterfall of props, each component’s state is like an additional water source that joins it at an arbitrary point but also flows down.

Neither parent nor child components can know if a certain component is stateful or stateless, and they shouldn’t care whether it is defined as a function or a class.

This is why state is often called local or encapsulated. It is not accessible to any component other than the one that owns and sets it.

This is commonly called a “top-down” or “unidirectional” data flow. Any state is always owned by some specific component, and any data or UI derived from that state can only affect components “below” them in the tree.

## Lifecycle methods

### constructor

### componentDidMount

> The `componentDidMount()` method runs after the component output has been rendered to the DOM. 

### componentWillUnmount

```js
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }

  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  tick() {
    this.setState({
      date: new Date()
    });
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

Let’s quickly recap what’s going on and the order in which the methods are called:

1. When `<Clock />` is passed to `ReactDOM.render()`, React calls the constructor of the `Clock`component. Since `Clock` needs to display the current time, it initializes `this.state` with an object including the current time. We will later update this state.
2. React then calls the `Clock` component’s `render()` method. This is how React learns what should be displayed on the screen. React then updates the DOM to match the `Clock`’s render output.
3. When the `Clock` output is inserted in the DOM, React calls the `componentDidMount()` lifecycle method. Inside it, the `Clock` component asks the browser to set up a timer to call the component’s `tick()` method once a second.
4. Every second the browser calls the `tick()` method. Inside it, the `Clock` component schedules a UI update by calling `setState()` with an object containing the current time. Thanks to the `setState()` call, React knows the state has changed, and calls the `render()` method again to learn what should be on the screen. This time, `this.state.date` in the `render()` method will be different, and so the render output will include the updated time. React updates the DOM accordingly.
5. If the `Clock` component is ever removed from the DOM, React calls the `componentWillUnmount()` lifecycle method so the timer is stopped.

总结：constructor->render()->componentDidMount()

state改变->render()

### componentWillReceiveProps

componentWillReceiveProps在初始化render的时候不会执行，它会在Component接受到新的状态(Props)时被触发，一般用于父组件状态更新时子组件的重新渲染。

在componentWillReceiveProps中想作任何变更最好都将两个状态进行比较，假如状态有异才执行下一步。不然容易造成组件的多次渲染，并且这些渲染都是没有意义的。

## Events

## Conditional Rendering

> Also remember that whenever conditions become too complex, it might be a good time to [extract a component](https://reactjs.org/docs/components-and-props.html#extracting-components).

- Returning `null` from a component’s `render` method does not affect the firing of the component’s lifecycle methods. For instance `componentDidUpdate` will still be called.

## Lists and Keys

> Keep in mind that if the `map()` body is too nested, it might be a good time to [extract a component](https://reactjs.org/docs/components-and-props.html#extracting-components).

## Forms

## Lifting State Up

## Composition vs Inheritance

- Remember that components may accept arbitrary props, including primitive values, React elements, or functions.

## Thinking in React

- You can build top-down or bottom-up. That is, you can either start with building the components higher up in the hierarchy or with the ones lower in it. In simpler examples, it’s usually easier to go top-down, and on larger projects, it’s easier to go bottom-up and write tests as you build.
- **Remember**: React is all about one-way data flow down the component hierarchy. 