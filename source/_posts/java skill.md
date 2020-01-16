---
title: java skill
date: 2016-05-20 21:50:30
tags: snippet
categories: java
---

## 类型

- int变量自增 效率问题 

  ```
  i++;和i += 1;形成的指令数较少，且不需要操作数栈。相对而言，i = i+1;需要至少2个操作数栈深度，并且形成的指令数较多。故，i++;和i+= 1;的效率一样，并且二者的效率都优于i = i+1;
  ```

## 泛型

**T，E，K，V，？是这样约定的：**

- ？表示不确定的 java 类型
- T (type) 表示具体的一个java类型
- K V (key value) 分别代表java键值中的Key Value
- E (element) 代表Element

**？和 T 的区别**：

![img](https://mmbiz.qpic.cn/mmbiz/knmrNHnmCLFRVA73vYfkqVGfia90X5nyEsksfM8Vz6btFZCRd3PCSJC1XkZhaJ0ufxcoohxAdr8X1tykU9CZPUQ/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

？和 T 都表示不确定的类型，区别在于我们可以对 T 进行操作，但是对 ？不行，比如如下这种 ：

```
// 可以
T t = operate();

// 不可以
？car = operate();
```

简单总结下：

T 是一个 确定的 类型，通常用于泛型类和泛型方法的定义，？是一个 不确定 的类型，通常用于泛型方法的调用代码和形参，不能用于定义类和泛型方法。

**PECS（Producer Extends Consumer Super）**原则：作为生产者提供数据（往外读取）时，适合用上界通配符（extends）；作为消费者消费数据（往里写入）时，适合用下界通配符（super）。

## 时间

在 JDK8 以后，建议使用 DateTimeFormatter 代替 SimpleDateFormat ，因为 SimpleDateFormat 是线程不安全的，而 DateTimeFormatter 是线程安全的。当然，也可以采用第三方提供的线程安全日期格式化函数，比如 apache 的 DateFormatUtils 工具类。