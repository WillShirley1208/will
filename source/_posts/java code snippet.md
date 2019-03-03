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