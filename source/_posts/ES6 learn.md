---
title: ES6 learn
date: 2019-04-04 11:35:30
tags: learn
categories: ES6
---

### Let

let is similar to var but let has scope. let is only accessible in the block level it is defined.

example:

```js
if (true) {
 let a = 40;
 console.log(a); //40
}
console.log(a); // undefined
```

and

```js
let a = 50;
let b = 100;
if (true) {
 let a = 60;
 var c = 10;
 console.log(a/c); // 6
 console.log(b/c); // 10
}
console.log(c); // 10
console.log(a); // 50
```

### Const

Const is used to assign a constant value to the variable. And the value cannot be changed. Its fixed.

example:

```js
const a = 50;
a = 60; // shows error. You cannot change the value of const.
const b = "Constant variable";
b = "Assigning new value"; // shows error.
```

Whenever you define a const variable, Javascript references the address of the value to the variable.

example:

```js
const LANGUAGES = ['Js', 'Ruby', 'Python', 'Go'];
LANGUAGES = "Javascript"; // shows error.
LANGUAGES.push('Java'); // Works fine.
console.log(LANGUAGES); // ['Js', 'Ruby', 'Python', 'Go', 'Java']
```

the variable ‘LANGUAGES’ actually references to the memory allocated to the array. So you cannot change the variable to reference some other memory location later. Throughout the program it only references to the array.

### Arrow Function

```js
// Old Syntax
function oldOne() {
 console.log("Hello World..!");
}
// New Syntax
var newOne = () => {
 console.log("Hello World..!");
}
```

and

```js
let NewOneWithParameters = (a, b) => {
 console.log(a+b); // 30
}
NewOneWithParameters(10, 20);
```

There are two things to explain here.

1. If you have a function with single parameter, you don’t need (). In our case **element** is the parameter.
2. And If you have single line as the body of the function you don’t need {} and also JS will implicitly returns the value after executing the function. You don’t have to use **return** keyword.
3. Arrow functions shine best with anything that requires `this` to be bound to the context, and not the function itself.

### For of loop

for..of iterates through list of elements (i.e) like Array and returns the elements (not their index) one by one.

```js
let arr = [2,3,4,1];
for (let value of arr) {
 console.log(value);
}
Output:
2
3
4
1
```

and

```js
let string = "Javascript";
for (let char of string) {
 console.log(char);
}
Output:
J
a
v
a
s
c
r
i
p
t
```

### Spread attributes

```js
let SumElements = (arr) => {
 	console.log(arr); // [10, 20, 40, 60, 90]
 	
	let sum = 0;
 	for (let element of arr) {
 		sum += element;
 	}
 	console.log(sum); // 220. 
}

SumElements([10, 20, 40, 60, 90]);
```

consider the same example with spread attributes:

```js
let SumElements = (...arr) => {
 console.log(arr); // [10, 20, 40, 60, 90]
let sum = 0;
 for (let element of arr) {
 sum += element;
 }
 console.log(sum); // 220. 
}
SumElements(10, 20, 40, 60, 90); // Note we are not passing array here. Instead we are passing the elements as arguments.
```

Math.max is a simple method that returns the maximum element from given list. It doesn’t accept an array.

```js
let arr = [10, 20, 60];
Math.max(arr); // Shows error. Doesn't accept an array.
```

So lets use our savior:

```js
let arr = [10, 20, 60];
Math.max(...arr); // 60
```

### Maps

Map holds key-value pairs. It’s similar to an array but we can define our own index. And indexes are unique in maps.

```js
var map = new Map();
map.set('name', 'John');
map.set('id', 10);

map.get('name'); // John
map.get('id'); // 10

map.size; // 2. Returns the size of the map.
map.keys(); // outputs only the keys. 
map.values(); // outputs only the values.

for (let key of map.keys()) {
 console.log(key);
}
Output:
name
id

for (let [key, value] of map) {
 console.log(key+" - "+value);
}
Output:
name - John
id - 10

```

### Sets

Sets are used to store the unique values of any type.

```js
var sets = new Set();
sets.add('a');
sets.add('b');
sets.add('a'); // We are adding duplicate value.
for (let element of sets) {
 console.log(element);
}
Output:
a
b

sets.size; // returns 2. Size of the set.
sets.has('a'); // returns true. 
sets.has('c'); // returns false.
```

### Static methods

```js
class Example {
 static Callme() {
 console.log("Static method");
 }
}
Example.Callme();
Output:
Static method
```

 you can call the function without creating any instance for the class.

### Getters and Setters

```js
class People {
constructor(name) {
 this.name = name;
 }
 getName() {
 return this.name;
 }
 setName(name) {
 this.name = name;
 }
}
let person = new People("Jon Snow");
console.log(person.getName());
person.setName("Dany");
console.log(person.getName());
Output:
Jon Snow
Dany
```

### Promises

 They are used to make **async** operations such as API request, file handling, downloading images, etc.

Before promises, programmers used to define **callbacks.** Callbacks are normal functions in Javascript which executes when the async operation is complete.

- three states in promises

  1. **Pending**: In this state the promise is just executing the async operation. For example, It’s making some API request to the server or downloading some images from cdn.  from this state promise can move to either to **Fulfilled** or to **Rejected**
  2. **Fulfilled**: If the promise has reached this state, then it means that the async operation is complete and we have the output. For example, we have the response from the API.
  3. **Rejected:** If the promise has reached this state, it means that the async operation is not successful and we have the error which caused the operation to fail.

  ```js
  const apiCall = new Promise(function(resolve, reject) {
   if ( API request to get some data ) {
    resolve("The request is successful and the response is "+ response);
   }
   else {
    reject("The request is not successful. The error is "+error);
   }
  });
  ```

  Then the **resolve** function is called if we get the response from the server. And if there is some error **reject** function is called with the error message.

- We use **handlers** to get the output from the promise.

  >Handlers are just functions which executes when some event occurs such as clicking a button, moving the cursor, etc.
  >
  >So we can use handlers to handle when the **resolve** function is called or **reject **function is called.

  - The handler **then** executes its **function parameter** when the **resolve** function is called inside the promise.

  ```js
  // calling the promise with some handlers.
  apiCall.then(function(x) {console.log(x); })
  
  // Output
  The request is successful and the response is {name: "Jon Snow"}
  
  ```

  - **Catch** handler looks out for **reject** function.

  ```js
  apiCall
  .then(function(x) {
   console.log(x); 
  })
  .catch(function(x) {
   console.log(x);
  }) 
  
  // Assuming the request is not successful ( reject function is called in the promise. )
  Output:
  The request is not successful
  ```

  

### Async / Await

#### async

```js
async function hello() {
 return "Hello Promise..!"
}
```

The above code is equivalent to the below code:

```js
function hello() {
 return new Promise(function(resolve, reject) {
 // executor function body.
 });
}
```

example:

```js
async function hello(a, b) {
 if (a < b) {
  return "Greater";
 }
 else {
  return new Error("Not Greater");
 }
}
hello(14, 10)
.then(function(x) {
 console.log("Good..! " + x); 
})
.catch(function(x) {
 console.log("Oops..! " + x); 
})
Output:
Oops..! Not Greater. 
// if you call hello(4, 10) you get "Good..! Greater"
```

Don’t forget that async function will return a promise. So of course, you can call **resolve** and **reject** function inside **async** function too. 

example:

```js
async function Max(a, b) {
 if (a > b) {
  return Promise.resolve("Success");
 }
 else {
  return Promise.reject("Error");
 }
}
Max(4, 10)
.then(function(x) {
 console.log("Good " + x); 
})
.catch(function(x) {
 console.log("Oops " + x); 
});
Output:
Oops Error
// If we pass Max(14, 10) then we should get "Good Success" :)
```

#### await

 It makes the Javascript to wait until you get the response from the endpoint. And then it resumes the execution.

**await can be used only inside async function. It doesn’t work outside async function**

```js
async function hello() {
 let response = await fetch('https://api.github.com/');
 // above line fetches the response from the given API endpoint.
 return response;
}
hello()
.then(function(x) {
 console.log(x); 
});
...
...
Output:
Response from the API.
```

### Array

#### Array Map

```js
let arr = [1,2,3,4,5];
let modifiedArr = arr.map(function(element, index, arr) {
  return element * 10;
});
console.log(modifiedArr);
Output:
[10, 20, 30, 40, 50]
```

And also note we have to return some value in the end. Which will be the modified value of that element. If you didn’t return anything then the particular element will be undefined.

One more thing I like to add is the second and third parameter is only optional. Only the first parameter is mandatory. for example :

```js
let modifiedArr = arr.map(function(element) {
  return element * 10;
});
```

write the map operator with arrow functions:

```js
let modifiedArr = arr.map((element, index) => {
  console.log("index "+index);
  return element * 10;
});
console.log(modifiedArr);
Output:
index 0
index 1
index 2
index 3
index 4
[10, 20, 30, 40, 50]
```

better:

```js
let modifiedArr = arr.map(element => element * 10);
console.log(modifiedArr);
```

#### Array Filter

```js
let arr = [1, 2, 3, 4, 5, 6]
let modifiedArr = arr.filter(function(element, index, array) {
  return element % 2 == 0
});
console.log(modifiedArr);
Output:
[2, 4, 6]
```

try by arrow function:

```js
let modifiedAarr = arr.filter((element, index) => element%2 == 0)
```

we have to return a boolean value for each element of the array. If you won’t return any boolean value at the end then the filter takes it as **false** and deletes the element.

#### Array Reduce

Array reduce is used to aggregate all the elements of an array and return a single value.

```js
let arr = [1,2,3,4,5,6]
let total= arr.reduce(function(sum, element, index, array) {
  return sum + element;
},0);
console.log("total is "+total);
Output:
total is 21
```

Unlike filter and map, reduce takes a function with four parameters and also a additional element. Unlike filter and map, the first two parameters are mandatory. Other two are optional.

The first parameter is the aggregator element.In our case it’s 0. 

Like filter and map you have to return the end result.

write the same code with arrow functions:

```js
let totalSum = arr.reduce((sum, element) => element+sum, 0)
```

### Template Literals

```js
let name = "Srebalaji";
let languages = () => {return "Ruby, Js, Java, Python"}
let msg = `My name is ${name}
My age is ${20+3}
And I code in ${languages()}`
Output:
My name is Srebalaji
My age is 23
And I code in Ruby, Js, Java, Python
```

### Imports and Exports

```js
//app.js
export let name = "Jon"
export let age = 23

//index.js
import {name, age} from './app'
console.log(name);
console.log(age);

//index.html
<script src="./index.js"></script>

Output:
Jon
23
```

and:

```js
//app.js
let a = 10;
let b = 2;
let sum = () => a+b;
export {a,b}
export default sum

//index.js
import * as variables from './app'
import addition from './app' // default value
console.log(variables.a);
console.log(variables.b);
console.log(addition());

Output:
10
2
12
```

1. If you are using * to import values then you have to use **alias** (i.e) names that will refer to imported values. In our example we have used **variables** as alias.

2. Using * to import values doesn’t import default value. You have to import it separately.

   for example:`import addition, * as variables from './app'`

### Destructuring objects and arrays

```js
let person = {firstName: "Jon", lastName: "Snow", age: 23}
const {firstName, lastName, age} = person
console.log(firstName);
console.log(lastName);
console.log(age);
Output:
Jon
Snow
23
```

### Extend and Super

```js
class Person{
 constructor(firstName, lastName, age) {
   this.firstName = firstName;
   this.lastName = lastName;
   this.age = age;
 }
 displayName() {
  return `${this.firstName} - ${this.lastName}`;
 }
}
class Employee extends Person {
 constructor(firstName, lastName, age, salary) {
  super(firstName, lastName, age);
  this.salary = salary;
 }
 displaySalary() {
  return `${this.salary}`;
 }
 displayName() {
  return super.displayName();
 }
 displayAge() {
  return this.age;
 }
}
let manager = new Employee("Jon", "Snow", 23, 100);
console.log(manager.displaySalary());
console.log(manager.displayName());
console.log(manager.displayAge());
Output:
100
Jon Snow
23
```

And then we have used **super** keyword to call the constructor of the parent class. And we also have called the method declared in the parent class using **super**.

### Generator functions

- Generator functions are written using the [`function*`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*) syntax. When called initially, generator functions do not execute any of their code, instead returning a type of iterator called a Generator. 
- When a value is consumed by calling the generator's **next** method, the Generator function executes until it encounters the **yield** keyword.
  - Each `yield` in a generator basically represents an asynchronous step in a more synchronous/sequential process — somewhat like `await` in an `async`function.
- The function can be called as many times as desired and returns a new Generator each time, however each Generator may only be iterated once.

### some method

- includes

  construct an array of the items, and use [`includes`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/includes):

  ```js
  ['a', 'b', 'c'].includes('b')
  ```

