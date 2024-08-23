---
title: java code snippet
date: 2016-02-16 12:01:30
tags: snippet
categories: java
---

## base64ToByte

```java
String string = "SmF2YWNvZGVnZWVrcw==";
// Get bytes from string
byte[] byteArray = Base64.decodeBase64(string.getBytes());

// Print the decoded array
System.out.println(Arrays.toString(byteArray));	
// Print the decoded string 
String decodedString = new String(byteArray);
System.out.println(string + " = " + decodedString);
```

## TimeConvert

```java
//date format
import org.apache.commons.lang3.time.DateFormatUtils;
String timeStr = DateFormatUtils.format(dateDate,"yyyy-MM-dd HH:mm:ss");
Date dateTime = DateUtils.parseDate(dateTimeStr,"yyyy-MM-dd HH:mm:ss");

SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
Date dateWithoutTime = sdf.parse(sdf.format(new Date()));

//capitalize the first letter of word
String output = input.substring(0, 1).toUpperCase() + input.substring(1);

//measure the time cost
long start = System.nanoTime();
//...
long end = System.nanoTime();
long used = end-start;
System.out.println("used:"+TimeUnit.NANOSECONDS.toMillis(used)+" ms");
```



## Lambda

```java
//stream read database   
public void testStreamRead() {
        oracleSqlSessionTemplate.select("xxxx.OracleMapper.getPersonInfos", new ResultHandler() {
            @Override
            public void handleResult(ResultContext resultContext) {
                PersonInfo personInfo = (PersonInfo) resultContext.getResultObject();
                LOG.debug("result count:[{}],result info:[{}]", resultContext.getResultCount(), personInfo.getName());
            }
        });
    }

    public void testStreamRead2() {
        oracleSqlSessionTemplate.select("xxxx.OracleMapper.getPersonInfos",
                resultContext -> {
                    PersonInfo personInfo = (PersonInfo) resultContext.getResultObject();
                    LOG.debug("result count:[{}],result info:[{}]", resultContext.getResultCount(), personInfo.getName());
                }
        );
    }

//iterate list
myFinalList = new ArrayList<>();
myListToParse.stream()
        .filter(elt -> elt != null)
        .forEach(elt -> myFinalList.add(doSomething(elt)));
//prefer
myFinalList = myListToParse.stream()
        .filter(elt -> elt != null)
        .map(elt -> doSomething(elt))
        .collect(Collectors.toList()); 
```

## thread

### notify thread

```java
class Shared
{
    synchronized void waitMethod()
    {
        Thread t = Thread.currentThread();
         
        System.out.println(t.getName()+" is releasing the lock and going to wait");
         
        try
        {
            wait();
        }
        catch (InterruptedException e) 
        {
            e.printStackTrace();
        }
         
        System.out.println(t.getName()+" has been notified and acquired the lock back");
    }
     
    synchronized void notifyOneThread()
    {
        Thread t = Thread.currentThread();
         
        notify();
         
        System.out.println(t.getName()+" has notified one thread waiting for this object lock");
    }
}
 
public class MainClass 
{   
    public static void main(String[] args) 
    {
        final Shared s = new Shared();
         
        //Thread t1 will be waiting for lock of object 's'
         
        Thread t1 = new Thread() 
        {
            @Override
            public void run()
            {
                s.waitMethod();
            }
        };
         
        t1.start();
         
        //Thread t2 will be waiting for lock of object 's'
         
        Thread t2 = new Thread() 
        {
            @Override
            public void run()
            {
                s.waitMethod();
            }
        };
 
        t2.start();
         
        //Thread t3 will be waiting for lock of object 's'
         
        Thread t3 = new Thread() 
        {
            @Override
            public void run()
            {
                s.waitMethod();
            }
        };
         
        t3.start();
         
        try
        {
            Thread.sleep(1000);
        } 
        catch (InterruptedException e) 
        {
            e.printStackTrace();
        }
         
        //Thread t4 will notify only one thread which is waiting for lock of object 's'
         
        Thread t4 = new Thread() 
        {
            @Override
            public void run()
            {
                s.notifyOneThread();
            }
        };
         
        t4.start(); 
    }   
}
```

### stop thread

```java
class MyThread extends Thread
{
    //Initially setting the flag as true
     
    private volatile boolean flag = true;
     
    //This method will set flag as false
     
    public void stopRunning()
    {
        flag = false;
    }
     
    @Override
    public void run()
    {
        //Keep the task in while loop
         
        //This will make thread continue to run until flag becomes false
         
        while (flag)
        {
            System.out.println("I am running....");
        }
         
        System.out.println("Stopped Running....");
    }
}
 
public class MainClass 
{   
    public static void main(String[] args) 
    {
        MyThread thread = new MyThread();
         
        thread.start();
         
        try
        {
            Thread.sleep(100);
        } 
        catch (InterruptedException e) 
        {
            e.printStackTrace();
        }
         
        //call stopRunning() method whenever you want to stop a thread
         
        thread.stopRunning();
    }   
}
```

---

```java
class MyThread extends Thread
{   
    @Override
    public void run()
    {
        while (!Thread.interrupted())
        {
            System.out.println("I am running....");
        }
         
        System.out.println("Stopped Running.....");
    }
}
 
public class MainClass 
{   
    public static void main(String[] args) 
    {
        MyThread thread = new MyThread();
         
        thread.start();
         
        try
        {
            Thread.sleep(100);
        } 
        catch (InterruptedException e) 
        {
            e.printStackTrace();
        }
         
        //interrupting the thread
         
        thread.interrupt();
    }   
}
```


### try-catch-finally

1. 不管有木有出现异常，finally块中代码都会执行** 
2. **当try和catch中有return时，finally仍然会执行** 
3. **finally是在return后面的表达式运算后执行的（此时并没有返回运算后的值，而是先把要返回的值保存起来，管finally中的代码怎么样，返回的值都不会改变，仍然是之前保存的值），所以函数返回值是在finally执行前确定的；** 
4. **finally中最好不要包含return，否则程序会提前退出，返回值不是try或catch中保存的返回值。** 
5. 任何执行try 或者catch中的return语句之前，都会先执行finally语句，如果finally中有return语句，那么程序就return了，所以finally中的return是一定会被return的。

### ReentrantLock

```java
import java.util.concurrent.locks.ReentrantLock;
import java.util.logging.Level;
import java.util.logging.Logger;

/**
 * Java program to show, how to use ReentrantLock in Java.
 * Reentrant lock is an alternative way of locking
 * apart from implicit locking provided by synchronized keyword in Java.
 *
 * @author  Javin Paul
 */
public class ReentrantLockHowto {

    private final ReentrantLock lock = new ReentrantLock();
    private int count = 0;

     //Locking using Lock and ReentrantLock
     public int getCount() {
        lock.lock();
        try {
            System.out.println(Thread.currentThread().getName() + " gets Count: " + count);
            return count++;
        } finally {
            lock.unlock();
        }
     }

     //Implicit locking using synchronized keyword
     public synchronized int getCountTwo() {
            return count++;
     }

    

    public static void main(String args[]) {
        final ThreadTest counter = new ThreadTest();
        Thread t1 = new Thread() {

            @Override
            public void run() {
                while (counter.getCount() &lt; 6) {
                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException ex) {
                        ex.printStackTrace();                    }
                }
            }
        };
      
        Thread t2 = new Thread() {

            @Override
            public void run() {
                while (counter.getCount() &lt; 6) {
                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException ex) {
                        ex.printStackTrace();
                    }
                }
            }
        };
      
        t1.start();
        t2.start();
      
    }
}
```

```
Output:
Thread-0 gets Count: 0
Thread-1 gets Count: 1
Thread-1 gets Count: 2
Thread-0 gets Count: 3
Thread-1 gets Count: 4
Thread-0 gets Count: 5
Thread-0 gets Count: 6
Thread-1 gets Count: 7
```

## stream

### Creating Java Streams

1. We can use Stream.of() to create a stream from similar type of data. For example, we can create Java Stream of integers from a group of int or Integer objects.

```java
Stream<Integer> stream = Stream.of(1,2,3,4);
```

2. We can use Stream.of() with an array of Objects to return the stream. Note that it doesn’t support autoboxing, so we can’t pass primitive type array.

```java
Stream<Integer> stream = Stream.of(new Integer[]{1,2,3,4}); 
//works fine

Stream<Integer> stream1 = Stream.of(new int[]{1,2,3,4}); 
//Compile time error, Type mismatch: cannot convert from Stream<int[]> to Stream<Integer>
```

3. We can use Collection stream() to create sequential stream and parallelStream() to create parallel stream.

```java
List<Integer> myList = new ArrayList<>();
for(int i=0; i<100; i++) myList.add(i);
		
//sequential stream
Stream<Integer> sequentialStream = myList.stream();
		
//parallel stream
Stream<Integer> parallelStream = myList.parallelStream();
```

4. We can use Stream.generate() and Stream.iterate() methods to create Stream.

```java
Stream<String> stream1 = Stream.generate(() -> {return "abc";});
Stream<String> stream2 = Stream.iterate("abc", (i) -> i);
```

5. Using Arrays.stream() and String.chars() methods.

```java
LongStream is = Arrays.stream(new long[]{1,2,3,4});
IntStream is2 = "abc".chars();
```

### Converting Java Stream to Collection or Array

1. We can use java Stream collect() method to get List, Map or Set from stream.

```java
Stream<Integer> intStream = Stream.of(1,2,3,4);
List<Integer> intList = intStream.collect(Collectors.toList());
System.out.println(intList); //prints [1, 2, 3, 4]

intStream = Stream.of(1,2,3,4); //stream is closed, so we need to create it again
Map<Integer,Integer> intMap = intStream.collect(Collectors.toMap(i -> i, i -> i+10));
System.out.println(intMap); //prints {1=11, 2=12, 3=13, 4=14}
```

2. We can use stream toArray() method to create an array from the stream.

```java
Stream<Integer> intStream = Stream.of(1,2,3,4);
Integer[] intArray = intStream.toArray(Integer[]::new);
System.out.println(Arrays.toString(intArray)); //prints [1, 2, 3, 4]
```

### Java Stream Intermediate Operations

1. Stream filter() : We can use filter() method to test stream elements for a condition and generate filtered list.

```java
List<Integer> myList = new ArrayList<>();
for(int i=0; i<100; i++) myList.add(i);
Stream<Integer> sequentialStream = myList.stream();

Stream<Integer> highNums = sequentialStream.filter(p -> p > 90); //filter numbers greater than 90
System.out.print("High Nums greater than 90=");
highNums.forEach(p -> System.out.print(p+" "));
//prints "High Nums greater than 90=91 92 93 94 95 96 97 98 99 "
```

2. Stream map() : We can use map() to apply functions to an stream. Let’s see how we can use it to apply upper case function to a list of Strings.

```java
Stream<String> names = Stream.of("aBc", "d", "ef");
System.out.println(names.map(s -> {
		return s.toUpperCase();
	}).collect(Collectors.toList()));
//prints [ABC, D, EF]
```

3. Stream sorted() : We can use sorted() to sort the stream elements by passing Comparator argument.

```java
Stream<String> names2 = Stream.of("aBc", "d", "ef", "123456");
List<String> reverseSorted = names2.sorted(Comparator.reverseOrder()).collect(Collectors.toList());
System.out.println(reverseSorted); // [ef, d, aBc, 123456]

Stream<String> names3 = Stream.of("aBc", "d", "ef", "123456");
List<String> naturalSorted = names3.sorted().collect(Collectors.toList());
System.out.println(naturalSorted); //[123456, aBc, d, ef]
```

4. Stream flatMap() : We can use flatMap() to create a stream from the stream of list. Let’s see a simple example to clear this doubt.

```java
Stream<List<String>> namesOriginalList = Stream.of(
	Arrays.asList("Pankaj"), 
	Arrays.asList("David", "Lisa"),
	Arrays.asList("Amit"));
//flat the stream from List<String> to String stream
Stream<String> flatStream = namesOriginalList
	.flatMap(strList -> strList.stream());

flatStream.forEach(System.out::println);
```

### Java Stream Terminal Operations

1. Stream reduce() example: We can use reduce() to perform a reduction on the elements of the stream, using an associative accumulation function, and return an Optional. Let’s see how we can use it multiply the integers in a stream.

```java
Stream<Integer> numbers = Stream.of(1,2,3,4,5);
		
Optional<Integer> intOptional = numbers.reduce((i,j) -> {return i*j;});
if(intOptional.isPresent()) System.out.println("Multiplication = "+intOptional.get()); //120
```

2. Stream count() example: We can use this terminal operation to count the number of items in the stream.

```java
Stream<Integer> numbers1 = Stream.of(1,2,3,4,5);
System.out.println("Number of elements in stream="+numbers1.count()); //5
```

3. Stream forEach() example: This can be used for iterating over the stream. We can use this in place of iterator. Let’s see how to use it for printing all the elements of the stream.

```java
Stream<Integer> numbers2 = Stream.of(1,2,3,4,5);
numbers2.forEach(i -> System.out.print(i+",")); //1,2,3,4,5,
```

4. Stream match() examples: Let’s see some of the examples for matching methods in Stream API.

```java
Stream<Integer> numbers3 = Stream.of(1,2,3,4,5);
System.out.println("Stream contains 4? "+numbers3.anyMatch(i -> i==4));
//Stream contains 4? true

Stream<Integer> numbers4 = Stream.of(1,2,3,4,5);
System.out.println("Stream contains all elements less than 10? "+numbers4.allMatch(i -> i<10));
//Stream contains all elements less than 10? true

Stream<Integer> numbers5 = Stream.of(1,2,3,4,5);
System.out.println("Stream doesn't contain 10? "+numbers5.noneMatch(i -> i==10));
//Stream doesn't contain 10? true
```

5. Stream findFirst() :This is a short circuiting terminal operation, let’s see how we can use it to find the first string from a stream starting with D.

```java
Stream<String> names4 = Stream.of("Pankaj","Amit","David", "Lisa");
Optional<String> firstNameWithD = names4.filter(i -> i.startsWith("D")).findFirst();
if(firstNameWithD.isPresent()){
	System.out.println("First Name starting with D="+firstNameWithD.get()); //David
}
```

### some example

```java
//merge collection
List<String> resultPersonnos = Stream.of(list1, list2)
                .flatMap(Collection::stream)
                .distinct()
                .sorted()
                .collect(Collectors.toList());
```

