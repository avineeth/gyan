## JAVA Concurrency in Practice

### Thread Safety
- An objects state is in its data, stored in **state variables** such as instance or static fields.
- Whenever more than one thread accesses a given state variable, and one of them might write to it, they all must coordinate their access to it using synchronization.
- If multiple threads access the same mutable state variable without appropriate synchronization, your program is broken. There are three ways to fix it:
  - Dont share the state variable across threads or
  - Make the state variables **immutable** or
  - Use **synchronization** whenever accessing the state variable.
- A class is thread-safe if it behaves correctly when accessed from multiple threads, regardless of the scheduling and intervleaving of the execution of those threads by the runtime environment, and with no additional synchronization or other cordination on the part of the calling code.
- Thread-safe classes encapsulate any needed synchronization so that clients need not provide their own.

#### Stateless Servlet

- the below servlet has no state (instance) variables.
- stateless objects are always thread-safe.

```
public class StatelessFactorizer implements Servlet {
    public void service(ServletRequest req, ServletResponse res) {
        BigInteger i = extractFromRequest(req);
        BigInteger[] factors = factor(i);
        encodeIntoResponse(resp, factors);
    }
}

```

#### Defining and Starting a Thread
- Two ways to Define a thread.
1. Provide a Runnable object.
    - The Runnable interface defines a single method, run, meant to contain the code executed in the thread. The Runnable object is passed to the Thread constructor, as in the HelloRunnable example:

```
public class HelloRunnable implements Runnable {

    public void run() {
        System.out.println("Hello from a thread!");
    }

    public static void main(String args[]) {
        (new Thread(new HelloRunnable())).start();
    }

}
```

2. Subclass Thread. 
   - The Thread class itself implements Runnable, though its run method does nothing. An application can subclass Thread, providing its own implementation of run, as in the HelloThread example:

```
public class HelloThread extends Thread {

    public void run() {
        System.out.println("Hello from a thread!");
    }

    public static void main(String args[]) {
        (new HelloThread()).start();
    }

}
```

Notice that both examples invoke **Thread.start()** in order to start the new thread.

- Thread.sleep causes the current thread to suspend execution for a specified period. 
- This method throws an InterruptedException, an exception that sleep throws when another thread interrupts the current thread while sleep is active.

##### How To Stop A Thread In Java?

- There was a stop() method in Thread class but its deprecated * because of deadlock and other issues.
- There are two ways to stop a thread.

1. By using a boolean volatile variable. 
			- In order to stop the thread, you need to set the value of this boolean variable to true when you want to stop a running thread.
			- Since you are setting this variable from a different thread e.g. main thread, it's important to mark this variable **volatile** to make it thread safe or by using getter and setter methods which are synchronised.

```
public class StoptheThreadExample {
	public static class LongRunningStuff implements Runnable{
		
		private volatile boolean flag = false;
		
		public void run() {
			try {
				for(int i=0; i<1000; i++) {
					if(!flag) {
						System.out.println("Helloo.." +i);
						Thread.sleep(2000);
					}
				}
				System.out.println("Im Dead - Using Volatile");
			}catch(InterruptedException e) {
				System.out.println("Im Dead -Interrupted");
				
			}
		}
		
		public void terminate() {
			flag =true;
		}
	
	}
	
	public static void main(String[] args) throws InterruptedException{
		LongRunningStuff longrunning = new LongRunningStuff();
		Thread t1 = new Thread(longrunning);
		t1.start();
		Thread.sleep(6000);
		longrunning.terminate();
		t1.join();
	}
}
```

2. Using interrupt() Method


//TODO add Simple Thread example here..



three different synchronized Map implementations in the Java API:

Hashtable
Collections.synchronizedMap(Map)
ConcurrentHashMap


Difference between HashMap and ConcurrentHashMap

To better visualize the ConcurrentHashMap, let it consider as a group of HashMaps. To get and put key-value pairs from hashmap, you have to calculate the hashcode and look for correct bucket location in array of Collection.Entry. Rest you have read on previous related article on how hashmap works.

In concurrentHashMap, the difference lies in internal structure to store these key-value pairs. ConcurrentHashMap has an addition concept of segments. It will be easier to understand it you think of one segment equal to one HashMap [conceptually]. A concurrentHashMap is divided into number of segments [default 16] on initialization. ConcurrentHashMap allows similar number (16) of threads to access these segments concurrently so that each thread work on a specific segment during high concurrency.

This way, if when your key-value pair is stored in segment 10; code does not need to block other 15 segments additionally. This structure provides a very high level of concurrency.



### Publishing an Object - Make defensive copies when needed

http://www.informit.com/articles/article.aspx?p=31551&seqNum=2

Returning a reference from a nonprivate method also publishes the returned object.
The caller can modify its contents.

```
public class UnsafeStates {

    private String[] states = {"AZ","NJ","NY", "FL"};

    public String[] getStates() {
        return states;
    }

    public static void main(String[] a) {
        UnsafeStates unsafeStates = new UnsafeStates();
        System.out.println(Arrays.asList(unsafeStates.getStates()));
        String[] statecopy =  unsafeStates.getStates();
        statecopy[0] = "CA";
        System.out.println(Arrays.asList(unsafeStates.getStates()));
    }
}
```
### ThreadLocal in Java

- ThreadLocal in Java is a different way to achieve thread-safety
- It doesn't address synchronization requirement, instead it eliminates sharing by providing explicitly copy of Object to each thread. 
- Since Object is no more shared there is no requirement of Synchronization which can improve performance.
- ThreadLocal are fantastic to implement Per Thread Singleton classes or per thread context information like **transaction id**.
- You can wrap any non Thread Safe object in ThreadLocal and suddenly its uses becomes Thread-safe, as its only being used by Thread Safe. One of the classic example of ThreadLocal is sharing SimpleDateForamt. Since SimpleDateFormat is not thread safe, having a global formatter may not work but having per Thread formatter will certainly work.
- Real Life example of ThreadLocal are in J2EE application servers which uses java ThreadLocal variable to keep track of transaction and security Context.
- It makes lot of sense to share heavy object like Database Connection as ThreadLocal in order to avoid excessive creation and cost of locking in case of sharing global instance.
- Each thread holds an exclusive copy of ThreadLocal variable which becomes eligible to Garbage collection after thread finished or died, normally or due to any Exception, Given those ThreadLocal variable doesn't have any other live references.
- ThreadLocal variables in Java are generally private static fields in Classes and maintain its state inside Thread.
- Using ThreadLocal inside web application is very dangerous as it can cause memory leak if you failed to remove ThreadLocal variable when web application stopped. so just beware of that. ThreadLocal's remove() method can be used to remove variable.

Since values set on a ThreadLocal object only are visible to the thread who set the value, no thread can set an initial value on a ThreadLocal using set() which is visible to all threads.

```
private ThreadLocal myThreadLocal = new ThreadLocal<String>() {
    @Override protected String initialValue() {
        return "This is the initial value";
    }
}; 
```

Example

```
public class ThreadLocalExample {

    public static class MyRunnable implements Runnable {
        private ThreadLocal<Integer> threadlocal = new ThreadLocal<Integer>();
        private Integer nonthreadlocal = new Integer((int)(Math.random() *100D));

        public void run(){
            threadlocal.set((int) (Math.random() *100D));

            try{
              Thread.sleep(2000);
            }catch(InterruptedException e) {
              e.printStackTrace();
            }

            System.out.println("ThreadLocal:" + threadlocal.get());
            System.out.println("Non-ThreadLocal:" + nonthreadlocal);
        }
    }

    public static void main(String[] args) throws Exception{

      MyRunnable sharedRunnable = new MyRunnable();
      Thread t1 = new Thread(sharedRunnable);
      Thread t2 = new Thread(sharedRunnable);

      t1.start();
      t2.start();

      t1.join();
      t2.join();
    }
}

```
