---
title: GO learn
date: 2024-07-28 15:08:28
tags: learn
categories: GO
---

## Syntax

### sclice

- syntax `slice[low:high]`
- "take from 1 to the end" with `numbers[1:]`.

### test

- `go test -v`
- `go test -cover`

### error

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

### pointer

> **when you call a function or a method the arguments are** ***copied***.

```go
//When calling func (w Wallet) Deposit(amount int) the w is a copy of whatever we called the method from.
//*Wallet rather than Wallet which you can read as "a pointer to a wallet
func (w *Wallet) Balance() int {
	return (*w).balance
}
```

- 不可寻址的情况：

1. **不可变的**值不可寻址。常量、基本类型的值字面量、字符串变量的值、函数以及方法的字面量都是如此。其实这样规定也有安全性方面的考虑。
2. 绝大多数被视为**临时结果**的值都是不可寻址的。算术操作的结果值属于临时结果，针对值字面量的表达式结果值也属于临时结果。但有一个例外，对切片字面量的索引结果值虽然也属于临时结果，但却是可寻址的。
3. 若拿到某值的指针可能会破坏程序的一致性，那么就是**不安全的**，该值就不可寻址。由于字典的内部机制，对字典的索引结果值的取址操作都是不安全的。另外，获取由字面量或标识符代表的函数或方法的地址显然也是不安全的。



### assert

```go
value, ok := interface{}(container).(map[int]string)
```

### data type

- 引用类型：切片、字典、通道、函数
- 值类型：数组、基础数据类型以及结构体类型

### pointer

- 

## tool

- errcheck

  ```go
  errcheck .
  ```


## concurrency

> Do not communicate by sharing memory; instead, share memory by communicating.

### sync

- `Mutex` allows us to add locks to our data

  > mutual exclusion，简称 mutex

- `WaitGroup` is a means of waiting for goroutines to finish jobs

- `RWMutex` 读写锁



## best practise

### directory

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



## Paraphrasing

- Use channels when passing ownership of data
- Use mutexes for managing state

- 名称的首字母为大写的程序实体才可以被当前包外的代码引用，否则它就只能被当前包内的其他代码引用。

- 一对不包裹任何东西的花括号，除了可以代表空的代码块之外，还可以用于表示不包含任何内容的数据结构（或者说数据类型）

- 函数类型属于引用类型，它的零值是`nil`。
- 结构体类型中的一个字段看作是它的一个属性或者一项数据，再把隶属于它的一个方法看作是附加在其中数据之上的一个能力或者一项操作。将属性及其能力（或者说数据及其操作）封装在一起，是面向对象编程（object-oriented programming）的一个主要原则。