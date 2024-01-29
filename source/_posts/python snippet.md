---
title: python snippet
date: 2023-11-14 15:28:51
tags: snippet
categories: python
---

- 统计运行时间

  ```python
  # 毫秒值
  start_time = time.perf_counter()
  xxx
  cost_time = int((time.perf_counter() - start_time)* 1000)
  ```

  

- `__call__函数`

  `__call__` 方法是 Python 中的一个特殊方法（也称为魔术方法），用于将一个对象作为函数调用。当一个对象被作为函数调用时，Python 解释器会自动调用该对象的 `__call__` 方法。

  在类中定义 `__call__` 方法可以使对象具有可调用的行为，就像调用函数一样。这样的对象被称为可调用对象。通过实现 `__call__` 方法，我们可以在对象被调用时执行特定的逻辑。

  以下是一个示例，展示了如何定义和使用 `__call__` 方法：

  ```python
  class CallableClass:
      def __call__(self, *args, **kwargs):
          print("Object is being called")
  
  obj = CallableClass()
  obj()  # 等同于调用 obj.__call__()
  ```

  在上面的示例中，我们定义了一个名为 `CallableClass` 的类，并在其中实现了 `__call__` 方法。当创建 `CallableClass` 的实例 `obj` 并将其作为函数调用时，`__call__` 方法会被自动调用，并打印出 "Object is being called"。

  需要注意的是，`__call__` 方法可以带有参数，就像普通的函数一样。我们可以在 `__call__` 方法中定义任意的逻辑，可以访问实例的属性、调用其他方法等。

  使用 `__call__` 方法可以使对象具有类似函数的行为，这在某些情况下非常有用，例如实现可调用的类或实现函数式编程的概念。
