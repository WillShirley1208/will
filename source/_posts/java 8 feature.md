---
title: java 8
date: 2015-09-01 12:01:30
tags: java
categories: learn
---

### forEach() method in Iterable interface

### default and static methods in Interfaces

- Java 8 introduces the “Default Method” or (Defender methods) feature, which allows the developer to add new methods to the interfaces without breaking their existing implementation. It provides the flexibility to allow interface to define implementation which will use as the default in a situation where a concrete class fails to provide an implementation for that method.

### Functional Interfaces and Lambda Expressions

#### Functional Interfaces

>Being object oriented is not bad, but it brings a lot of verbosity to the program. Java 8 Functional Interfaces and Lambda Expressions help us in writing smaller and cleaner code by removing a lot of boiler-plate code.
- An interface with exactly one abstract method is called Functional Interface. @FunctionalInterface annotation is added so that we can mark an interface as functional interface.
- Some of the useful java 8 functional interfaces are Consumer, Supplier, Function and Predicate.

#### Lambda Expression

- Objects are the base of java programming language and we can never have a function without an Object, that’s why Java language provide support for using lambda expressions only with functional interfaces.
- Lambda Expressions syntax is (argument) -> (body). 

### Java Stream API for Bulk Data Operations on Collections
### Java Time API
### Collection API improvements
### Concurrency API improvements
### Java IO improvements
### Miscellaneous Core API improvements