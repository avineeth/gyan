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
### Race Condition
- race condition occurs when two or more threads can access shared data and they try to change it at the same time. Because the thread scheduling algorithm can swap between threads at any time, you don't know the order in which the threads will attempt to access the shared data. Therefore, the result of the change in data is dependent on the thread scheduling algorithm, i.e. both threads are "racing" to access/change the data.

- Problems often occur when one thread does a "check-then-act" (e.g. "check" if the value is X, then "act" to do something that depends on the value being X) and another thread does something to the value in between the "check" and the "act". E.g:

```
if (x == 5) // The "Check"
{
   y = x * 2; // The "Act"

   // If another thread changed x in between "if (x == 5)" and "y = x * 2" above,
   // y will not be equal to 10.
}
```
- The point being, y could be 10, or it could be anything, depending on whether another thread changed x in between the check and act. You have no real way of knowing.

- In order to prevent race conditions from occurring, you would typically put a lock around the shared data to ensure only one thread can access the data at a time. This would mean something like this:
```
// Obtain lock for x
if (x == 5)
{
   y = x * 2; // Now, nothing can change x until the lock is released. 
              // Therefore y = 10
}
// release lock for x
```

### What is a deadlock

- Deadlock in Java is a condition where two or more threads are blocked forever, waiting for each other.
- Deadlocks will only occur when you have two or more locks that can be aquired at the same time and they are grabbed in different order.
- Ways to avoid having deadlocks are:
1. avoid having locks (if possible),
2. avoid having more than one lock
3. always take the locks in the same order

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

#### join()
The current thread calls join(), via another thread's thread object reference when it wants to wait for that other thread to terminate. 

#### yield() method

Theoretically, to ‘yield’ means to let go, to give up, to surrender. A yielding thread tells the virtual machine that it’s willing to let other threads be scheduled in its place. This indicates that it’s not doing something too critical. Note that it’s only a hint, though, and not guaranteed to have any effect at all.

yield() is defined as following in Thread.java.
```
/**
  * A hint to the scheduler that the current thread is willing to yield its current use of a processor. The scheduler is free to ignore
  * this hint. Yield is a heuristic attempt to improve relative progression between threads that would otherwise over-utilize a CPU. 
  * Its use should be combined with detailed profiling and benchmarking to ensure that it actually has the desired effect. 
  */
 
public static native void yield();
```
Let’s list down important points from above definition:

- Yield is a Static method and Native too.
- Yield tells the currently executing thread to give a chance to the threads that have equal priority in the Thread Pool.
- There is no guarantee that Yield will make the currently executing thread to runnable state immediately.
- It can only make a thread from Running State to Runnable State, not in wait or blocked state.

#### Interrupts

- An interrupt is an indication to a thread that it should stop what it is doing and do something else. It's up to the programmer to decide exactly how a thread responds to an interrupt, but it is very common for the thread to terminate.
- If the thread is frequently invoking methods that throw InterruptedException, it simply returns from the run method after it catches that exception. 
  - Many methods that throw InterruptedException, such as sleep, are designed to cancel their current operation and return immediately when an interrupt is received.
 ```
 for (int i = 0; i < importantInfo.length; i++) {
    // Pause for 4 seconds
    try {
        Thread.sleep(4000);
    } catch (InterruptedException e) {
        // We've been interrupted: no more messages.
        return;
    }
    // Print a message
    System.out.println(importantInfo[i]);
 }
```
- What if a thread goes a long time without invoking a method that throws InterruptedException? 
  - Then it must periodically invoke Thread.interrupted, which returns true if an interrupt has been received. For example:
```
for (int i = 0; i < inputs.length; i++) {
    heavyCrunch(inputs[i]);
    if (Thread.interrupted()) {
        // We've been interrupted: no more crunching.
        return;
    }
}
```
- The interrupt mechanism is implemented using an internal flag known as the interrupt status. Invoking Thread.interrupt sets this flag.

### Blocking and interruptible methods
- Threads may block, or pause, for several reasons: waiting for I/O completion,waiting to acquire a lock, waiting to wake up from Thread.sleep, or waiting for the result of a computation in another thread. 
- When a thread blocks, it is usually suspended and placed in one of the blocked thread states (BLOCKED, WAITING, or TIMED_WAITING).
- The distinction between a blocking operation and an ordinary operation that merely takes a long time to finish is that a blocked thread must wait for an event that is beyond its control before it can proceed—the I/O completes, the lock becomes available, or the external computation finishes.
- When that external event occurs, the thread is placed back in the RUNNABLE state and becomes eligible again for scheduling.
- The put and take methods of BlockingQueue throw the checked InterruptedException, as do a number of other library methods such as Thread.sleep.
- When a method can throw InterruptedException, it is telling you that it is a blocking method, and further that if it is interrupted, it will make an effort to stop blocking early.
- Thread provides the interrupt method for interrupting a thread and for querying whether a thread has been interrupted. Each thread has a boolean property that represents its interrupted status; interrupting a thread sets this status.
- Interruption is a cooperative mechanism. One thread cannot force another to stop what it is doing and do something else; when thread A interrupts thread B, A is merely requesting that B stop what it is doing when it gets to a convenient stopping point—if it feels like it. 
- While there is nothing in the API or language specification that demands any specific application-level semantics for interruption,the most sensible use for interruption is to cancel an activity. 
- Blocking methods that are responsive to interruption make it easier to cancel long-running activities on a timely basis.

- When your code calls a method that throws InterruptedException, then your method is a blocking method too, and must have a plan for responding to interruption.
- For library code, there are basically two choices:
1. **Propagate the InterruptedException.** This is often the most sensible policy if you can get away with it—just propagate the InterruptedException to your caller. This could involve not catching InterruptedException, or catching it and throwing it again after performing some brief activity-specific cleanup.
2. **Restore the interrupt.** Sometimes you cannot throw InterruptedException, for instance when your code is part of a Runnable. In these situations, you must catch InterruptedException and restore the interrupted status by calling interrupt on the current thread, so that code higher up the call stack can

- **But there is one thing you should not do with InterruptedException—catch it and do nothing in response.** This deprives code higher up on the call stack of the opportunity to act on the interruption, because the evidence that the thread was interrupted is lost.

```
	public class TaskRunnable implements Runnable {
		BlockingQueue<Task> queue;
		...
		public void run() {
			try {
				processTask(queue.take());
			} catch (InterruptedException e) {
				// restore interrupted status
				Thread.currentThread().interrupt();
			}
		}
	}
```

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

#### Difference between Wait and Sleep, Yield in Java

Read more: http://javarevisited.blogspot.com/2011/12/difference-between-wait-sleep-yield.html#ixzz4mNrQPCiQ


### Volatile keyword

- The Java volatile keyword is used to mark a Java variable as **"being stored in main memory"**. More precisely that means, that every read of a volatile variable will be read from the computer's main memory, and not from the CPU cache, and that every write to a volatile variable will be written to main memory, and not just to the CPU cache.
- In a multithreaded application where the threads operate on non-volatile variables, each thread may copy variables from main memory into a CPU cache while working on them, for performance reasons. If your computer contains more than one CPU, each thread may run on a different CPU. That means, that each thread may copy the variables into the CPU cache of different CPUs.
- With non-volatile variables there are no guarantees about when the Java Virtual Machine (JVM) reads data from main memory into CPU caches, or writes data from CPU caches to main memory. 
- If Thread A writes to a volatile variable and Thread B subsequently reads the same volatile variable, then **all variables visible to Thread A** before writing the volatile variable, will also be visible to Thread B after it has read the volatile variable. 
- The reading and writing instructions of volatile variables **cannot be reordered by the JVM** (the JVM may reorder instructions for performance reasons as long as the JVM detects no change in program behaviour from the reordering). Instructions before and after can be reordered, but the volatile read or write cannot be mixed with these instructions. Whatever instructions follow a read or write of a volatile variable are guaranteed to happen after the read or write.
- Locking can guarantee both Visibility and Atomicity. Volatile variables can only guarantee Visibility.
- Use Volatile variables when Writes to the variable doesnt depend on its current value (like a status flag).
  

### Monitors

[Source] https://www.artima.com/insidejvm/ed2/threadsynchP.html

- Monitors are the mechanism that Java uses to support synchronization is the monitor.
- Java's monitor supports two kinds of thread synchronization: **mutual exclusion** and **cooperation**.
- Mutual exclusion, which is supported in the Java virtual machine via **object locks (mutex)**, enables multiple threads to independently work on shared data without interfering with each other. 
- Cooperation, which is supported in the Java virtual machine via the **wait and notify methods of class Object**, enables threads to work together towards a common goal.

- A monitor is like a building that contains one special room that can be occupied by only one thread at a time. The room usually contains some data. From the time a thread enters this room to the time it leaves, it has exclusive access to any data in the room. Entering the monitor building is called **entering the monitor.** Entering the special room inside the building is called **acquiring the monitor.** Occupying the room is called **owning the monitor,** and leaving the room is called **releasing the monitor.** Leaving the entire building is called **exiting the monitor.**
- A monitor region is code that needs to be executed as one indivisible operation with respect to a particular monitor. In other words, one thread must be able to execute a monitor region from beginning to end without another thread concurrently executing a monitor region of the same monitor. A monitor enforces this one-thread-at-a-time execution of its monitor regions. The only way a thread can enter a monitor is by arriving at the beginning of one of the monitor regions associated with that monitor. The only way a thread can move forward and execute the monitor region is by acquiring the monitor.

- When a thread arrives at the beginning of a monitor region (synchronized region..explained later), it is placed into an **entry set** for the associated monitor. The entry set is like the front hallway of the monitor building. If no other thread is waiting in the entry set and no other thread currently owns the monitor, the thread acquires the monitor and continues executing the monitor region. When the thread finishes executing the monitor region, it exits (and releases) the monitor.
- If a thread arrives at the beginning of a monitor region that is protected by a monitor already owned by another thread, the newly arrived thread must wait in the entry set. When the current owner exits the monitor, the newly arrived thread must compete with any other threads also waiting in the entry set. Only one thread will win the competition and acquire the monitor.

- Cooperation is important when one thread needs some data to be in a particular state and another thread is responsible for getting the data into that state. For example, one thread, a "read thread," may be reading data from a buffer that another thread, a "write thread," is filling. The read thread needs the buffer to be in a "not empty" state before it can read any data out of the buffer. If the read thread discovers that the buffer is empty, it must wait. The write thread is responsible for filling the buffer with data. Once the write thread has done some more writing, the read thread can do some more reading.
- The form of monitor used by the Java virtual machine is called a "Wait and Notify" monitor. (It is also sometimes called a "Signal and Continue" monitor.) In this kind of monitor, a thread that currently owns the monitor can suspend itself inside the monitor by executing a wait command. When a thread executes a wait, it releases the monitor and enters a wait set. The thread will stay suspended in the wait set until some time after another thread executes a notify command inside the monitor. When a thread executes a notify, it continues to own the monitor until it releases the monitor of its own accord, either by executing a wait or by completing the monitor region. After the notifying thread has released the monitor, the waiting thread will be resurrected and will reacquire the monitor.
- As a result, a notify must often be considered by waiting threads merely as a hint that the desired state may exist. Each time a waiting thread is resurrected, it may need to check the state again to determine whether it can move forward and do useful work. If it finds the data still isn't in the desired state, the thread could execute another wait or give up and exit the monitor.

- The Java virtual machine offers two kinds of notify commands: "notify" and "notify all." A notify command selects one thread arbitrarily from the wait set and marks it for eventual resurrection. A notify all command marks all threads currently in the wait set for eventual resurrection.
- To a great extent, the manner in which a Java virtual machine implementation selects the next thread from the wait or entry sets is a decision of individual implementation designers. 
- For example, implementation designers can decide how to select: 
  - a thread from the wait set given a notify command 
  - the order to resurrect threads from the wait set given a notify all command 
  - the order to allow threads from the entry set to acquire the monitor 
  - how to choose between threads suspended in the wait set versus the entry set after a notify command

![Image](https://github.com/avineeth/gyan/blob/master/img/monitor.gif?raw=true)  

#### Object Locking

- Because the heap and method area are shared by all threads, Java programs need to coordinate multi-threaded access to two kinds of data: 
  - instance variables, which are stored on the heap 
  - class variables, which are stored in the method area.
- Programs never need to coordinate access to local variables, which reside on Java stacks, because data on the Java stack is private to the thread to which the Java stack belongs.
- In the Java virtual machine, every object and class is logically associated with a monitor. 
- For objects, the associated monitor protects the object's instance variables. For classes, the monitor protects the class's class variables. If an object has no instance variables, or a class has no class variables, the associated monitor protects no data.

- To implement the mutual exclusion capability of monitors, the Java virtual machine associates a lock (sometimes called a mutex) with each object and class.
- A lock is like a privilege that only one thread can "own" at any one time. Threads need not obtain a lock to access instance or class variables. If a thread does obtain a lock, however, no other thread can obtain a lock on the same data until the thread that owns the lock releases it. (To "lock an object" is to acquire the monitor associated with that object.)
- Class locks are actually implemented as object locks. When the Java virtual machine loads a class file, it creates an instance of class java.lang.Class. When you lock a class, you are actually locking that class's Class object.

- A thread in the Java virtual machine requests a lock when it arrives at the beginning of a monitor region. 
- In Java, there are two kinds of monitor regions: synchronized statements and synchronized methods. 
- Each monitor region in a Java program is associated with an object reference. 
- When a thread arrives at the first instruction in a monitor region, the thread must obtain a lock on the referenced object.
- The thread is not allowed to execute the code until it obtains the lock. Once it has obtained the lock, the thread enters the block of protected code. When the thread leaves the block, no matter how it leaves the block, it releases the lock on the associated object.

- Note that as a Java programmer, you never explicitly lock an object. Object locks are internal to the Java virtual machine.
- In your Java programs, **you identify the monitor regions of your program by writing synchronized statements and methods**. As the Java virtual machine runs your program, it automatically locks an object or class every time it encounters a monitor region.

##### Object level locking:
Object level locking means you want to synchronize non static method or block so that it can be accessed by only one thread at a time for that instance. It is used if you want to protect non static data.
```
public synchronized int incrementCount(){}
public int incrementCount() {
  synchronized (this) {
   count++;
   return count;
  }
```
##### Class level locking:
Class level locking means you want to synchronize static method or block so that it can be accessed by only one thread for whole class. If you have 10 instances of class, only one thread will be able to access only one method or block of any one instance at a time. It is used if you want to protect static data.

```
public static synchronized int incrementCount()
{
}
public int incrementCount() {
  synchronized (RequestCounter.class) {
   count++;
   return count;
  }
  ```
  
##### Re-entrant Lock 
  - A single thread is allowed to lock the same object multiple times.
  - For each object, the Java virtual machine maintains a count of the number of times the object has been locked.
  - An unlocked object has a count of zero. When a thread acquires the lock for the first time, the count is again incremented to one. Each time the thread acquires a lock on the same object, the count is again incremented. 
  - (Only the thread that already owns an object's lock is allowed to lock it again. As mentioned previously, no other thread can lock the object until the owning thread releases the lock.) 
  - Each time the thread releases the lock, the count is decremented. When the count reaches zero, the lock is released and made available to other threads.

#### Mutex vs Semaphore

- Mutex is basically mutual exclusion. Only one thread can acquire the resource at once. When one thread acquires the resource, no other thread is allowed to acquire the resource until the thread owning the resource releases. All threads waiting for acquiring resource would be blocked.

- Semaphore is used to control the number of threads executing. There will be fixed set of resources. The resource count will gets decremented every time when a thread owns the same. When the semaphore count reaches 0 then no other threads are allowed to acquire the resource. The threads get blocked till other threads owning resource releases.

- In short, the main difference is how many threads are allowed to acquire the resource at once ?
  - Mutex --its ONE.
  - Semaphore -- its DEFINED_COUNT, ( as many as semaphore count)

- Semaphores have no notion of ownership, this means that any thread can release a semaphore (this can lead to many problems in itself but can help with "death detection").
- Whereas a mutex does have the concept of ownership (i.e. you can only release a mutex you have acquired).

#### Synchronization Support in the Instruction Set

- As mentioned earlier, the language provides two built-in ways to identify monitor regions in your programs: synchronized statements and synchronized methods. These two mechanisms, which implement the mutual exclusion aspect of synchronization, are supported by the Java virtual machine's instruction set.

- Synchronized Statements To create a synchronized statement, you use the synchronized keyword with an expression that evaluates to an object reference, as in the reverseOrder() method below:
- the statements contained within the synchronized block will not be executed until a lock is acquired on the current object (this). If instead of a this reference, the expression yielded a reference to another object, the lock associated with that object would be acquired before the thread continued. If the expression yields a reference to an instance of class Class, the lock associated with the class is acquired.

- monitorenter and monitorexit: Two opcodes, monitorenter and monitorexit, are used for synchronization blocks within methods. These opcodes are shown in the Table 20-1. Table 20-1. Monitors
- When monitorenter is encountered by the Java virtual machine, it acquires the lock for the object referred to by objectref on the stack. If the thread already owns the lock for that object, the count that is associated with the lock is incremented. Each time monitorexit is executed for the thread on the object, the count is decremented. When the count reaches zero, the monitor is released.

- Class Object declares five methods that enable programmers to access the Java virtual machine's support for the coordination aspect of synchronization. 

1. void wait();	 - Enter a monitor's wait set until notified by another thread
2. void wait(long timeout);  - Enter a monitor's wait set until notified by another thread or timeout milliseconds elapses
3. void wait(long timeout, int nanos);	-Enter a monitor's wait set until notified by another thread or timeout milliseconds plus nanos nanoseconds elapses
4. void notify(); - Wake up one thread waiting in the monitor's wait set. (If no threads are waiting, do nothing.)
5. void notifyAll(); - Wake up all threads waiting in the monitor's wait set. (If no threads are waiting, do nothing.)

####  Thread States
- NEW : Thread state for a thread which has not yet started.
- RUNNABLE : Thread state for a runnable thread. A thread in the runnable state is executing in the Java virtual machine but it may be waiting for other resources from the operating system such as processor.
- BLOCKED : Thread state for a thread blocked waiting for a monitor lock. A thread in the blocked state is waiting for a monitor lock to enter a synchronized block/method or reenter a synchronized block/method after calling Object.wait.
- WAITING: Thread state for a waiting thread. A thread is in the waiting state due to calling one of the following methods:
   - Object.wait with no timeout
   - Thread.join with no timeout
   - LockSupport.park A thread in the waiting state is waiting for another thread to perform a particular action. For example, a thread that has called Object.wait() on an object is waiting for another thread to call Object.notify() or Object.notifyAll() on that object. A thread that has called Thread.join() is waiting for a specified thread to terminate.
- TIMED_WAITING : Thread state for a waiting thread with a specified waiting time. A thread is in the timed waiting state due to calling one of the following methods with a specified positive waiting time:
  - Thread.sleep
  - Object.wait with timeout
  - Thread.join with timeout
  - LockSupport.parkNanos
  - LockSupport.parkUntil
- TERMINATED : Thread state for a terminated thread. The thread has completed execution.

![Image](https://github.com/avineeth/gyan/blob/master/img/thread_states.png?raw=true)  


### Synchronized Collections
- Legacy Classes like Vector and Hastable are synchronized collection classes.
- also Synchronized collection wrappers like Collections.synchronizedXxx exist.
- These achieve thread safety by encapsulating their state and synchronzing every public method so that only one thread can access the collection state.
- though synchronized collections are thread-safe, there would be need to add additional client-side locking to guard compound actions.
- If an explicit iterator is used, the iterator method must be called from within the synchronized block. Failure to follow this advice may result in nondeterministic behavior

```
Map<KeyType, ValType> m = Collections.synchronizedMap(new HashMap<KeyType, ValType>());
    ...
Set<KeyType> s = m.keySet();
    ...
// Synchronizing on m, not s!
synchronized(m) {
    while (KeyType k : s)
        foo(k);
}
```

### ConcurrentModificationException

- The iterators returned by the synchronized collections are not designed to deal with concurrent modification, and they are **fail-fast**—meaning that if they detect that the collection has changed since iteration began, they throw the unchecked ConcurrentModificationException.
- These fail-fast iterators are not designed to be foolproof—they are designedto catch concurrency errors on a “good-faith-effort” basis and thus act only as early-warning indicators for concurrency problems. 
- They are implemented by associating a modification count with the collection: if the modification count changes during iteration, hasNext or next throws ConcurrentModificationException.
- HashMap contains a variable to count the number of modifications and iterator use it when you call its next() function to get the next entry.
```
HashMap.java

/**
     * The number of times this HashMap has been structurally modified
     * Structural modifications are those that change the number of mappings in
     * the HashMap or otherwise modify its internal structure (e.g.,
     * rehash).  This field is used to make iterators on Collection-views of
     * the HashMap fail-fast.  (See ConcurrentModificationException).
     */
    transient volatile int modCount;
 ```
 
- Note that Iterator.remove is the only safe way to modify a collection during iteration; the behavior is unspecified if the underlying collection is modified in any other way while the iteration is in progress.

```
public static void main(String args[]) {

		Map<String,String> emp = new HashMap<String,String>();
		emp.put("1","david");
		emp.put("2","mary");
		emp.put("3","john");
		
		//If Iterator.remove() is used ConcurrentModificationException is not thrown.
		Iterator iter = emp.entrySet().iterator();
		while(iter.hasNext()) {
			Map.Entry<String,String> entry = (Map.Entry<String,String>)iter.next();
			System.out.println(entry.getKey());
		}
		
		// directly removing from the collection using remove during iteration causes concurrent modification exception.
		for(String empno : emp.keySet() ){
			System.out.println(emp.get(empno));
			emp.remove(empno); //**ConcurrentModificationException**
		}
	}
```
#### What is fail-fast in java?
- A fail-fast system is nothing but immediately report any failure that is likely to lead to failure. When a problem occurs, a fail-fast systemfails immediately. In Java, we can find this behavior with iterators. Incase, you have called iterator on a collection object, and another thread tries to modify the collection object, then concurrent modification exception will be thrown. This is called fail-fast.

### Concurrent Collections
- **ConcurrentHashMap** - a replacement for synchronized hash-based Map implementations
- **CopyOnWriteArrayList** - a replacement for synchronized List implementations for cases where traversal is the dominant operation. 
- **BlockingQueue** - extends Queue to add blocking insertion and retrieval operations. If the queue is empty, a retrieval blocks until an element is available, and if the queue is full (for bounded queues) an insertion blocks until there is space available. Blocking queues are extremely useful in producer-consumer designs.

#### Concurrent HashMap

http://www.docjar.com/html/api/java/util/concurrent/ConcurrentHashMap.java.html

##### How ConcurrentHashMap Works Internally

 - In concurrentHashMap, the difference lies in internal structure to store these key-value pairs. To better visualize the ConcurrentHashMap, let it consider as a group of HashMaps. To get and put key-value pairs from hashmap, you have to calculate the hashcode and look for correct bucket location in array of Collection.Entry.
- ConcurrentHashMap has an addition concept of segments. It will be easier to understand it you think of one segment equal to one HashMap [conceptually].
- Number of segments is called Concurrency-Level which determines number of thread that can write simultaneous. 
- A concurrentHashMap is divided into number of segments [default 16] on initialization.
- This way, if when your key-value pair is stored in segment 10; code does not need to block other 15 segments additionally. This structure provides a very high level of concurrency.
- Reads are completely lock free i.e No need to acquire lock for reading.
- ConcurrentHashMap, along with the other concurrent collections, further improve on the synchronized collection classes by providing iterators that do not throw **ConcurrentModificationException**, thus eliminating the need to lock the collection during iteration.
- The iterators returned by ConcurrentHashMap are **weakly consistent** instead of **fail-fast**. A weakly consistent iterator can tolerate concurrent modification, traverses elements as they existed when the iterator was constructed, and may (but is not guaranteed to) reflect modifications to the collection after the construction of the iterator.
- As with all improvements, there are still a few tradeoffs. The semantics of methods that operate on the entire Map, such as size and isEmpty, have been slightly weakened to reflect the concurrent nature of the collection. Since the result of size could be out of date by the time it is computed, it is really only an estimate, so size is allowed to return an approximation instead of an exact count. -- -- While at first this may seem disturbing, in reality methods like size and isEmpty are far less useful in concurrent environments because these quantities are moving targets. So the requirements for these operations were weakened to enable performance optimizations for the most important operations, primarily get, put, containsKey, and remove.

##### Initialization

- First when you create a ConcurrentHashMap you can provide concurrency level. This determines size of Segment array. Size of segment array will always be equal or more than the concurrency level. If this is not provided default is used - 

```
public ConcurrentHashMap(int initialCapacity,
                 float loadFactor,
                 int concurrencyLevel)
Creates a new, empty map with the specified initial capacity, load factor and concurrency level.
Parameters:
initialCapacity - the initial capacity. The implementation performs internal sizing to accommodate this many elements.
loadFactor - the load factor threshold, used to control resizing. Resizing may be performed when the average number of elements per bin exceeds this threshold.
concurrencyLevel - the estimated number of concurrently updating threads. The implementation performs internal sizing to try to accommodate this many threads.

```
- Note that the size of segment table will always be power of 2. So if you give  concurrency level as 10 then next best power of 2 match will be picked up i.e 16 and Segment array of size 16 will be created which implies 16 threads can simultaneously operate on the map.

##### Putting element in ConcurrentHashMap 

- For putting element in Map we first need to determine which segment the element should be processed for. For this we first get hascode of the key. Next we do a **rehash** of the existing hash.
-  Once hash is calculated you can get the segment which it belongs to and delegate put method to segments put method as follows.

```
 public V put(K key, V value) {
        if (value == null)
            throw new NullPointerException();
        int hash = hash(key.hashCode());
        return segmentFor(hash).put(key, hash, value, false);
    }
```    
- Once put is delegated to segment , segment will add it to the appropriate bucket in the segment.
```
 	V put(K key, int hash, V value, boolean onlyIfAbsent) {
            lock();
            try {
                int c = count;
                if (c++ > threshold) // ensure capacity
                    rehash();
                HashEntry<K,V>[] tab = table;
                int index = hash & (tab.length - 1);
                HashEntry<K,V> first = tab[index];
                HashEntry<K,V> e = first;
                while (e != null && (e.hash != hash || !key.equals(e.key)))
                    e = e.next;
 
                V oldValue;
                if (e != null) {
                    oldValue = e.value;
                    if (!onlyIfAbsent)
                        e.value = value;
                }
                else {
                    oldValue = null;
                    ++modCount;
                    tab[index] = new HashEntry<K,V>(key, hash, first, value);
                    count = c; // write-volatile
                }
                return oldValue;
            } finally {
                unlock();
            }
        }
```	
- First call is to lock(). Since it is a write/update operation on a bucket of same segment we need a lock. If you recollect Segment class it extends ReentrantLock so each segment is a lock. So you can call lock() and unlock() directly in Segment class.
- Next it's like a normal HashMap. You find the index of the Entry table where your elements hash falls and add it there as linked list.
- You can see similar code has HashMap that updates value if key is same, inserts in array if there is no element in the table and adds it in the linked list of the table if element already exists.
- Finally once operation is complete it calls unlock() so that other threads can continue update.
- Note the lock is a blocking call. 
- You can also see call for rehash if threshold is reached. Like Entry array Segment also has a threshold and when it is reached Segment array is resized for performance. That's what rehash. 

##### Getting element from  ConcurrentHashMap : 

- Get on ConcurrentHashMap is very simple no locks involved. You simply read the data and return -
```
        public V get(Object key) {
                int hash = hash(key.hashCode());
                return segmentFor(hash).get(key, hash);
        }
 
        V get(Object key, int hash) {
            if (count != 0) { // read-volatile
                HashEntry<K,V> e = getFirst(hash);
                while (e != null) {
                    if (e.hash == hash && key.equals(e.key)) {
                        V v = e.value;
                        if (v != null)
                            return v;
                        return readValueUnderLock(e); // recheck
                    }
                    e = e.next;
                }
            }
            return null;
        }
```
![Image](https://github.com/avineeth/gyan/blob/master/img/concurrenthaspmap.png?raw=true)  


### CopyOnWriteArrayList

##### Copy-on-write concept
- Copy-on-write is an optimization stategy used in computer programming. 
- Copy-on-write stems from the understanding that when multiple separate tasks **use** identical copies of the same information, it is not necessary to create separate copies of that information for each process, instead they can all be given pointers to the same resource.
- However, when a local copy has been modified, the copy-on-write paradigm has no provision that the shared resource has in the meantime not been updated by another task or tasks.
- So Copy-on-write is therefore only suggested if only the latest update is important and occasional use of a slightly stale value is not harmful. Copy-on-write is the name given to the process of identifying when a task attempts to make a change to the shared information, creating a separate (private) copy of that information for the task and redirecting the task to making changes to the private copy to prevent its changes from becoming visible to all the other tasks

##### CopyOnWriteArrayList 
- as the name suggests, when ever there is a write operation like "Add", "set" so on, it copies the undelying array.
- So basic idea is whenever we add or remove to the CopyOnWriteArrayList, the underlying array is copied with the modification.
- It means, whenever there is modification done by thread that update the ArrayList, all other threads holding an older copy of different array.
- This is ordinarily too costly, **but will be more efficient than alternatives when traversal (read) operations vastly outnumber mutations (writes),** and is useful when you cannot or don't want to synchronize traversals, yet need to preclude interference among concurrent threads.
-  the underlying array reference is marked as volatile so that readers do not need to use a lock to see changes to the referenced array which means array update is an atomic operation and hence reads will always see the array in a consistent state.
- When a write operation occurs this volatile reference is only updated in the final statement via setArray() method. Up until this point any read operations will return elements from the old copy of the array. 
- Also, the write lock is required to prevent concurrent modification, which may result the array holding inconsistent data or changes being lost

```
414   public boolean More ...add(E e) {
415         final ReentrantLock lock = this.lock;
416         lock.lock();
417         try {
418             Object[] elements = getArray();
419             int len = elements.length;
420             Object[] newElements = Arrays.copyOf(elements, len + 1);
421             newElements[len] = e;
422             setArray(newElements);
423             return true;
424         } finally {
425             lock.unlock();
426         }
427     }

```

### Blocking Queue

- BlockingQueue is a queue which is **thread safe** to insert or retrieve elements from it. 
- It provides a mechanism which blocks requests for inserting new elements when the queue is full or requests for removing elements when the queue is empty, with the additional option to stop waiting when a specific timeout passes.
- This functionality makes BlockingQueue a nice way of implementing the Producer-Consumer pattern, as the producing thread can insert elements until the upper limit of BlockingQueue while the consuming thread can retrieve elements until the lower limit is reached.
- BlockingQueue is an Interface, The classes that implement BlockingQueue interface are available in java.util.concurrent package and they are the following:
  - ArrayBlockingQueue
  - DelayQueue
  - LinkedBlockingDeque
  - LinkedBlockingQueue
  - PriorityBlockingQueue
  - SynchronousQueue

Operation | Throws exception | Special value |	Blocks | Times out
----------|------------------|---------------|---------|----------
Insert | add(e) | offer(e) | put(e) | offer(e, time, unit)
Remove | remove() | poll() | take() | poll(time, unit)
Examine | element() | peek() | not applicable |	not applicable


```
import java.util.concurrent.ArrayBlockingQueue;
import java.util.concurrent.BlockingQueue;

/**
 * Created by Vineeth on 7/9/2017.
 */
public class BlockingQueueExample {

    public static void main(String[] args) throws InterruptedException{

        BlockingQueue<Integer> queue = new ArrayBlockingQueue<Integer>(10);
        Producer p = new Producer(queue);
        Consumer c = new Consumer(queue);

        Thread p1 = new Thread(p);
        Thread c1 = new Thread(c);
        p1.start();
        c1.start();

    }
}


class Producer implements Runnable {

    private BlockingQueue<Integer> queue;

    public Producer(BlockingQueue<Integer> q) {
        this.queue = q;

    }

    public void run() {

        for (int i = 0; i < 200; i++) {
            try {
                queue.put(i);
                System.out.println("Produced:[" + i + "]");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

        }
    }
}

class Consumer implements Runnable {

    private BlockingQueue<Integer> queue;

    public Consumer(BlockingQueue<Integer> q) {
        this.queue =q;
    }

    public void run(){


        try {
            while (true) {
                int i = queue.take();
                System.out.println("Consumed:[" + i + "]");
                Thread.sleep(5000);
            }
        }catch(InterruptedException e) { e.printStackTrace();}
    }
}
```

### Synchronizers
- A synchronizer is any object that coordinates the control flow of threads based on its state.
- Main Synchronizers after Java 1.5
  - Semaphores
  - CountDownLatches  
  - Barriers
  - Blocking queues
- All synchronizers share certain structural properties: they encapsulate state that determines whether threads arriving at the synchronizer should be allowed to pass or forced to wait, provide methods to manipulate that state, and provide methods to wait efficiently for the synchronizer to enter the desired state.

### Semaphores
- Counting Semaphore in Java is a synchronizer
- Counting Semaphore in Java maintains specified number of pass or permits, In order to access a shared resource, Current Thread must acquire a permit. 
- If permit is already exhausted by other thread than it can wait until a permit is available due to release of permit from different thread. 
- This concurrency utility can be very useful to implement producer consumer design pattern or implement bounded pool or resources like Thread Pool, DB Connection pool etc.
- Semaphore provides two main method acquire() and release() for getting permits and releasing permits. 
- acquire() method blocks until permit is available. Semaphore provides both blocking method as well as unblocking method to acquire permits. 
```
package concurrent;

import java.util.concurrent.Semaphore;

/**
 * Created by Vineeth on 7/9/2017.
 */
public class CountingSemaphoreExample {

    public static void main(String[] args) {
        Semaphore semaphore = new Semaphore(1);
        Thread e1 = new Thread(new Executer(semaphore));
        Thread a1 = new Thread(new Signaler(semaphore));
        e1.start();
        a1.start();
    }
}

class Executer implements Runnable {

    private Semaphore semaphore;

    public Executer(Semaphore s) {
        this.semaphore = s;
    }

    public void run() {
        while (true) {
            try {
                System.out.println("Aquiring");
                semaphore.acquire();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }

    }
}

class Signaler implements  Runnable {

    private Semaphore semaphore;

    public Signaler(Semaphore s) {
        this.semaphore = s;
    }

    public void run() {
        while(true) {
            System.out.println("Releasing...");
            semaphore.release();
            try {
                Thread.sleep(5000);
            } catch (InterruptedException e) {}
        }

    }
}
```

### Latches
- A latch is a synchronizer that can delay the progress of threads until it reaches its terminal state.
- A latch acts as a gate: until the latch reaches the terminal state the gate is closed and no thread can pass, and in the terminal state the gate opens, allowing all threads to pass.
- Once the latch reaches the terminal state, it cannot change state again, so it remains open forever.
- CountDownLatch is a flexible latch implementation that can be used in any of these situations; it allows one or more threads to wait for a set of events to occur.
- The latch state consists of a counter initialized to a positive number, representing the number of events to wait for. 
- The countDown method decrements the counter,indicating that an event has occurred, 
- the await methods wait for the counter to reach zero, which happens when all the events have occurred.
- If the counter is nonzero on entry, await blocks until the counter reaches zero, the waiting thread is interrupted, or the wait times out.

Example

```
import java.util.concurrent.CountDownLatch;
public class Waiter implements Runnable {

  private CountDownLatch latch;

  public Waiter(CountDownLatch latch) {
    this.latch = latch;
  }

  public void run() {
    try {
        latch.await();
        System.out.println("Waiter Has Started");
    }catch(InterruptedException e) {
      Thread.currentThread().interrupt();
    }
  }
}

```
```
import java.util.concurrent.CountDownLatch;

public class Decrementer implements Runnable {

  private CountDownLatch latch;

  public Decrementer(CountDownLatch latch) {
    this.latch = latch;
  }

  public void run() {
    try{
      latch.countDown();
      System.out.println("Decrementer Counting Down: 1");
      Thread.sleep(2000);
      latch.countDown();
      System.out.println("Decrementer Counting Down: 2");
      Thread.sleep(2000);
      latch.countDown();
      System.out.println("Decrementer Counting Down: 3");
      Thread.sleep(2000);

    }catch(InterruptedException e) {
      Thread.currentThread().interrupt();
    }
  }
}
```

```
import java.util.concurrent.CountDownLatch;

public class CountDownLatchExample {

  public static void main(String[] args) {
    CountDownLatch latch = new CountDownLatch(3);

    Waiter waiter = new Waiter(latch);
    Decrementer decrementer = new Decrementer(latch);

    Thread t1 = new Thread(waiter);
    Thread t2 = new Thread(decrementer);

    t1.start();
    t2.start();
  }

}
```

## Executor

- Executor Framework provides a flexible threadpool implementation.
- It is part of java.util.concurrent package.
- The java.util.concurrent package defines three executor interfaces:
  1. Executor - a simple interface that supports launching new tasks.
  2. ExecutorService - a subinterface of Executor, which adds features that help manage the lifecycle, both for the individual tasks and of the executor itself.
  3. ScheduledExecutorService - a subinterface of ExecutorService, supports future/or periodic execution of tasks.

- A thread pool, as its name suggests, manages a homogeneous pool of worker threads.
- You can create a thread pool by calling one of the static factory methods in Executors:
  - newFixedThreadPool. A fixed-size thread pool creates threads as tasks are submitted, up to the maximum pool size, and then attempts to keep the pool size constant (adding new threads if a thread dies due to an unexpected Exception).
  1. newCachedThreadPool. A cached thread pool has more flexibility to reap idle threads when the current size of the pool exceeds the demand for processing, and to add new threads when demand increases, but places no bounds on the size of the pool.
  2. newSingleThreadExecutor. A single-threaded executor creates a single worker thread to process tasks, replacing it if it dies unexpectedly. Tasks are guaranteed to be processed sequentially according to the order imposed by the task queue (FIFO, LIFO, priority order).
  3. newScheduledThreadPool. A fixed-size thread pool that supports delayed and periodic task execution, similar to Timer. 

- Executing tasks in pool threads has a number of advantages over the threadpertask approach. Reusing an existing thread instead of creating a new one amortizes thread creation and teardown costs over multiple requests.

### ExecutorService
- ExecutorService interface extends Executor, adding a number of methods for lifecycle management (as well as some convenience methods for task submission).
- The lifecycle management methods of ExecutorService are:
```
public interface ExecutorService extends Executor {
void shutdown();
List<Runnable> shutdownNow();
boolean isShutdown();
boolean isTerminated();
boolean awaitTermination(long timeout, TimeUnit unit)
throws InterruptedException;
// ... additional convenience methods for task submission
<T> Future<T>	submit(Callable<T> task);
Future<?>	submit(Runnable task);

}
```
- The lifecycle implied by ExecutorService has three states—running, shuttingdown, and terminated.
- ExecutorServices are initially created in the running state. 
- The shutdown method initiates a graceful shutdown: no new tasks are accepted but previously submitted tasks are allowed to complete—including those that have not yet begun execution.
- The shutdownNow method initiates an abrupt shutdown: it attempts to cancel outstanding tasks and does not start any tasks that are queued but not begun.

- The ExecutorService interface supplements execute with a similar, but more versatile **submit()** method. Like execute, submit accepts Runnable objects, but also accepts **Callable** objects, which allow the task to return a value. The submit method returns a **Future** object, which is used to retrieve the Callable return value and to manage the status of both Callable and Runnable tasks.

```
private static void executorServiceCallable() throws ExecutionException, InterruptedException{
        ExecutorService exec = Executors.newFixedThreadPool(10);

        Future<String> future = exec.submit( new Callable<String>() {
            public String call(){
                System.out.println("This is a callable asynchronous task.");
                try {
                    Thread.sleep(10000);
                }catch (InterruptedException e) {
                    System.out.println("I am Interrupted");
                    e.printStackTrace();
                }
                return "Callable Output";
            }

        });
        //future.cancel(true);
        System.out.println("thread output" + future.get());

        exec.shutdown();
    }
```

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
