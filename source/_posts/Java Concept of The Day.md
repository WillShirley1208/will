---
title: Java Concept of The Day
date: 2016-10-16 12:01:30
tags: learn
categories: java
top: true
---

- Stack is used only for execution purpose. Heap is used for storage purpose.
### Non-Static Members And Their Memory Management In Java
- Non-Static variables and Non-Static methods are non-static components of a class. These are also called instance components of a class. Non-static components are stored inside the object memory. Each object will have their own copy of non-static components. But,  static components are common to all objects of that class.
### IIB-Instance Initialization Block
- IIB is used to initialize instance variables or non-static variables.
- IIB blocks will not be called from the constructor in which this() statement is written as a first statement. 
### super and this keywords in java
- super class constructor is called by super() calling statement. You can’t use super() calling statement outside the constructor. By default, super() calling statement is the first statement in any constructor. 
- this() is the calling statement to same class constructor. It must be used within constructor only. If it is used, it must be the first statement in the constructor.
- You can’t use super and this keywords in a static method and in a static initialization block even though you are referring static members.
### 10 Points Every Java Programmer Should Know About final keyword in java
-  Any class or any method can be either abstract or final but not both. abstract and final are totally opposite. Because, abstract class or abstract method must be implemented or modified in the sub classes but final does not allow this. This creates an ambiguity.
-  The global variable which is declared as final and static remains unchanged for the whole execution. Because, Static members are stored in the class memory and they are loaded only once in the whole execution. They are common to all objects of the class. If you declare static variables as final, any of the objects can’t change their value as it is final. Therefore, variables declared as final and static are sometimes referred to as Constants. All fields of interfaces are referred as constants, because they are final and static by default.
### Garbage Collection And finalize() method In Java
- Whenever you run a java program, JVM creates three threads. 1) main thread   2) Thread Scheduler   3) Garbage Collector Thread. In these three threads, main thread is a user thread and remaining two are daemon threads which run in background.
### Access Modifiers In Java
- Class can not be a private except inner classes. Inner classes are nothing but again members of outer class. So members of a class (field, method, constructor and inner class) can be private but not the class itself.
  ![](https://i.imgur.com/6h3llC0.jpg)
### What Are Access And Non-Access Modifiers In Java?
![](https://i.imgur.com/nB7eV1i.jpg)
### Type Casting In Java
 Please note that by using casting, data can not be modified but only type of data can be modified.
- Primitive Casting.
  - memory size `byte < short < int < long < float < double.`
    - Auto Widening
    - Explicit Narrowing
- Derived Casting
  - Auto-up Casting
    - Explicit Down Casting
### Abstraction In Java
- Abstract classes contain abstract methods (you can refer them as ideas) so that they can be implemented in sub classes according to their requirements. They are also called as incomplete classes as they have some unimplemented abstract methods(ideas).
### Interfaces In Java
- Interfaces in java are very much similar to abstract classes but interfaces contain only abstract methods (you can refer to them as only ideas). Abstract classes may contain both abstract methods as well as concrete methods. But interfaces must contain only abstract methods. Concrete methods are not allowed in interfaces. Therefore, Interfaces show 100% abstractness.
### Auto-Widening Vs Auto-Boxing Vs Auto-UpCasting In Java
![](https://javaconceptoftheday.com/wp-content/uploads/2014/11/widening-Vs-boxing.png?x70034)
### Exception Handing
java.lang.Throwable is the super class of all errors and exceptions in java.
![](https://javaconceptoftheday.com/wp-content/uploads/2014/11/Exceptions.png?x70034)
![](https://i.imgur.com/xHC2xQo.jpg)
- java.lang.Error class represents the errors which are mainly caused by the environment in which application is running. For example, OutOfMemoryError occurs when JVM runs out of memory or StackOverflowError occurs when stack overflows.
- Where as java.lang.Exception class represents the exceptions which are mainly caused by the application itself. For example, NullPointerException occurs when an application tries to access null object or ClassCastException occurs when an application tries to cast incompatible class types. 
### Arrays
- Array can hold the references to any type of objects. It is important to note that array can contain only references to the objects, not the objects itself. 
### Threads
- Processes and threads can be diagrammatically represented as this
  ![](https://javaconceptoftheday.com/wp-content/uploads/2014/11/ThreadsAndProcesses.png?x70034)
- User threads are threads which are created by the application or user.
- Daemon threads are threads which are mostly created by the JVM.
- There are three constant fields in java.lang.Thread class related to priority of a thread. They are,
  ```
    	MIN_PRIORITY   —> It defines the lowest priority that a thread can have and It’s value is 1.
  	NORM_PRIORITY  —> It defines the normal priority that a thread can have and it’s value is 5.
  	MAX_PRIORITY  —> It defines the highest priority that a thread can have and it’s value is 10.
  ```
    The priority of a main thread, if explicitly not set, is always 5 i.e NORM_PRIORITY.
- avoid thread interference 
  ```
    	By declaring the method as synchronized.
  	By declaring the variables as final.
  	By declaring the variable as volatile.
  	By creating the immutable objects.
  	By using Atomic operations.
  	By restricting the access to same object by multiple threads.
  ```
- The Logic Behind The Synchronization In Java :
  ```
    The synchronization in java is built around an entity called object lock or monitor. Here is the brief description about lock or monitor.
  - Whenever an object is created to any class, an object lock is created and is stored inside the object.
  - One object will have only one object lock associated with it.
  - Any thread wants to enter into synchronized methods or blocks of any object, they must acquire object lock associated with that object and release the lock after they are done with the execution.
  - The other threads which wants to enter into synchronized methods of that object have to wait until the currently executing thread releases the object lock.
  - To enter into static synchronized methods or blocks, threads have to acquire class lock associated with that class as static members are stored inside the class memory.
  ```
- mutex
```
class Shared
{
    static void staticMethod()
    {
        synchronized (Shared.class)
        {
            //static synchronized block
        }
    }
    void NonStaticMethod()
    {
        synchronized (this)
        {
            //Non-static synchronized block
        }
    }
    void anotherNonStaticMethod()
    {
        synchronized (new Shared())
        {
            //Non-static synchronized block
        }
    }
}
```
- 10 Points-To-Remember About Synchronization In Java :
```
1. You can use synchronized keyword only with methods but not with variables, constructors, static initializer and instance initializers.
2. Constructors, Static initializer and instance initializer can’t be declared with synchronized keyword, but they can contain synchronized blocks.
3. Both static and non-static methods can use synchronized keyword. For static methods, thread need class level lock and for non-static methods, thread need object level lock.
4. It is possible that both static synchronized and non-static synchronized methods can run simultaneously. Because, static methods need class level lock and non-static methods need object level lock.
5. A method can contain any number of synchronized blocks. This is like synchronizing multiple parts of a method.
6. Synchronization blocks can be nested.
7. Lock acquired by the thread before executing a synchronized method or block must be released after the completion of execution, no matter whether execution is completed normally or abnormally (due to exceptions).
8. Synchronization in java is Re-entrant in nature. A thread can not acquire a lock that is owned by another thread. But, a thread can acquire a lock that it already owns. That means if a synchronized method gives a call to another synchronized method which needs same lock, then currently executing thread can directly enter into that method or block without acquiring the lock.
9. synchronized method or block is very slow. They decrease the performance of an application. So, special care need to be taken while using synchronization. Use synchronization only when you needed it the most.
10. Use synchronized blocks instead of synchronized methods. Because, synchronizing some part of a method improves the performance than synchronizing the whole method.
```
- wait(), notify() and notifyAll()
```
These methods are final methods of java.lang.Object class. That means every class in java will have these methods. Below is the method signatures of these methods.
1. public final void wait() throws InterruptedException
This method tells the currently executing thread to release the lock of this object and wait until some other thread acquires the same lock and notify it using either notify() or notifyAll() methods. This method throws InterruptedException if waiting thread is interrupted.
2. public final void notify()
This method wakes up one thread randomly that called wait() method on this object.
3. public final void notifyAll()
This method wakes up all the threads that called wait() method on this object. But, only one thread will acquire lock of this object depending upon the priority.

Important Note : These three methods must be called within synchronized method or block. Any thread which calls these methods must have lock of that object.
```
- Some Things-To-Remember About wait(), notify() and notifyAll() :
  - If a thread calls notify() method and more than one threads are waiting for the object lock, then only one thread will be notified randomly.
  - When a thread calls notifyAll() method on an object, it notifies all the threads which are waiting for this object lock. But, only one thread will acquire this object lock depending upon priority.
  - When you call sleep() method on a thread, thread goes to sleep with holding the object lock with it. But, if you call wait() method, thread releases the object lock and goes for sleep. This is the main difference between wait() and sleep() methods.
  - wait(), notify() and notifyAll() are final methods of java.lang.Object class not java.lang.Thread class.
  - wait(), notify() and notifyAll() – all these three methods throw IllegalMonitorStateException if the calling thread does not owns the object lock.
  - wait() method is overloaded in Object class. There are two more wait() methods available in Object class.

- Thread Life Cycle OR Thread States In Java
  - NEW,	A thread will be in this state before calling start() method.
    - RUNNABLE, A thread will be in this state after calling the start() method.
    - BLOCKED, A thread will be in this state when a thread is waiting for object lock to enter into synchronized method/block or a thread will be in this state if deadlock occurs. 
    - WAITING, A thread will be in this state when wait() or join() method is called.
    - TIMED_WAITING, A thread will be in this state when thread is sleeping. i.e A thread will be in this state when sleep() or wait() with timeOut or join() with timeOut is called.
    - TERMINATED, A thread will be in this state once it finishes it’s execution.
      ![](https://javaconceptoftheday.com/wp-content/uploads/2014/11/JavaThreadLifeCycle.png?x70034)
- 7 Things Every Java Programmer Should Know About Threads In Java

  - **Runnable interface vs Thread class** : I am of opinion that when multiple threads need to execute same task, then use Runnable interface. If multiple threads need to execute different tasks, then go for Thread class.

- Difference Between wait() and sleep() Methods In Java
  - **wait()**:  releases the lock or monitor
    - **sleep()**: not  releases the lock or monitor

    ![](https://javaconceptoftheday.com/wp-content/uploads/2015/02/WaitAndSleep.png)
- Extends Thread Vs Implements Runnable In Java
  ![](https://javaconceptoftheday.com/wp-content/uploads/2015/12/RunnableVsThread.png)
  From the above all findings, it is clear that “Implements Runnable” is the preferred method to create the threads in java.

- How To Stop A Thread In Java

  - One is using boolean variable and second one is using interrupt() method. In this post, we will discuss both of these methods.

- Difference Between notify And notifyAll In Java
  - wait(), notify() and notifyAll() must be called within synchronized method or synchronized block.
    ![](https://javaconceptoftheday.com/wp-content/uploads/2016/03/NotifyVsNotifyAll.png)

- Difference Between BLOCKED Vs WAITING States In Java
  - There are six thread states in java. They are **NEW, RUNNABLE, BLOCKED, WAITING, TIMED_WAITING and TERMINATED**.
    ![](https://javaconceptoftheday.com/wp-content/uploads/2016/06/WaitingVsBlocked.png)

### String
- strings are treated as objects.
- JVM allocates some memory specially meant for string literals. This part of the heap memory is called String Constant Pool.
```
	String s1 = "abc"; 
	String s2 = "xyz";
	String s3 = "123";
	String s4 = "A";
    String s5 = new String("abc");
	char[] c = {'J', 'A', 'V', 'A'};
	String s6 = new String(c);
	String s7 = new String(new StringBuffer());
```
![](https://javaconceptoftheday.com/wp-content/uploads/2014/11/String-Constant-Pool.png)
- String Constant Pool is allocated to an object depending upon it’s content. There will be no two objects in the pool having the same content.
- ```
  “When you create a string object using string literal, JVM first checks the content of to be created object. If there exist an object in the pool with the same content, 	then it returns the reference of that object. It doesn’t create new object. If the content is different from the existing objects then only it creates new object.”
  ```
    ```
    	In simple words, there can not be two string objects with same content in the string constant pool. But, there can be two string objects with the same content in the heap memory.
    ```
- Immutability is the fundamental property of string objects. In whatever way you create the string objects, either using string literals or using new operator, they are immutable.

### Generic
- Defining Generic Class

  - Generics Work Only With Derived Types
- Rules To Follow While Implementing Generic Interfaces
  - Only generic classes can implement generic interfaces. 
    `class GenericClass<T> implements GenericInterface<T>`
    -A normal class can implement a generic interface if type parameter of generic interface is a wrapper class. For example, below implementation of GenericInterface is legal.
  ```
  	interface GenericInterface<Integer>{
       			//Generic interface with Integer as type parameter
  	}
  	class NormalClass implements GenericInterface<Integer>{
      			 //Normal class implementing generic interface
  	}
  ```
  - Class implementing generic interface at least must have same number and same type of parameters and at most can have any number and any type of parameters.
  ```
  class GenericClass2<T, V> implements GenericInterface<T>{
    		//Class with two type parameters
  }
  ```
- Define Methods And Constructors As Generic
  - Generic Methods:
    `<type-Parameters> return_type method_name(parameter list)`
  - Constructors As Generics,As we all know that constructors are like methods but without return types
- Bounded Types
  - the syntax for declaring Bounded type parameters.
    `<T extends SuperClass>`
  ```
  	public static <T extends Number> void printNumbers(T[] t)
  ```
- Wildcard arguments 
  - The syntax for declaring this type of wildcard arguments is:`GenericType<?>`
  - To specify an upper bound for wildcards:`GenericType<? extends SuperClass>`
  - specify a lower bound for wildcard argument using super clause:`GenericType<? super SubClass>`
- Type Erasure

### Collection Framework
- Collection Framework – Class Hierarchy
  - All classes and interfaces related to Collection Framework are placed in java.util package. 
    ![](https://javaconceptoftheday.com/wp-content/uploads/2014/11/CollectionHierarchy.png)
- Collection Framework – Collection Interface
    ![](https://javaconceptoftheday.com/wp-content/uploads/2014/11/CollectionInterface.png)

    - equals() and hashcode() methods in the Collection interface are not the methods of java.lang.Object class. Because, interfaces does not inherit from Object class. Only classes in java are inherited from Object class. Any classes implementing Collection interface must provide their own version of equals() and hashcode() methods or they can retain default version inherited from Object class.
- Collection Framework – List Interface
  - the class diagram of List interface：
    ![](https://javaconceptoftheday.com/wp-content/uploads/2014/11/ListInterface.png)
- Collection Framework – The ArrayList Class
  - Default initial capacity of an ArrayList is 10.
    - the hierarchy diagram of ArrayList class.
      ![](https://javaconceptoftheday.com/wp-content/uploads/2014/12/ArrayListClass.png)
    - Properties Of ArrayList :
      - Size of the ArrayList is not fixed. It can increase and decrease dynamically as we add or delete the elements.
        - ArrayList can have any number of null elements.
        - ArrayList can have duplicate elements.
        - As ArrayList implements RandomAccess, you can get, set, insert and remove elements of the ArrayList from  any arbitrary position.
          ![](https://javaconceptoftheday.com/wp-content/uploads/2014/12/ArrayListTemplateNew.png)
- Differences Between Array Vs ArrayList In Java
  - Array is static in nature,ArrayList is dynamic in nature
    - when you try to add elements to ArrayList beyond its capacity, it creates the new array with increased size and copies the elements from old array to new array.
    - if adding an element requires resizing of an ArrayList, then it gets slightly slower as it involves creating a new array in the background and copying all elements from old array to new array.
    - Array can hold both primitive data types (int, float….) as well as objects. Where as ArrayList can hold only objects. If you try to insert primitive data into ArrayList, data is automatically boxed into corresponding wrapper class.
      ![](https://javaconceptoftheday.com/wp-content/uploads/2016/09/ArrayVsArrayList.png)
    - ArrayList to Array,**toArray()** method returns an array containing all elements of the ArrayList.` Object[] array = list.toArray();`
    - Array To ArrayList:
      - Arrays.asList() 
        ```
        	String[] array = new String[] {"ANDROID", "JSP", "JAVA", "STRUTS", "HADOOP", "JSF"};
        	ArrayList<String> list = new ArrayList<String>(Arrays.asList(array));
        ```
        - Collection.addAll()
        ```
        	String[] array = new String[] {"ANDROID", "JSP", "JAVA", "STRUTS", "HADOOP", "JSF"};
        	ArrayList<String> list = new ArrayList<String>();
         		Collections.addAll(list, array);
        ```
        - ArrayList.addAll()
        ```
        	String[] array = new String[] {"ANDROID", "JSP", "JAVA", "STRUTS", "HADOOP", "JSF"};
        	ArrayList<String> list = new ArrayList<String>();
         	list.addAll(Arrays.asList(array));
        ```
        - Using streams from java 8
        ```
        	String[] array = new String[] {"ANDROID", "JSP", "JAVA", "STRUTS", "HADOOP", "JSF"};
        	List<Object> list = Arrays.stream(array).collect(Collectors.toList());
        ```
        ![](https://javaconceptoftheday.com/wp-content/uploads/2016/07/ArrayToArrayList.png)
- Remove Duplicate Elements From ArrayList
  - Using HashSet
    ```
       	HashSet<String> set = new HashSet<String>(listWithDuplicateElements);
       	ArrayList<String> listWithoutDuplicateElements = new ArrayList<String>(set);
    ```
    - Using LinkedHashSet
      `LinkedHashSet<String> set = new LinkedHashSet<String>(listWithDuplicateElements);`
      ![](https://javaconceptoftheday.com/wp-content/uploads/2015/07/RemovingDuplicateElementsFromArrayList.png)
- Collection Framework – The Vector Class
  - Vector class is synchronized. 
    - All methods of Vector class are synchronized 
    - capacity of the vector will be doubled whenever size exceeds capacity.
    - Why Not To Use Vector Class In Your Code
      - you can achieve thread safe ArrayList by using synchronizedList() method of Collections class. 
- Java Collection Framework – The LinkedList Class
  - In general terms, LinkedList is a data structure where each element consist of three things. First one is the reference to previous element, second one is the actual value of the element and last one is the reference to next element.
    ![](https://javaconceptoftheday.com/wp-content/uploads/2014/12/HowLinkedListWorks.png)
- Collection Framework – The Queue Interface
  - First-In-First-Out.
    - You can’t add or get or set elements at an arbitrary position in the queues. 
      ![](https://javaconceptoftheday.com/wp-content/uploads/2014/11/HowQueueWorks.png)
    - Properties Of Queue :
      - Null elements are not allowed in the queue. 
        - Queue can have duplicate elements.
        - Unlike a normal list, queue is not random access. i.e you can’t set or insert or get elements at an arbitrary positions.
        - In the Queue Interface, there are two methods to obtain and remove the elements from the head of the queue. They are poll() and remove(). The difference between them is, poll() returns null if the queue is empty and remove() throws an exception if the queue is empty.
        - There are two methods in the Queue interface to obtain the elements but don’t remove. They are peek() and element(). peek() returns null if the queue is empty and element() throws an exception if the queue is empty.

- Collection Framework – The Deque Interface
  -  the hierarchy diagram of Deque interface：
    ![](https://javaconceptoftheday.com/wp-content/uploads/2014/11/Deque.png)
    - The main advantage of Deque is that you can use it as both Queue (FIFO) as well as Stack (LIFO).
    - Deque is nothing but the double ended queue. That means, you can insert, retrieve and remove the elements from both the ends. 
      ![](https://javaconceptoftheday.com/wp-content/uploads/2014/11/HowDequeWorks.png)

- Collection Framework – The Set Interface
  - Order of elements in a set is implementation dependent. HashSet elements are ordered on hash code of elements. TreeSet elements are ordered according to supplied Comparator (If no Comparator is supplied, elements will be placed in ascending order) and LinkedHashSet maintains insertion order.
  - The HashSet internally uses HashMap to store the objects. The elements you insert in HashSet will be stored as keys of that HashMap object and their values will be a constant called PRESENT. This constant is defined as private static final Object PRESENT = new Object() in the source code of HashSet class.
  - HashSet can have maximum one null element.
    ![](https://javaconceptoftheday.com/wp-content/uploads/2015/01/HowHashSetWorks.png)
  - LinkedHashSet internally uses LinkedHashMap to store it’s elements just like HashSet which internally uses HashMap to store it’s elements.
  - LinkedHashSet maintains insertion order. This is the main difference between LinkedHashSet and HashSet.
  - SortedSet elements are sorted according to supplied Comparator. If you don’t mention any Comparator while creating a SortedSet, elements will be placed in ascending order.
  - SortedSet Inserted elements must be of Comparable type and they must be mutually Comparable.
  - Elements inserted in the TreeSet must be of Comparable type and elements must be mutually comparable. 
  - TreeSet internally uses TreeMap to store it’s elements just like HashSet and LinkedHashSet which use HashMap and LinkedHashMap respectively to store their elements.
- Java Collection Framework – The Map Interface
  ![](https://javaconceptoftheday.com/wp-content/uploads/2015/01/MapInterface.png)
  - Each key-value pairs of the map are stored as Map.Entry objects. Map.Entry is an inner interface of Map interface.
  - HashMap doesn’t maintain any order of elements. LinkedHashMap maintains insertion order of elements. Where as TreeMap places the elements according to supplied Comparator.
  - Default initial capacity of HashMap is 16.
  - Hashing is nothing but the function or algorithm or method which when applied on any object/variable returns an unique integer value representing that object/variable.
  - The capacity of an HashMap is the number of buckets in the hash table. The initial capacity is the capacity of an HashMap at the time of its creation. The default initial capacity of the HashMap is 24 i.e 16. The capacity of the HashMap is doubled each time it reaches the threshold. i.e the capacity is increased to 25=32, 26=64, 27=128….. when the threshold is reached.
  - HashMap internally uses an array of Entry<K, V> objects to store the data.
  - HashTable is a legacy class. It is almost considered as due for deprecation. Since JDK 1.5, ConcurrentHashMap is considered as better option than the HashTable.

### JDBC
- Java Database Connectivity API which is used by the java application to interact with the database.
    - JDBC API not directly interacts with the database. It uses JDBC driver of that database to interact with the database.
      ![](https://javaconceptoftheday.com/wp-content/uploads/2015/06/JavaJDBCDriverDatabase.png)
    - JDBC driver is a software component provided along with the database which is required by the JDBC API to interact with the database. Each database will have its own JDBC driver.

    >In simple terms, JDBC drivers are nothing but the implementations of interfaces provided in the JDBC API (java.sql and javax.sql packages) with respect to a particular database. These implementations are bundled in a JAR file and supplied along with the database. These implementations are used by the JDBC API to interact with that database.

### Big O Notations
- Big O notations consist of O and an expression enclosed within ( ). Expression within ( ) represents the number of computations a particular algorithm has to perform to solve a given problem.
- **O(1)** represents an algorithm which has to perform exact one computation to solve a problem. O(1) represents an algorithm which takes same time to execute for any size of input data. In other words, O(1) denotes an algorithm which performance is not affected by the size of the input data. 
  ```
  	boolean isFirstElementZero(int[] inputData)
  		{
  			if(inputData[0] == 0)
  			{
  				return true;
  			}
  		
  			return false;
  		}
  ```
- **O(N)** represents an algorithm which has to perform ‘N’ computations to solve a problem. Where ‘N’ is the number of elements in input data. That means algorithm will take more time as number of elements in input data increases. In the other words, O(N) denotes an algorithm whose performance is directly proportional to size of the input data.
  ```
  boolean searchForElement(int[] inputData, int n)
  	{
  		for (int i = 0; i < inputData.length; i++)
  		{
  			if(inputData[i] == n)
  			{
  				return true;
  			}
  		}
  	
  		return false;
  	}
  ```
- **O(N^2)** denotes an algorithm which has to perform N^2 computations to solve a problem. Where N is the number of elements in input data. In the other words, O(N^2) represents an algorithm whose performance is directly proportional to square of the size of the input data. The algorithms which have nested iterations over the input data will give O(N^2) performance. Further deeper iterations over the input data will give O(N^3), O(N^4) and so on.
  ```
  	boolean findDuplicates(int[] inputData)
  	{
  		for (int i = 0; i < inputData.length; i++)
  		{
  			for (int j = 0; j < inputData.length; j++)
  			{
  				if(inputData[i] == inputData[j] && i!=j)
  				{
  					return true;
  				}
  			}
  		}
  	
  		return false;
  	}
  ```
- **O(log N)** represents an algorithm whose number of computations grows linearly as input data grows exponentially. i.e O(log N) represents the algorithms whose execution time grows linearly as input data grows exponentially. That means if an algorithm takes 1 second to compute 10 elements, then it will take 2 seconds to compute 100 elements, 3 seconds to compute 1000 elements and so on. The best example for O(log N) is binary search algorithm which uses divide and conquer rule to search for an element.
- **O(2N)** represents an algorithm whose execution time is doubled for every extra element in the input data. For example, if an algorithm takes 4 seconds to compute 2 elements, then it will take 8 seconds to compute 3 elements, 16 seconds for 4 elements, 32 seconds for 5 elements and so on.
- **O(N!)** represents an algorithm which has to perform N! computations to solve a problem. Where N is the number of elements in the input data. For example, if an algorithm takes 2 seconds to compute 2 elements, then it will take 6 seconds to compute 3 elements, 24 seconds to compute 4 elements and so on.
  ![](https://javaconceptoftheday.com/wp-content/uploads/2015/05/Big-O-Notation.png)