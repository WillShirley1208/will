---
title: redux learn
date: 2019-04-07 23:50:30
tags: learn
categories: redux
---

### Passing Data Between Adjacent Components

If you have components that are siblings and need to share data, the way to do that in React is to pull that data up into a parent component and pass it down with props.

That can be cumbersome though. Redux can help by giving you one global “parent” where you can store the data, and then you can `connect` the sibling components to the data with React-Redux.

### redux vs react-redux

`redux` knows nothing about React *at all*. (The `redux` library can be used outside of a React app too. It’ll work with Vue, Angular, and even backend Node/Express apps.)

`react-redux` lets you connect pieces of the state to React components.

### store

in charge for orchestrating all the interactions

the **state** is the data, and the **store** is where it’s kept.

### reducer 

the producer to make the state

> (state, action) => newState

```js
var letters = ['r', 'e', 'd', 'u', 'x'];

// `reduce` takes 2 arguments:
//   - a function to do the reducing (you might say, a "reducer")
//   - an initial value for accumulatedResult
var word = letters.reduce(
  function(accumulatedResult, arrayItem) {
    return accumulatedResult + arrayItem;
  },
''); // <-- notice this empty string argument: it's the initial value

console.log(word) // => "redux"
```

**Important Rule of Reducers **:

- Never return undefined from a reducer.
- Reducers must be pure functions.(This means that they can’t modify their arguments, and they can’t have side effects.)

### action

plain JavaScript objects with a property named type. 

> An action object describes a change you want to make (like “please increment the counter”) or an event that happenend (like “the request to the server failed with this error”).
>
> action don’t really *do* anything. Not on their own, anyway.

```js
{
  type: "add an item",
  item: "Apple"
}

or

{
  type: 7008
}

or

{
  type: "INCREMENT"
}
```

In order to make an action DO something, you need to **dispatch** it.

---

### redux tips

#### Redux avoids these problems with some simple rules.

- State is read-only, and actions are the only way to modify it.
- Changes happen one way, and one way only: dispatch(action) -> reducer -> new state.
- The reducer function must be “pure” – it cannot modify its arguments, and it can’t have side effects.

#### The most important methods.

- getState for **accessing the current state** of the application
- dispatch for **dispatching an action**
- subscribe for **listening on state changes**

### react-redux

#### Provider

By wrapping the entire app with the `Provider` component, *every component* in the app tree will be able to access the Redux store if it wants to.

#### connect

all of components can access the Redux store, but not automatically , We’ll need to use the `connect` function on our components to access the store.

#### connects a React component with the Redux store.

- the mapStateToProps function
- the mapDispatchToProps function

```js
import React, { Component } from "react";
import logo from "./logo.svg";
import "./App.css";

import { connect } from "react-redux";

class App extends Component {
  render() {
    const { fetching, dog, onRequestDog, error } = this.props;
    
    return (
      <div className="App">
        ......
      </div>
    );
  }
}

const mapStateToProps = state => {
  return {
    fetching: state.fetching,
    dog: state.dog,
    error: state.error
  };
};

const mapDispatchToProps = dispatch => {
  return {
    onRequestDog: () => dispatch({ type: "API_CALL_REQUEST" })
  };
};

export default connect(mapStateToProps, mapDispatchToProps)(App);
```



### redux-thunk

It is a **middleware**, basically a plugin for Redux, that enables Redux to deal with actions like `getUser()`, ie.`dispatch(getUser())`

thunk: it’s an action creator that returns a function instead of a plain action object, like this:

```js
function doStuff() {
  return function(dispatch, getState) {
    // dispatch actions here
    // or fetch data
    // or whatever
  }
}
```

the 2 agruments in return function,Most of the time you’ll only need `dispatch`, but sometimes you want to do something conditionally, based on some value in the Redux state. In that case, call `getState()` and you’ll have the entire state to read as needed.

#### setup redux thunk

```js
import thunk from 'redux-thunk';
import { createStore, applyMiddleware } from 'redux';

function reducer(state, action) {
  // ...
}

const store = createStore(
  reducer,
  applyMiddleware(thunk)
);
```

### redux-saga

`redux-saga` relies on generators, but does a decent amount of the work for us, so (in my fairly limited experience) a deep understanding of them for this use-case isn’t necessary.

```js
import { takeLatest, call, put } from "redux-saga/effects";
import axios from "axios";

// watcher saga: watches for actions dispatched to the store, starts worker saga
export function* watcherSaga() {
  yield takeLatest("API_CALL_REQUEST", workerSaga);
}

// function that makes the api request and returns a Promise for response
function fetchDog() {
  return axios({
    method: "get",
    url: "https://dog.ceo/api/breeds/image/random"
  });
}

// worker saga: makes the api call when watcher saga sees the action
function* workerSaga() {
  try {
    const response = yield call(fetchDog);
    const dog = response.data.message;

    // dispatch a success action to the store with the new dog
    yield put({ type: "API_CALL_SUCCESS", dog });
  
  } catch (error) {
    // dispatch a failure action to the store with the error
    yield put({ type: "API_CALL_FAILURE", error });
  }
}
```

