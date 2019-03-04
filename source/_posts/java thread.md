---
title: java thread
date: 2018-06-1 10:01:30
tags: snippet
categories: java
---

### how to avoid deadlock

![](https://2.bp.blogspot.com/-63RZ-BTlAFs/VfGeHMnGdFI/AAAAAAAADuw/gwqtrVliMsM/s400/Deadlock%2Bof%2BThreads.jpg)

### ConcurrentHashMap faster than Hashtable

>  ConcurrentHashMap is introduced as an alternative of Hashtable in Java 5, it is faster because of its design. ConcurrentHashMap divides the whole map into different segments and only lock a particular segment during the update operation, instead of [Hashtable](http://javarevisited.blogspot.com/2012/01/java-hashtable-example-tutorial-code.html), which locks whole Map.

![](https://1.bp.blogspot.com/-s28yqJthVWo/V10_G31sSCI/AAAAAAAAGNY/8oJE5cDD5BUa5VBiCI8Bx-UdpD8_MoeSwCLcB/s400/Internal%2Bimplementation%2Bof%2BConcurrentHashMap%2Bin%2BJava.png)

###   submit() and execute() method of Executor and ExecutorService

>  The main difference between submit and execute method from ExecutorService interface is that former return a result in the form of a Future object, while later doesn't return a result. By the way, both are used to submit a task to thread pool in Java but one is defined in Executor interface,while other is added into ExecutorService interface.

![](https://2.bp.blogspot.com/-ybewy1bWbFs/VxZFewRdKbI/AAAAAAAAFt4/fuAHJBE1r_8nlfUz34YTKI1j70UdqaMiQCLcB/s640/Difference%2Bbetween%2Bsubmit%2Bvs%2Bexecute%2Bmethod%2Bin%2BJava.png)



#### when to use

>  - In general, if you are doing computational task e.g. calculating some risk stats, [calculating factorial of large numbers](http://java67.blogspot.com/2015/09/how-to-use-biginteger-class-in-java.html) or doing some time-consuming computation e which results in some value then use the submit() method. It immediately returns a Future object, which can be later queried to get the value of computation by calling get() method.

> - Remember, get() is a [blocking call](http://javarevisited.blogspot.com/2012/02/what-is-blocking-methods-in-java-and.html) so always call the version which accepts a timeout. While you can use the execute() method if you just want your code to be run in parallel by worker threads of the thread pool.

```java
Future future = executorService.submit(new Runnable() {
    public void run() {
        System.out.println("Asynchronous task");
    }
});

future.get();  //returns null if the task has finished correctly.
```

```java
Future future = executorService.submit(new Callable(){
    public Object call() throws Exception {
        System.out.println("Asynchronous Callable");
        return "Callable Result";
    }
});

System.out.println("future.get() = " + future.get());
```

###  ReentrantLock vs synchronized

#### the advantages of ReentrantLock

1. Ability to lock interruptibly.

2. Ability to timeout while waiting for lock.

3. Power to create fair lock.

4. API to get list of waiting thread for lock.

5. Flexibility to try for lock without blocking.

#### the disadvantages of ReentrantLock

1.   Major drawback of using ReentrantLock in Java is wrapping method body inside [try-finally block](http://javarevisited.blogspot.com/2012/11/difference-between-final-finally-and-finalize-java.html), which makes code unreadable and hides business logic. 

2. programmer is responsible for acquiring and releasing lock, which is a power but also opens gate for new subtle bugs, when programmer forget to release the lock in finally block.

### ReadWriteLock

![](https://4.bp.blogspot.com/-yYqp8m0BGdk/V11A-s0qwVI/AAAAAAAAGNw/LL_qGX5_qD4dHTFMe3kklI4jYWyDHzIyQCLcB/s400/ReadWriteLock%2Bin%2BJava.jpg)

### stop thread

> - There was some control methods in JDK 1.0 e.g. stop(), suspend() and resume() which was deprecated in later releases due to potential deadlock threats, from then Java API designers has not made any effort to provide a consistent, thread-safe and elegant way to stop threads.

> - Programmers mainly rely on the fact that thread stops automatically as soon as they finish execution of run() or call() method. To manually stop, programmers either take advantage of volatile boolean variable and check in every iteration if run method has loops or interrupt threads to abruptly cancel tasks.

### ThreadLocal

>- The `ThreadLocal` class in Java enables you to create variables that can only be read and written by the same thread. Thus, even if two threads are executing the same code, and the code has a reference to a`ThreadLocal` variable, then the two threads cannot see each other's `ThreadLocal` variables.
>- Each thread holds an exclusive copy of ThreadLocal variable which becomes eligible to Garbage collection after thread finished or died, normally or due to any Exception, Given those ThreadLocal variable doesn't have any other live references.
>- ThreadLocal variables in Java are generally private static fields in Classes and maintain its state inside Thread.

### synchronized vs concurrent collection

> later is more scalable than former
>
>  synchronized collections locks the whole collection e.g. whole Map or List while concurrent collection never locks the whole Map or List. They achieve thread safety by using advanced and sophisticated techniques like lock stripping. For example, the ConcurrentHashMap divides the whole map into several segments and locks only the relevant segments, which allows multiple threads to access other segments of same ConcurrentHashMap without locking.

#### CopyOnWriteArrayList

>  CopyOnWriteArrayList allows multiple reader threads to read without synchronization and when a write happens it copies the whole ArrayList and swap with a newer one.

### Stack and Heap

> - Each thread has their own stack, which is used to store local variables, method parameters and call stack. Variable stored in one Thread’s stack is not visible to other.
> - heap is a common memory area which is shared by all threads.Objects whether local or at any level is created inside heap.

- To improve performance thread tends to cache values from heap into their stack, which can create problems if that variable is modified by more than one thread, this is where volatile variables comes in picture. volatile suggest threads to read value of variable always from main memory. 
- If there is no memory left in the stack for storing function call or local variable, JVM will throw java.lang.StackOverFlowError, while if there is no more heap space for creating an object, JVM will throw java.lang.OutOfMemoryError: Java Heap Space
- Variables stored in stacks are only visible to the owner Thread while objects created in the heap are visible to all thread. In other words, stack memory is kind of private memory of Java Threads while heap memory is shared among all threads.

### thread pool

> Java API provides Executor framework, which allows you to create different types of thread pools e.g. single thread pool, which process one task at a time, fixed thread pool (a pool of fixed number of thread) or cached thread pool (an expandable thread pool suitable for applications with many short lived tasks).

#### the benefits

- Use of Thread Pool reduces response time by avoiding thread creation during request or task processing.

- Use of Thread Pool allows you to change your execution policy as you need. you can go from single thread to multiple threads by just replacing ExecutorService implementation.
- Thread Pool in Java application increases the stability of the system by creating a configured number of threads decided based on system load and available resource.
- Thread Pool frees application developer from thread management stuff and allows to focus on business logic.

### volatile vs atomic variable

