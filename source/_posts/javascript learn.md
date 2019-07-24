---
title: javascript learn
date: 2019-03-29 12:01:30
tags: learn
categories: javascript
---

### types

- [`Number`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number)

- [`String`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)

- [`Boolean`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Boolean)

  > false, 0, empty strings (""), NaN, null, and undefined all become false.

- [`Symbol`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol) (new in ES2015)

- `Object`

  - [`Function`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function)
  - [`Array`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)
  - [`Date`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)
  - [`RegExp`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp)

- [`null`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/null)

- [`undefined`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/undefined)

  > declare a variable without assigning a value to it. If you do this, the variable's type is `undefined`. `undefined` is actually a constant.

### Variables

- let

  > **let** allows you to declare block-level variables. The declared variable is available from the *block* it is enclosed in.

- const

  > **const** allows you to declare variables whose values are never intended to change. The variable is available from the *block* it is declared in.

- var

  > A variable declared with the **var** keyword is available from the written out *function* (as opposed to an anonymous function) it is declared in.

### Operators

> `+`, `-`, `*`, `/` , `%` ,`=`,`+=` and `-=`

### Control structures

- Conditional statements are supported by `if` and `else`

-  `while` loops and `do-while` loops.

-  [`for` loop](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for) is the same as that in C and Java

  ```js
  for (var i = 0; i < 5; i++) {
    // Will execute 5 times
  }
  
  for (let value of array) {
    // do something with value
  }
  
  for (let property in object) {
    // do something with object property
  }
  ```

- `&&` and `||` operators

-  a ternary operator for conditional expressions:

  `var allowed = (age > 18) ? 'yes' : 'no';`

-  `switch` statement 

### Objects

> objects can be thought of as simple collections of name-value pairs, like HashMaps in Java.
>
> The "name" part is a JavaScript string, while the value can be any JavaScript value — including more objects.

- two basic ways to create an empty object:

  ```js
  var obj = new Object();
  var obj = {};
  ```

### Arrays

```js
var a = new Array();
a[0] = 'dog';
a[1] = 'cat';
a[2] = 'hen';

var b = ['dog', 'cat', 'hen'];

a.length; // 3
b.length;//3
```

- terating over an array

  ```js
  ['dog', 'cat', 'hen'].forEach(function(currentValue, index, array) {
    // Do something with currentValue or array[index]
  });
  ```

### Functions

> A JavaScript function can take 0 or more named parameters. The function body can contain as many statements as you like and can declare its own variables which are local to that function. The `return` statement can be used to return a value at any time, terminating the function. If no return statement is used (or an empty return with no value), JavaScript returns `undefined`.

```js
function avg(...args) {
  var sum = 0;
  for (let value of args) {
    sum += value;
  }
  return sum / args.length;
}

//above defined is equivalent to below

var avg = function() {
  var sum = 0;
  for (var i = 0, j = arguments.length; i < j; i++) {
    sum += arguments[i];
  }
  return sum / arguments.length;
};

avg(2, 3, 4, 5); // 3.5
```

### Custom objects

> JavaScript uses functions as classes. 

- the way to attach a function to an object.

  ```js
  function makePerson(first, last) {
    return {
      first: first,
      last: last,
      fullName: function() {
        return this.first + ' ' + this.last;
      },
      fullNameReversed: function() {
        return this.last + ', ' + this.first;
      }
    };
  }
  
  var s = makePerson('Simon', 'Willison');
  s.fullName(); // "Simon Willison"
  s.fullNameReversed(); // "Willison, Simon"
  ```

  good:

  ```js
  function Person(first, last) {
    this.first = first;
    this.last = last;
    this.fullName = function() {
      return this.first + ' ' + this.last;
    };
    this.fullNameReversed = function() {
      return this.last + ', ' + this.first;
    };
  }
  var s = new Person('Simon', 'Willison');
  ```

  better:

  ```js
  function personFullName() {
    return this.first + ' ' + this.last;
  }
  function personFullNameReversed() {
    return this.last + ', ' + this.first;
  }
  function Person(first, last) {
    this.first = first;
    this.last = last;
    this.fullName = personFullName;
    this.fullNameReversed = personFullNameReversed;
  }
  ```

  best:

  ```js
  function Person(first, last) {
    this.first = first;
    this.last = last;
  }
  Person.prototype.fullName = function() {
    return this.first + ' ' + this.last;
  };
  Person.prototype.fullNameReversed = function() {
    return this.last + ', ' + this.first;
  };
  ```

- `this` refers to the current object. 

  > What that actually means is specified by the way in which you called that function.
  >
  >  If you called it using dot notation or bracket notation on an object, that object becomes `this`.
  >
  >  If dot notation wasn't used for the call, `this` refers to the global object.

- `new` is strongly related to `this`.

  > Functions that are designed to be called by `new` are called constructor functions. Common practice is to capitalize these functions as a reminder to call them with `new`.

- `Person.prototype` is an object shared by all instances of `Person`.

  > **prototype chain**:any time you attempt to access a property of `Person` that isn't set, JavaScript will check `Person.prototype` to see if that property exists there instead. As a result, anything assigned to `Person.prototype` becomes available to all instances of that constructor via the `this` object.

  - JavaScript lets you modify something's prototype at any time in your program, which means you can add extra methods to existing objects at runtime:

    ```js
    var s = new Person('Simon', 'Willison');
    s.firstNameCaps(); // TypeError on line 1: s.firstNameCaps is not a function
    
    Person.prototype.firstNameCaps = function() {
      return this.first.toUpperCase();
    };
    s.firstNameCaps(); // "SIMON"
    ```

  - the prototype forms part of a chain. The root of that chain is `Object.prototype`, whose methods include `toString()` — it is this method that is called when you try to represent an object as a string. 

- The **call()** method calls a function with a given `this` value and arguments provided individually.

  ```js
  function Product(name, price) {
    this.name = name;
    this.price = price;
  }
  
  function Food(name, price) {
    Product.call(this, name, price);
    this.category = 'food';
  }
  
  console.log(new Food('cheese', 5).name);
  // expected output: "cheese"
  ```

- The **apply()** method calls a function with a given `this` value, and `arguments` provided as an array (or an [array-like object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Indexed_collections#Working_with_array-like_objects)).

  ```js
  var numbers = [5, 6, 2, 3, 7];
  
  var max = Math.max.apply(null, numbers);
  
  console.log(max);
  // expected output: 7
  
  var min = Math.min.apply(null, numbers);
  
  console.log(min);
  // expected output: 2
  ```

#### Inner functions

```js
function parentFunc() {
  var a = 1;

  function nestedFunc() {
    var b = 4; // parentFunc can't use this
    return a + b; 
  }
  return nestedFunc(); // 5
}
```

### Closures

```js
function makeAdder(a) {
  return function(b) {
    return a + b;
  };
}
var x = makeAdder(5);
var y = makeAdder(20);
x(6); // returns 11
y(7); // returns 27
```

> Scope objects form a chain called the scope chain, similar to the prototype chain used by JavaScript's object system.



## Other

**curly braces**

括号（）

**Spread syntax**

省略号

- The spread syntax is simply three dots: `...`
- It allows an iterable to expand in places where 0+ arguments are expected.

#### asterisk

星号 *

### regular expression

- 校验含有空格

  `reg = /^((?! ).)*$/;`

- 校验只有数字、字母和下划线

  `reg = /^[_0-9a-z]*$/;`

### setInterval

定时调度