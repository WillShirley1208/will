---
title: python snippet
date: 2023-11-14 15:28:51
tags: snippet
categories: python
---

### 统计运行时间

```python
# 毫秒值
start_time = time.perf_counter()
xxx
cost_time = int((time.perf_counter() - start_time)* 1000)
```

### `__call__函数`

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

### `**`使用

- `**` 运算符用于解包字典或关键字参数。当应用于字典时，`**` 运算符可以将字典中的键值对解包为一系列关键字参数传递给函数或方法。

  ```python
  def print_person_info(name, age, city):
      print(f"Name: {name}")
      print(f"Age: {age}")
      print(f"City: {city}")
  
  person = {
      'name': 'Alice',
      'age': 25,
      'city': 'New York'
  }
  
  print_person_info(**person)
  ```

  

### 布尔真假判断

虽然所有用户类实例的布尔值都是真。但是 Python 提供了改变这个行为的办法：**自定义类的 `__bool__` 魔法方法** *（在 Python 2.X 版本中为 `__nonzero__`）*。当类定义了 `__bool__` 方法后，它的返回值将会被当作类实例的布尔值。

另外，`__bool__` 不是影响实例布尔真假的唯一方法。如果类没有定义 `__bool__` 方法，Python 还会尝试调用 `__len__` 方法*（也就是对任何序列对象调用 `len` 函数）*，通过结果是否为 `0` 判断实例真假。

### 在条件判断中使用 all() / any()

`all()` 和 `any()` 两个函数非常适合在条件判断中使用。这两个函数接受一个可迭代对象，返回一个布尔值，其中：

- `all(seq)`：仅当 `seq` 中所有对象都为布尔真时返回 `True`，否则返回 `False`
- `any(seq)`：只要 `seq` 中任何一个对象为布尔真就返回 `True`，否则返回 `False`

### 使用 partial 构造新函数

```python
def multiply(x, y):
    return x * y
def double(value):
    # 返回另一个函数调用结果
    return multiply(2, value)
```

可写成

```python
import functools

double = functools.partial(multiply, 2)
```

### Mixin

> Mixin 是面向对象编程中的一个术语，它表示一种设计模式，用于将一组功能或行为以模块化的方式添加到类中，而不需要使用继承的方式。

### LOOP

```python
for i, name in enumerate(names):
    print(i, name)
```

- 使用 product 扁平化多层嵌套循环
- 使用 islice 实现循环内隔行处理
- 使用 takewhile 替代 break 语句
- 使用生成器编写自己的修饰函数
