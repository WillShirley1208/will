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

​	jsx

```jsx
const heading = <h1 className="site-heading">Hello, React</h1>
```

​	non-jsx

```react
const heading = React.createElement('h1', { className: 'site-heading' }, 'Hello, React!')
```

the feature of jsx:

- `className` is used instead of `class` for adding CSS classes, as `class` is a reserved keyword in JavaScript.
- Properties and methods in JSX are camelCase - `onclick` will become `onClick`.
- Self-closing tags *must* end in a slash - e.g. `<img />`

## Components

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

A class component must include `render()`, and the `return` can only return one parent element.

## Props

> Props are an effective way to pass existing data to a React component, however the component cannot change the props - they're read-only. 

## State

> You can think of state as any data that should be saved and modified without necessarily being added to a database - for example, adding and removing items from a shopping cart before confirming your purchase.

- You must use `this.setState()` to modify an array. Simply applying a new value to `this.state.property` will not work.