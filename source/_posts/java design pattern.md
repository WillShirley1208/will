---
title: java design pattern
date: 2017-12-20 12:01:30
tags: learn
categories: design pattern
---

## Creational Design Patterns

### Singleton

- different approaches to implement:
  - Private constructor to restrict instantiation of the class from other classes.
  - Private static variable of the same class that is the only instance of the class.
  - Public static method that returns the instance of the class, this is the global access point for outer world to get the instance of the singleton class.
- design concerns with the implementation
  - Eager initialization
  - Static block initialization
  - Lazy Initialization
  - Thread Safe Singleton
  - Bill Pugh Singleton Implementation
  - Using Reflection to destroy Singleton Pattern
  - Enum Singleton
  - Serialization and Singleton

### Factory

- Factory design pattern provides approach to code for interface rather than implementation.
- Factory pattern removes the instantiation of actual implementation classes from client code. Factory pattern makes our code more robust, less coupled and easy to extend. For example, we can easily change PC class implementation because client program is unaware of this.
- Factory pattern provides abstraction between implementation and client classes through inheritance.

### Abstract Factory

- Abstract Factory design pattern provides approach to code for interface rather than implementation.
- Abstract Factory pattern is “factory of factories” and can be easily extended to accommodate more products, for example we can add another sub-class Laptop and a factory LaptopFactory.
- Abstract Factory pattern is robust and avoid conditional logic of Factory pattern.

### Builder

- Builder pattern solves the issue with large number of optional parameters and inconsistent state by providing a way to build the object step-by-step and provide a method that will actually return the final Object.

### Prototype

- Prototype design pattern is used when the Object creation is a costly affair and requires a lot of time and resources and you have a similar object already existing.Prototype pattern provides a mechanism to copy the original object to a new object and then modify it according to our needs. Prototype design pattern uses java cloning to copy the object.

## Structural Design Patterns

### Adapter

- Adapter design pattern is one of the structural design pattern and its used so that two unrelated interfaces can work together. The object that joins these unrelated interface is called an Adapter.

### Composite

- Composite design pattern is used when we have to represent a part-whole hierarchy.

### Proxy

- Proxy design pattern common uses are to control access or to provide a wrapper implementation for better performance.

### Flyweight

- Use sharing to support large numbers of fine-grained objects efficiently

### Facade

- Provide a unified interface to a set of interfaces in a subsystem. Facade Pattern defines a higher-level interface that makes the subsystem easier to use.

### Bridge

- Decouple an abstraction from its implementation so that the two can vary independently.

### Decorator

- Decorator design pattern is helpful in providing runtime modification abilities and hence more flexible. Its easy to maintain and extend when the number of choices are more.
- The disadvantage of decorator design pattern is that it uses a lot of similar kind of objects (decorators).

## Behavioral Design Patterns

### Template Method

- Template method should consists of certain steps whose order is fixed and for some of the methods, implementation differs from base class to subclass. Template method should be final.
- Most of the times, subclasses calls methods from super class but in template pattern, superclass template method calls methods from subclasses, this is known as Hollywood Principle – “don’t call us, we’ll call you.”.
- Methods in base class with default implementation are referred as Hooks and they are intended to be overridden by subclasses, if you want some of the methods to be not overridden, you can make them final, for example in our case we can make buildFoundation() method final because if we don’t want subclasses to override it.

### Media

- Allows loose coupling by encapsulating the way disparate sets of objects interact and communicate with each other. Allows for the actions of each object set to vary independently of one another.