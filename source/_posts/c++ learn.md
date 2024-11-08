---
title: c++ learn
date: 2024-09-08 17:51:34
tags: learn
categories: c++
---

# philosophy

The underlying design philosophy of C and C++ can be summed up as “trust the programmer” -- which is both wonderful and dangerous. C++ is designed to allow the programmer a high degree of freedom to do what they want. However, this also means the language often won’t stop you from doing things that don’t make sense, because it will assume you’re doing so for some reason it doesn’t understand. There are quite a few pitfalls that new programmers are likely to fall into if caught unaware. This is one of the primary reasons why knowing what you shouldn’t do in C/C++ is almost as important as knowing what you should do.

Typically, good solutions have the following characteristics:

- They are straightforward (not overly complicated or confusing).
- They are well documented (especially around any assumptions being made or limitations).
- They are built modularly, so parts can be reused or changed later without impacting other parts of the program.
- They can recover gracefully or give useful error messages when something unexpected happens.

# compiler

- **Build** compiles all *modified* code files in the project or workspace/solution, and then links the object files into an executable. If no code files have been modified since the last build, this option does nothing.

  - compile

  <img src="/images/c++/CompileSource.png" style="zoom:100%;"/>

  - linking

  <img src="/images/c++/LinkingObjects.webp">

  **g++（GNU Compiler Collection for C++）**

  `g++` 是 GNU 编译器集合（GNU Compiler Collection, GCC）的一部分，专门用于编译 C++ 程序。GCC 是由 GNU 项目开发的开源编译器，支持多种编程语言，包括 C、C++、Objective-C、Fortran、Ada 和 Go 等。

  ```shell
  # use
  g++ -o my_program my_program.cpp
  
  # specify compiler standard
  g++ --std=gnu++17 {filename}.cpp
  ```

  **clang++（Clang C++ Frontend）**

  `clang++` 是 Clang 项目的一部分，Clang 是一个由苹果公司主导开发的编译器前端，用于替代 GCC。它不仅支持 C++，还支持 C、Objective-C 和其他语言。

  ```shell
  # use
  clang++ -o my_program my_program.cpp
  
  # specify compiler standard
  clang++ --std=c++17 {filename}.cpp
  ```

  

- **Clean** removes all cached objects and executables so the next time the project is built, all files will be recompiled and a new executable produced.

- **Rebuild** does a “clean”, followed by a “build”.

- **Compile** recompiles a single code file (regardless of whether it has been cached previously). This option does not invoke the linker or produce an executable.

- **Run/start** executes the executable from a prior build. Some IDEs (e.g. Visual Studio) will invoke a “build” before doing a “run” to ensure you are running the latest version of your code. Otherwise (e.g. Code::Blocks) will just execute the prior executable.

# grammer

## Basics

### operator

- The `:: `symbol is an operator called the **scope resolution operator**.

### iostream

- std::endl

  output a newline; Using `std::endl` is often inefficient than `'\n'`

```
std::cin and std::cout always go on the left-hand side of the operator.
std::cout is used to output a value (cout = character output).
std::cin is used to get an input value (cin = character input).
<< is used with std::cout, and shows the direction that data is moving. std::cout << 4 moves the value 4 to the console.
>> is used with std::cin, and shows the direction that data is moving. std::cin >> x moves the value the user entered from the keyboard into variable x.
```

- Avoid implementation-defined and unspecified behavior whenever possible, as they may cause your program to malfunction on other implementations.

addition (`+`), subtraction (`-`), multiplication (`*`), division (`/`).  assignment (`=`)

## function

- When a function parameter exists but is not used in the body of the function, do not give it a name. You can optionally put a name inside a comment.

  ```c++
  void doSomething(int) // ok: unnamed parameter will not generate warning
  {
  }
  
  void doSomething(int /*count*/)
  {
  }
  ```

- Names used for function parameters or variables declared in a function body are only visible within the function that declares them. This means local variables within a function can be named without regard for the names of variables in other functions. This helps keep functions independent.
- Define your local variables as close to their first use as reasonable.

- **declaration**, tells the *compiler* about the *existence* of an identifier and its associated type information.

  ```c++
  int add(int x, int y); // tells the compiler about a function named "add" that takes two int parameters and returns an int.  No body!
  int x;                 // tells the compiler about an integer variable named x
  ```

- **definition** is a declaration that actually implements (for functions and types) or instantiates (for variables) the identifier.
  In C++, all definitions are declarations. Therefore `int x;` is both a definition and a declaration.

- the compiler compiles each file individually. 

- Use double quotes to include header files that you’ve written or are expected to be found in the current directory. Use angled brackets to include headers that come with your compiler, OS, or third-party libraries you’ve installed elsewhere on your system.

- header guard

  ```c++
  #ifndef ADD_H
  #define ADD_H
  
  int add(int x, int y);
  
  #endif
  ```

  

## Fundamental Data Types

- The smallest unit of memory is a **binary digit** (also called a **bit**), which can hold a value of 0 or 1.
- Each memory address holds 1 byte of data. A **byte** is a group of bits that are operated on as a unit. The modern standard is that a byte is comprised of 8 sequential bits.

| Types                                                        | Category             | Meaning                                          | Example |
| :----------------------------------------------------------- | :------------------- | :----------------------------------------------- | :------ |
| float double long double                                     | Floating Point       | a number with a fractional part                  | 3.14159 |
| bool                                                         | Integral (Boolean)   | true or false                                    | true    |
| char <br />wchar_t <br />char8_t (C++20) <br />char16_t (C++11) <br />char32_t (C++11) | Integral (Character) | a single character of text                       | ‘c’     |
| short int int long int long long int (C++11)                 | Integral (Integer)   | positive and negative whole numbers, including 0 | 64      |
| std::nullptr_t (C++11)                                       | Null Pointer         | a null pointer                                   | nullptr |
| void                                                         | Void                 | no type                                          | n/a     |

| Category       | Type           | Minimum Size | Typical Size       | Note                  |
| :------------- | :------------- | :----------- | :----------------- | :-------------------- |
| Boolean        | bool           | 1 byte       | 1 byte             |                       |
| character      | char           | 1 byte       | 1 byte             | always exactly 1 byte |
|                | wchar_t        | 1 byte       | 2 or 4 bytes       |                       |
|                | char8_t        | 1 byte       | 1 byte             |                       |
|                | char16_t       | 2 bytes      | 2 bytes            |                       |
|                | char32_t       | 4 bytes      | 4 bytes            |                       |
| integer        | short          | 2 bytes      | 2 bytes            |                       |
|                | int            | 2 bytes      | 4 bytes            |                       |
|                | long           | 4 bytes      | 4 or 8 bytes       |                       |
|                | long long      | 8 bytes      | 8 bytes            |                       |
| floating point | float          | 4 bytes      | 4 bytes            |                       |
|                | double         | 8 bytes      | 8 bytes            |                       |
|                | long double    | 8 bytes      | 8, 12, or 16 bytes |                       |
| pointer        | std::nullptr_t | 4 bytes      | 4 or 8 bytes       |                       |
### integer
| Size / Type   | Range                                                   |
| :------------ | :------------------------------------------------------ |
| 8-bit signed  | -128 to 127                                             |
| 16-bit signed | -32,768 to 32,767                                       |
| 32-bit signed | -2,147,483,648 to 2,147,483,647                         |
| 64-bit signed | -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807 |

| Name          | Type            | Range                                                   | Notes                                                        |
| :------------ | :-------------- | :------------------------------------------------------ | :----------------------------------------------------------- |
| std::int8_t   | 1 byte signed   | -128 to 127                                             | Treated like a signed char on many systems. See note below.  |
| std::uint8_t  | 1 byte unsigned | 0 to 255                                                | Treated like an unsigned char on many systems. See note below. |
| std::int16_t  | 2 byte signed   | -32,768 to 32,767                                       |                                                              |
| std::uint16_t | 2 byte unsigned | 0 to 65,535                                             |                                                              |
| std::int32_t  | 4 byte signed   | -2,147,483,648 to 2,147,483,647                         |                                                              |
| std::uint32_t | 4 byte unsigned | 0 to 4,294,967,295                                      |                                                              |
| std::int64_t  | 8 byte signed   | -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807 |                                                              |
| std::uint64_t | 8 byte unsigned |                                                         |                                                              |

### floating point

| Size                                    | Range                                     | Precision                              |
| :-------------------------------------- | :---------------------------------------- | :------------------------------------- |
| 4 bytes                                 | ±1.18 x 10-38 to ±3.4 x 1038 and 0.0      | 6-9 significant digits, typically 7    |
| 8 bytes                                 | ±2.23 x 10-308 to ±1.80 x 10308 and 0.0   | 15-18 significant digits, typically 16 |
| 80-bits (typically uses 12 or 16 bytes) | ±3.36 x 10-4932 to ±1.18 x 104932 and 0.0 | 18-21 significant digits               |
| 16 bytes                                | ±3.36 x 10-4932 to ±1.18 x 104932 and 0.0 | 33-36 significant digits               |

- When outputting floating point numbers, `std::cout` has a default precision of 6 -- that is, it assumes all floating point variables are only significant to 6 digits (the minimum precision of a float), and hence it will truncate anything after that.
- **Inf**, which represents infinity. Inf can be positive or negative.  **NaN**, which stands for “Not a Number”.

# tool

## vscode

### config file

- c_cpp_properties.json

  ```shell
  # config include path
  Open the Command Palette (Ctrl+Shift+P) and type C/C++: Edit Configurations (UI).
  This will open the c_cpp_properties.json file. If it doesn't exist, it will be created.
  ```

- setting.json
- tasks.json





