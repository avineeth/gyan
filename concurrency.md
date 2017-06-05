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

#### join()
The current thread calls join(), via another thread's thread object reference when it wants to wait for that other thread to terminate. 

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

##### Re-entrant Lock 
  - A single thread is allowed to lock the same object multiple times.
  - For each object, the Java virtual machine maintains a count of the number of times the object has been locked.
  - An unlocked object has a count of zero. When a thread acquires the lock for the first time, the count is again incremented to one. Each time the thread acquires a lock on the same object, the count is again incremented. 
  - (Only the thread that already owns an object's lock is allowed to lock it again. As mentioned previously, no other thread can lock the object until the owning thread releases the lock.) 
  - Each time the thread releases the lock, the count is decremented. When the count reaches zero, the lock is released and made available to other threads.


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
 
//TODO add EmployeeDB.java

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

//TODO Blocking Queues

### Synchronizers

- A synchronizer is any object that coordinates the control flow of threads based on its state.
- Main Synchronizers after Java 1.5
  - CountDownLatches
  - Semaphores
  - Barriers
  - Blocking queues
- All synchronizers share certain structural properties: they encapsulate state that determines whether threads arriving at the synchronizer should be allowed to pass or forced to wait, provide methods to manipulate that state, and provide methods to wait efficiently for the synchronizer to enter the desired state.


#### Latches
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
