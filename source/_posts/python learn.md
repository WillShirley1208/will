---
title: python learn
date: 2023-10-31 17:04:12
tags: learn
categories: python
---

![](/images/python/python知识图谱.png)

# 语法

## 数据结构

### 列表list和元组tuple

```python
l = [1, 2, 'hello', 'world'] # 列表中同时含有 int 和 string 类型的元素
l
[1, 2, 'hello', 'world']
 
tup = ('jason', 22) # 元组中同时含有 int 和 string 类型的元素
tup
('jason', 22)
```

- 列表是动态的，长度可变，可以随意的增加、删减或改变元素。列表的存储空间略大于元组，性能略逊于元组。
- 元组是静态的，长度大小固定，不可以对元素进行增加、删减或者改变操作。元组相对于列表更加轻量级，性能稍优。

### 字典和集合

```python
d1 = {'name': 'jason', 'age': 20, 'gender': 'male'}
d2 = dict({'name': 'jason', 'age': 20, 'gender': 'male'})
d3 = dict([('name', 'jason'), ('age', 20), ('gender', 'male')])
d4 = dict(name='jason', age=20, gender='male') 
d1 == d2 == d3 ==d4
True
 
s1 = {1, 2, 3}
s2 = set([1, 2, 3])
s1 == s2
True
```

- 字典在 Python3.7+ 是有序的数据结构，而集合是无序的，其内部的哈希表存储结构，保证了其查找、插入、删除操作的高效性。所以，字典和集合通常运用在对元素的高效查找、去重等场景。

### 字符串

```python
s1 = 'hello'
s2 = "hello"
s3 = """hello"""
s1 == s2 == s3
True
```

- 转义字符

```python
s = 'a\nb\tc'
print(s)
a
b	c

len(s)
5
```

- 常用方法

  - 和其他数据结构，如列表、元组一样，字符串的索引同样从 0 开始，index=0 表示第一个元素（字符），[index:index+2] 则表示第 index 个元素到 index+1 个元素组成的子字符串。

  - 字符串是不可变的（immutable）

  - 字符串格式化

    ```python
    print('no data available for person with id: {}, name: {}'.format(id, name)) '''最新规范'''
    print('no data available for person with id: %s, name: %s' % (id, name))'''以往规范，%s 表示字符串型，%d 表示整型'''
    ```

### json

- json.dumps() 这个函数，接受 基本数据类型，然后将其序列化为 string
- json.loads() 这个函数，接受一个合法字符串，然后将其反序列化为基本数据类型

## 条件与循环

### 条件

```python
# y = |x|
if x < 0:
    y = -x
else:
    y = x
# 场景二
if condition_1:
    statement_1
elif condition_2:
    statement_2
...
elif condition_i:
    statement_i
else:
    statement_n
```



### 循环

```python
# 列表
l = [1, 2, 3, 4]
for item in l:
    print(item)
1
2
3
4
# 字典
d = {'name': 'jason', 'dob': '2000-01-01', 'gender': 'male'}
for k in d: # 遍历字典的键
    print(k)
name
dob
gender
 
for v in d.values(): # 遍历字典的值
    print(v)
jason
2000-01-01
male    
 
for k, v in d.items(): # 遍历字典的键值对
    print('key: {}, value: {}'.format(k, v))
key: name, value: jason
key: dob, value: 2000-01-01
key: gender, value: male 

```

### while

```python
while True:
    try:
        text = input('Please enter your questions, enter "q" to exit')
        if text == 'q':
            print('Exit system')
            break
        ...
        ...
        print(response)
    except as err:
        print('Encountered error: {}'.format(err))
        break 
```



## 异常

- 当程序中存在多个 except block 时，最多只有一个 except block 会被执行。换句话说，如果多个 except 声明的异常类型都与实际相匹配，那么只有最前面的 except block 会被执行，其他则被忽略。

```python
try:
    s = input('please enter two numbers separated by comma: ')
    num1 = int(s.split(',')[0].strip())
    num2 = int(s.split(',')[1].strip())
    ...
except ValueError as err:
    print('Value Error: {}'.format(err))
except IndexError as err:
    print('Index Error: {}'.format(err))
except:
    print('Other error')
 
print('continue')
...
```

- 无论发生什么情况，finally block 中的语句都会被执行，哪怕前面的 try 和 excep block 中使用了 return 语句。

```python
import sys
try:
    f = open('file.txt', 'r')
    .... # some data processing
except OSError as err:
    print('OS error: {}'.format(err))
except:
    print('Unexpected error:', sys.exc_info()[0])
finally:
    f.close()
```

- 自定义异常，定义并实现了初始化函数和 str 函数（直接 print 时调用）：

```python
class MyInputError(Exception):
    """Exception raised when there're errors in input"""
    def __init__(self, value): # 自定义异常类型的初始化
        self.value = value
    def __str__(self): # 自定义异常类型的 string 表达形式
        return ("{} is invalid input".format(repr(self.value)))
    
try:
    raise MyInputError(1) # 抛出 MyInputError 这个异常
except MyInputError as err:
    print('error: {}'.format(err))
```

## 函数

```python
def name(param1, param2, ..., paramN):
    statements
    return/yield value # optional
```

### 函数嵌套

- 函数的嵌套能够保证内部函数的隐私。
- 合理的使用函数嵌套，能够提高程序的运行效率

```python
def f1():
    print('hello')
    def f2():
        print('world')
    f2()
f1()
 
# 输出
hello
world
```

### 函数变量

- 局部变量：只在函数内部有效。一旦函数执行完毕，局部变量就会被回收

- 全局变量：不能在函数内部随意改变全局变量的值，如果我们一定要在函数内部改变全局变量的值，就必须加上 global 这个声明

  ```python
  MIN_VALUE = 1
  MAX_VALUE = 10
  def validation_check(value):
      global MIN_VALUE
      ...
      MIN_VALUE += 1
      ...
  validation_check(5)
  ```

### 闭包

```python
def nth_power(exponent):
    def exponent_of(base):
        return base ** exponent
    return exponent_of # 返回值是 exponent_of 函数
 
square = nth_power(2) # 计算一个数的平方
cube = nth_power(3) # 计算一个数的立方 
 
print(square(2))  # 计算 2 的平方
print(cube(2)) # 计算 2 的立方
# 输出
4 # 2^2
8 # 2^3
```

### 匿名函数

- **lambda 是一个表达式（expression），并不是一个语句（statement）**
- **lambda 的主体是只有一行的简单表达式，并不能扩展成一个多行的代码块**

```python
square = lambda x: x**2
square(3)
# 输出
9

# 等同于
def square(x):
    return x**2
square(3)
```

### 函数式编程

- 所谓函数式编程，是指代码中每一块都是不可变的（immutable），都由纯函数（pure function）的形式组成。这里的纯函数，是指函数本身相互独立、互不影响，对于相同的输入，总会有相同的输出，没有任何副作用

- 主要提供了这么几个函数：map()、filter() 和 reduce()，通常结合匿名函数 lambda 一起使用

## 类

> **类，一群有着相同属性和函数的对象的集合。**
>
> OOP思想四要素： 类 对象 属性 函数

- 类函数

- 成员函数

- 静态函数

```python
class Document():
    
    WELCOME_STR = 'Welcome! The context for this book is {}.'
    
    def __init__(self, title, author, context):
        print('init function called')
        self.title = title
        self.author = author
        self.__context = context
    
    # 类函数
    @classmethod
    def create_empty_book(cls, title, author):
        return cls(title=title, author=author, context='nothing')
    
    # 成员函数
    def get_context_length(self):
        return len(self.__context)
    
    # 静态函数
    @staticmethod
    def get_welcome(context):
        return Document.WELCOME_STR.format(context)
 
 
empty_book = Document.create_empty_book('What Every Man Thinks About Apart from Sex', 'Professor Sheridan Simove')
 
 
print(empty_book.get_context_length())
print(empty_book.get_welcome('indeed nothing'))
 
########## 输出 ##########
 
init function called
7
Welcome! The context for this book is indeed nothing.
```

### 构造函数

- 每个类都有构造函数，继承类在生成对象的时候，是不会自动调用父类的构造函数的，因此你必须在 **init**() 函数中显式调用父类的构造函数。它们的执行顺序是 子类的构造函数 -> 父类的构造函数。

### 抽象函数/抽象类

- 抽象类是一种特殊的类，它生下来就是作为父类存在的，一旦对象化就会报错。同样，抽象函数定义在抽象类之中，子类必须重写该函数才能使用。相应的抽象函数，则是使用装饰器 @abstractmethod 来表示。

- 抽象类就是这么一种存在，它是一种自上而下的设计风范，你只需要用少量的代码描述清楚要做的事情，定义好接口，然后就可以交给不同开发人员去开发和对接。



## 用法tips

- 引用规范 `from your_file import function_name, class_name`
- 每个Python文件都有一个特殊的变量`__name__`。当一个Python文件被直接运行时，`__name__`的值被设置为`'__main__'`。当一个Python文件被导入到另一个文件中时，`__name__`的值被设置为该文件的名字，所以用`if __name__ == '__main__'`来避开 import 时执行。

- 比较和拷贝

  - `'=='`操作符比较对象之间的值是否相等

  - `'is'`操作符，相当于比较对象之间的 ID 是否相等

    > 对于整型数字来说，以上`a is b`为 True 的结论，只适用于 -5 到 256 范围内的数字