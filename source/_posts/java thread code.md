---
title: java thread code
date: 2018-06-1 10:01:30
tags: code
categories: thread
---

### Future and FutureTask

> - A **Future** interface provides methods **to check if the computation is complete, to wait for its completion and to retrieve the results of the computation**. The result is retrieved using Future’s get() method when the computation has completed, and it blocks until it is completed.
> - **FutureTask**
>		1. FutureTask implementation Future interface and RunnableFuture Interface, means one can use FutureTask as Runnable and can be submitted to ExecutorService for execution.
>		2. When one call Future.submit() Callable or Runnable objects then most of time ExecutorService creates FutureTask, and one can create it manually also.
>		3. FutureTask acts like a latch.
>		4. Computation represent by FutureTask is implemented with Callable interface.
>		5. It implements Future or Callable interface.
>		6. Behaviour of get() method depends on the state of the task. If tasks are not completed get() method waits or blocks till the task is completed. Once task completed, it returns the result or throws an ExecutionException.

![](https://cdncontribute.geeksforgeeks.org/wp-content/uploads/pool2.png)

```java
// Java program do two FutureTask 
// using Runnable Interface 

import java.util.concurrent.*; 
import java.util.logging.Level; 
import java.util.logging.Logger; 

class MyRunnable implements Runnable { 

	private final long waitTime; 

	public MyRunnable(int timeInMillis) 
	{ 
		this.waitTime = timeInMillis; 
	} 

	@Override
	public void run() 
	{ 
		try { 
			// sleep for user given millisecond 
			// before checking again 
			Thread.sleep(waitTime); 

			// return current thread name 
			System.out.println(Thread 
								.currentThread() 
								.getName()); 
		} 

		catch (InterruptedException ex) { 
			Logger 
				.getLogger(MyRunnable.class.getName()) 
				.log(Level.SEVERE, null, ex); 
		} 
	} 
} 

// Class FutureTaskExample excute two future task 
class FutureTaskExample { 

	public static void main(String[] args) 
	{ 
		// create two object of MyRunnable class 
		// for FutureTask and sleep 1000, 2000 
		// millisecond before checking again 
		MyRunnable myrunnableobject1 = new MyRunnable(1000); 
		MyRunnable myrunnableobject2 = new MyRunnable(2000); 

		FutureTask<String> 
			futureTask1 = new FutureTask<>(myrunnableobject1, 
										"FutureTask1 is complete"); 
		FutureTask<String> 
			futureTask2 = new FutureTask<>(myrunnableobject2, 
										"FutureTask2 is complete"); 

		// create thread pool of 2 size for ExecutorService 
		ExecutorService executor = Executors.newFixedThreadPool(2); 

		// submit futureTask1 to ExecutorService 
		executor.submit(futureTask1); 

		// submit futureTask2 to ExecutorService 
		executor.submit(futureTask2); 

		while (true) { 
			try { 

				// if both future task complete 
				if (futureTask1.isDone() && futureTask2.isDone()) { 

					System.out.println("Both FutureTask Complete"); 

					// shut down executor service 
					executor.shutdown(); 
					return; 
				} 

				if (!futureTask1.isDone()) { 

					// wait indefinitely for future 
					// task to complete 
					System.out.println("FutureTask1 output = "
									+ futureTask1.get()); 
				} 

				System.out.println("Waiting for FutureTask2 to complete"); 

				// Wait if necessary for the computation to complete, 
				// and then retrieves its result 
				String s = futureTask2.get(250, TimeUnit.MILLISECONDS); 

				if (s != null) { 
					System.out.println("FutureTask2 output=" + s); 
				} 
			} 

			catch (Exception e) { 
				Sysmtem.out.println("Exception: " + e); 
			} 
		} 
	} 
} 
```

```
Output:

FutureTask1 output=FutureTask1 is complete
Waiting for FutureTask2 to complete
Waiting for FutureTask2 to complete
Waiting for FutureTask2 to complete
Waiting for FutureTask2 to complete
FutureTask2 output=FutureTask2 is complete
Both FutureTask Complete
```

