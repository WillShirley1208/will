---
title: GO learn
date: 2024-07-28 15:08:28
tags: learn
categories: GO
---

# env

- `GOPATH`

`GOPATH` 是 Go 工作区的根目录，通常包含三个子目录：`src`、`pkg` 和 `bin`

> 可通过 go env GOPATH 查看Go 工作区的根目录。`GOPATH` 中的 `bin` 目录通常包含由 `go install`安装的可执行文件

# Syntax

## sclice

> sclice selects a half-open range which includes the first element, but excludes the last one.

- syntax `slice[low:high]`
- "take from 1 to the end" with `numbers[1:]`.
- A slice does not store any data, it just describes a section of an underlying array. Changing the elements of a slice modifies the corresponding elements of its underlying array. Other slices that share the same underlying array will see those changes.

- The default is zero for the low bound and the length of the slice for the high bound.

  ```go
  var a [10]int
  # is equal below 
  a[0:10]
  a[:10]
  a[0:]
  a[:]
  ```

  

## Go's while

 C's `while` is spelled `for` in Go.

```go
for sum < 1000 {
		sum += sum
}
```

## error

-  errors are values, so we can refactor it out into a variable and have a single source of truth for it.

  ```go
  var ErrInsufficientFunds = errors.New("cannot withdraw, insufficient funds")
  
  func (w *Wallet) Withdraw(amount Bitcoin) error {
  	if amount > w.balance {
  		return ErrInsufficientFunds
  	}
  	w.balance -= amount
  	return nil
  }
  ```

- [Don’t just check errors, handle them gracefully](https://dave.cheney.net/2016/04/27/dont-just-check-errors-handle-them-gracefully)

## pointer

> A pointer holds the memory address of a value.
>
> The `&` operator **generates** a pointer to its operand.
>
> The `*` operator **denotes** the pointer's underlying value.

```go
	i := 42

	p := &i         // point to i
	fmt.Println(*p) // read i through the pointer
	*p = 21         // set i through the pointer
	fmt.Println(i)  // see the new value of i
```



**when you call a function or a method the arguments are** ***copied***.

```go
//When calling func (w Wallet) Deposit(amount int) the w is a copy of whatever we called the method from.
//*Wallet rather than Wallet which you can read as "a pointer to a wallet
func (w *Wallet) Balance() int {
	return (*w).balance
}

// Pointer receivers: Methods with pointer receivers can modify the value to which the receiver points. Since methods often need to modify their receiver, pointer receivers are more common than value receivers.
```

- 不可寻址的情况：

1. **不可变的**值不可寻址。常量、基本类型的值字面量、字符串变量的值、函数以及方法的字面量都是如此。其实这样规定也有安全性方面的考虑。
2. 绝大多数被视为**临时结果**的值都是不可寻址的。算术操作的结果值属于临时结果，针对值字面量的表达式结果值也属于临时结果。但有一个例外，对切片字面量的索引结果值虽然也属于临时结果，但却是可寻址的。
3. 若拿到某值的指针可能会破坏程序的一致性，那么就是**不安全的**，该值就不可寻址。由于字典的内部机制，对字典的索引结果值的取址操作都是不安全的。另外，获取由字面量或标识符代表的函数或方法的地址显然也是不安全的。

## assert

```go
value, ok := interface{}(container).(map[int]string)
```

## data type

- 引用类型：切片、字典、通道、函数
- 值类型：数组、基础数据类型以及结构体类型



# concurrency

> Do not communicate by sharing memory; instead, share memory by communicating.

## backstone

PMG stands for P (logical **p**rocessors), M (**m**achine threads), and G (**g**oroutines). The key point is that each logical processor (P) can only have one machine thread (M) running on it at any time. And for a goroutine (G) to run, it needs to be attached to a thread (M).

## sync

- `Mutex` allows us to add locks to our data

  > mutual exclusion，简称 mutex

- `WaitGroup` is a means of waiting for goroutines to finish jobs

- `RWMutex` 读写锁

## channel

> they provide a way to send data from one goroutine to another, ensuring smooth communication and synchronization in concurrent programs.

# best practise

## directory

```
project-root/
├── cmd/  # 包含项目的主要应用程序。每个应用程序都有一个单独的子目录。
│   └── myapp/
│       └── main.go  # 主应用程序入口点
├── internal/  # 包含不希望其他项目导入的包
│   ├── app/  # 应用程序的核心逻辑
│   │   └── app.go
│   ├── config/  # 配置相关代码
│   │   └── config.go
│   └── models/  # 数据模型
│       └── user.go
├── pkg/  # 可以被外部应用程序使用的库代码
│   ├── database/  # 数据库操作相关代码
│   │   └── database.go
│   └── utils/  # 通用工具函数
│       └── helper.go
├── api/  # API相关的代码，如果项目提供API
│   └── v1/
│       └── api.go
├── web/  # Web应用相关的文件，如模板和静态资产
│   ├── templates/
│   └── static/
├── scripts/  # 各种工具脚本，如数据库迁移脚本
│   └── migrate.go
├── tests/  # 单元测试和集成测试
│   └── app_test.go
├── docs/  # 项目文档
│   └── README.md
├── go.mod  # Go模块文件
├── go.sum
├── .gitignore
└── README.md
```

## dependency

```shell
# get depends
go get github.com/jackblack369/minio@feat-gateway
```



# Paraphrasing

- Use channels when passing ownership of data
- Use mutexes for managing state

- 名称的首字母为大写的程序实体才可以被当前包外的代码引用，否则它就只能被当前包内的其他代码引用。

- 一对不包裹任何东西的花括号，除了可以代表空的代码块之外，还可以用于表示不包含任何内容的数据结构（或者说数据类型）

- 函数类型属于引用类型，它的零值是`nil`。
- 结构体类型中的一个字段看作是它的一个属性或者一项数据，再把隶属于它的一个方法看作是附加在其中数据之上的一个能力或者一项操作。将属性及其能力（或者说数据及其操作）封装在一起，是面向对象编程（object-oriented programming）的一个主要原则。

# test

- `go test -v`
- `go test -cover`

```shell
# 测试指定目录下的某个方法
go test -v /path/to/package -run ^FuncName$
# e.g. go test -v ./http -run ^TestRunning$  # 注意 "./" 这个是必须的
```

# tool

- errcheck

  ```shell
  errcheck .
  ```

- format

  ```shell
  gofmt -w yourfile.go
  ```

  

# A vs B

- Golang's goroutine VS Python's coroutine

| **Aspect**            | **Go (Goroutines)**                                     | **Python (Coroutines)**                          |
| --------------------- | ------------------------------------------------------- | ------------------------------------------------ |
| **Concurrency Model** | Preemptive, managed by Go runtime (M:N threading)       | Cooperative, event-loop-based (async/await)      |
| **Execution Model**   | True concurrency, can run in parallel on multiple cores | Single-threaded, asynchronous (mostly I/O-bound) |
| **Syntax**            | `go func()`                                             | `async def` and `await`                          |
| **Task Management**   | Automatic, via Go runtime                               | Manual, via event loop (e.g., `asyncio`)         |
| **Performance**       | Efficient, ideal for both I/O-bound and CPU-bound tasks | Efficient for I/O-bound, not CPU-bound tasks     |
| **Error Handling**    | Requires manual management via channels or sync         | Propagates through `await`/event loop            |

**1. Scheduling:**

**Goroutines:** In Go, a goroutine is a lightweight thread that runs concurrently with other goroutines in the same process. The scheduler of the operating system determines which goroutine to run next.

 **Coroutines:** In Python, a coroutine is an asynchronous function that can suspend its execution at specific points and resume from there later. Coroutines are scheduled by the runtime environment (e.g., CPython) itself.

**2. Context switching:**

 **Goroutines:** In Go, context switching between goroutines is relatively cheap because goroutines share the same memory space as their parent process.

 **Coroutines:** In Python, context switching between coroutines can be more expensive because each coroutine has its own stack and local variables.

**3. Yielding control:**

 **Goroutines:** Go's goroutines do not have a concept of yielding control to another goroutine or task. Once a goroutine is scheduled, it runs until completion.

 **Coroutines:** Python coroutines can yield control to other coroutines using the `yield` keyword, allowing them to suspend and resume their execution at specific points.

**4. Looping:**

 **Goroutines:** Go's goroutines do not have a loop mechanism like Python's coroutines. Goroutines must be scheduled manually using `runtime.Gosched()` or other scheduling APIs.

 **Coroutines:** Python coroutines can use loops, such as `for` loops, to iterate over data and switch between different coroutine implementations.

**5. Error handling:**

 **Goroutines:** Go's goroutines handle errors internally when a panic occurs. The parent process receives the error through the `main` function.

 **Coroutines:** Python coroutines can also handle errors, but they typically use exceptions or try-except blocks to manage errors.

**6. Global variables:**

 **Goroutines:** Go's goroutines do not have access to global variables by default. They must be passed explicitly using channels or shared data structures.

 **Coroutines:** Python coroutines can access global variables, but it's generally recommended to avoid sharing mutable state between coroutines.
