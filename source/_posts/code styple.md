---
title: code style
date: 2024-08-14 10:51:25
tags: style
categories: code
---

# functional programming

## Golang

Go (Golang) supports functional programming concepts, although it is primarily an imperative language. Here are some key functional programming features in Go:

### 1. First-Class Functions
Functions in Go are first-class citizens, meaning they can be assigned to variables, passed as arguments, and returned from other functions.

```go
package main

import "fmt"

func add(a, b int) int {
    return a + b
}

func main() {
    var op func(int, int) int
    op = add
    fmt.Println(op(1, 2)) // Outputs: 3
}
```

### 2. Higher-Order Functions
Functions that take other functions as arguments or return functions are called higher-order functions.

```go
package main

import "fmt"

func apply(op func(int, int) int, a, b int) int {
    return op(a, b)
}

func main() {
    add := func(a, b int) int { return a + b }
    fmt.Println(apply(add, 1, 2)) // Outputs: 3
}
```

### 3. Closures
Go supports closures, which are functions that capture the variables from their surrounding scope.

```go
package main

import "fmt"

func incrementer() func() int {
    i := 0
    return func() int {
        i++
        return i
    }
}

func main() {
    inc := incrementer()
    fmt.Println(inc()) // Outputs: 1
    fmt.Println(inc()) // Outputs: 2
}
```

### 4. Pure Functions
Pure functions are functions that do not have side effects and return the same result given the same arguments.

```go
package main

import "fmt"

func add(a, b int) int {
    return a + b
}

func main() {
    fmt.Println(add(1, 2)) // Outputs: 3
}
```

### 5. Immutability
While Go does not enforce immutability, you can design your functions to avoid mutating state.

```go
package main

import "fmt"

func addToSlice(slice []int, value int) []int {
    newSlice := append(slice, value)
    return newSlice
}

func main() {
    original := []int{1, 2, 3}
    newSlice := addToSlice(original, 4)
    fmt.Println(original) // Outputs: [1, 2, 3]
    fmt.Println(newSlice) // Outputs: [1, 2, 3, 4]
}
```

### 6. Functional Composition
You can compose functions to build more complex operations.

```go
package main

import "fmt"

func add(a, b int) int {
    return a + b
}

func multiply(a, b int) int {
    return a * b
}

func compose(f, g func(int, int) int) func(int, int) int {
    return func(a, b int) int {
        return f(g(a, b), b)
    }
}

func main() {
    addThenMultiply := compose(multiply, add)
    fmt.Println(addThenMultiply(2, 3)) // Outputs: 15 (5 * 3)
}
```

These features allow you to use functional programming techniques in Go, making your code more modular, reusable, and easier to reason about.

# tips

- 函数式程序更倾向于铺设调节数据流转换的管道结构，而可变的命令式程序更倾向于迭代地处理一个个类型对象