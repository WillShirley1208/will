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

# point

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

