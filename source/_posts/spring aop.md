---
title: spring aop
date: 2017-12-18 20:50:30
tags: learn
categories: spring
---

### Architectural

![](https://www.baeldung.com/wp-content/uploads/2017/11/Program_Execution.jpg)

### Business Object

> **A business object is a normal class which has a normal business logic.**

### Aspect

> **An aspect is a modularization of a concern that cuts across multiple classes.**

### JoinPoint

> **A Joinpoint is a point during the execution of a program, such as execution of a method or the handling of an exception.**

In Spring AOP, a *JoinPoint* always represents a method execution.

### Pointcut

> **A Pointcut is a predicate that helps match an *Advice* to be applied by an *Aspect* at a particular *JoinPoint*.**

The Advice is often associated with a *Pointcut* expression and runs at any *Joinpoint* matched by the Pointcut.

### Advice

> **An advice is an action taken by an aspect at a particular *Joinpoint*. Different types of advice include *“around,” “before”* and *“after”* advice.**

In Spring, an *Advice* is modeled as an interceptor, maintaining a chain of interceptors around the *Joinpoint*.

---

### Example:Wiring Business Object and Aspect

- Business Object

  ```java
  public class SampleAdder {
      public int add(int a, int b) {
          return a + b;
      }
  }
  ```

- a simple Aspect

  Unified logging can be an example of such cross-cutting concern:

  ```java
  public class AdderAfterReturnAspect {
      private Logger logger = LoggerFactory.getLogger(this.getClass());
      public void afterReturn(Object returnValue) throws Throwable {
          logger.info("value return was {}",  returnValue);
      }
  }
  ```

- config excerpt

  ```xml
  <bean id="sampleAdder" class="org.xxx.SampleAdder" />
  <bean id="doAfterReturningAspect"
    class="org.xxx.AdderAfterReturnAspect" />
  <aop:config>
      <aop:aspect id="aspects" ref="doAfterReturningAspect">
         <aop:pointcut id="pointCutAfterReturning" expression=
           "execution(* org.xxx.SampleAdder+.*(..))"/>
         <aop:after-returning method="afterReturn"
           returning="returnValue" pointcut-ref="pointCutAfterReturning"/>
      </aop:aspect>
  </aop:config>
  ```

  

---

> 依赖注入DI有助于应用对象之间的解耦，而AOP可以实现横切关注点与它们所影响的对象之间的解耦

- 通知Advice

  > 切面的工作被称为通知，通知定义了切面是什么以及何时使用。

  - 前置通知（Before）：在目标方法被调用之前调用通知功能
  - 后置通知（After）：在目标方法完成之后调用通知，此时不会关心方法的输出
  - 返回通知（After-returning）：在目标方法成功执行之后调用通知
  - 异常通知（After-throwing）：在目标方法抛出异常后调用通知
  - 环绕通知（Around）：通知包裹了被通知的方法，在被通知的方法调用之前和调用之后执行自定义的行为。

- 连接点Join point

  > 连接点是应用程序的点，可供插入切面
  >
  > 我理解的是程序的某处，或者任意一处都可以称作为连接点

- 切点Pointcut

  >切点有助于缩小切面所通知的连接点的范围
  >
  >如果Advice定义了切面的what和when,那么pointcut就定义了where

- 切面Aspect

  > Aspect是Advice和Pointcut的结合

  