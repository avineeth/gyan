TODO:

- Thread Dumps
- GC Pauses
# Garbage Collection

### JVM Generations

- Emperical analysis of objects have shown that objects are short lived.
- Therefore the heap is divided into smaller parts or generations.
  - Young Generation
  - Old or Tenured Generation
  - Permanent Generation (before Java 8) / Metaspace (Java 8 onwards)
![Image](https://github.com/avineeth/gyan/blob/master/img/jvm_metapsace.png?raw=true)

#### Stop the world
- The term is "stop-the-world." Stop-the-world will occur no matter which GC algorithm you choose. Stop-the-world means that the JVM is stopping the application from running to execute a GC. When stop-the-world occurs, every thread except for the threads needed for the GC will stop their tasks. The interrupted tasks will resume only after the GC task has completed. GC tuning often means reducing this stop-the-world time.

- Young generation: Most of the newly created objects are located here. Since most objects soon become unreachable, many objects are created in the young generation, then disappear. When objects disappear from this area, we say a "minor GC" has occurred. Minor garbage collections are always stop the world events.

- Old generation: The objects that did not become unreachable and survived from the young generation are copied here. It is generally larger than the young generation. As it is bigger in size, the GC occurs less frequently than in the young generation. When objects disappear from the old generation, we say a "major GC" (or a "full GC") has occurred. Major garbage collection are also Stop the World events, often a major gc is much slower because it involves all live objects, so major gc should be minimized.

- The permanent generation is also called the "method area," and it stores classes or interned character strings (until java 7). So, this area is definitely not for objects that survived from the old generation to stay permanently. A GC may occur in this area. The GC that took place here is still counted as a major GC. 
![Image](https://github.com/avineeth/gyan/blob/master/img/JavaGarbageCollectionheap.png?raw=true)

### Garbage collection process
refer the link below:
http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/index.html


#### What if an object in the old generation need to reference an object in the young generation?

- To handle these cases, there is something called the a "card table" in the old generation, which is a 512 byte chunk.
- Whenever an object in the old generation references an object in the young generation, it is recorded in this table.
- When a GC is executed for the young generation, only this card table is searched to determine whether or not it is subject for GC, instead of checking the reference of all the objects in the old generation.
- This card table is managed with write barrier. This write barrier is a device that allows a faster performance for minor GC. Though a bit of overhead occurs because of this, the overall GC time is reduced. 
- Write barrier - a piece of code executed whenever a member variable (of a reference type) is assigned/written to. If the new reference points to a young object and it's stored in an old object, the write barrier records that fact for the garbage collect.

![Image](https://github.com/avineeth/gyan/blob/master/img/card-table-structure.png?raw=true)


#### Where are Static methods and Variables stored?
- Static methods (in fact all methods) as well as static variables are stored in the PermGen section of the heap, since they are part of the reflection data (class related data, not instance related).
- Note that only the variables and their technical values (primitives or references) are stored in PermGen space. If your static variable is a reference to an object that object itself is stored in the normal sections of the heap (young/old generation or survivor space). Those objects (unless they are interal objects like classes etc.) are not stored in PermGen space.

- Example:
```
static int i = 1; //the value 1 is stored in the permgen section
static Object o = new SomeObject(); //the reference(pointer/memory address) is stored in the permgen section, the object itself is not.
```

#### Tenuring Threshold

- Each object in Java heap has a header which is used by Garbage Collection (GC) algorithm. 
- The young space collector (which is responsible for object promotion) uses a few bit(s) from this header to track the number of collections object that have survived (32-bit JVM use 4 bits for this, 64-bit probably some more).
- During young space collection, every single object is copied. The Object may be copied to one of survival spaces (one which is empty before young GC) or to the old space.
- For each object being copied, GC algorithm increases it's age (number of collection survived) and if the age is above the current tenuring threshold it would be copied (promoted) to old space. 
- The Object could also be copied to the old space directly if the survival space gets full (overflow).
- The journey of Object has the following pattern:
  - allocated in eden
  - copied from eden to survival space due to young GC
  - copied from survival to (other) survival space due to young GC (this could happen few times)
  - promoted from survival (or possible eden) to old space due to young GC (or full GC)
  - the actual tenuring threshold is dynamically adjusted by JVM, but MaxTenuringThreshold sets an upper limit on it.
- If you set MaxTenuringThreshold=0, all objects will be promoted immediately. Default value is 15 in Garbage First (G1) Garbage Collector

http://javarevisited.blogspot.in/2011/04/garbage-collection-in-java.html

- According to JDK 7, there are 5 GC types for the Old Generation. 
1. Serial GC
2. Parallel GC
3. Parallel Old GC (Parallel Compacting GC)
4. Concurrent Mark & Sweep GC  (or "CMS")
5. Garbage First (G1) GC


#### Serial GC (-XX:+UseSerialGC)

- The GC in the young generation uses the type we explained in the previous paragraph. The GC in the old generation uses an algorithm called ** "mark-sweep-compact." **
- The first step of this algorithm is to mark the surviving objects in the old generation.
- Then, it checks the heap from the front and leaves only the surviving ones behind (sweep).
- In the last step, it fills up the heap from the front with the objects so that the objects are piled up consecutively, and divides the heap into two parts: one with objects and one without objects (compact).
- The serial GC is suitable for a small memory and a small number of CPU cores.

#### Parallel GC (-XX:+UseParallelGC)
- While the serial GC uses only one thread to process a GC, the parallel GC uses several threads to process a GC, and therefore, faster. - This GC is useful when there is enough memory and a large number of cores. It is also called the "throughput GC."

#### CMS GC (-XX:+UseConcMarkSweepGC)

![Image](https://github.com/avineeth/gyan/blob/master/img/serial-gc-and-cms-gc.png?raw=true)

- As you can see from the picture, the Concurrent Mark-Sweep GC is much more complicated than any other GC types that I have explained so far. 
- The early **initial mark** step is simple. The surviving objects among the objects the closest to the classloader are searched. So, the pausing time is very short.
- In the **concurrent mark** step, the objects referenced by the surviving objects that have just been confirmed are tracked and checked. The difference of this step is that it proceeds while other threads are processed at the same time.
- In the **remark step**, the objects that were newly added or stopped being referenced in the concurrent mark step are checked.
- Lastly, in the **concurrent sweep step**, the garbage collection procedure takes place. The garbage collection is carried out while other threads are still being processed. 
- Since this GC type is performed in this manner, the pausing time for GC is very short. The CMS GC is also called the low latency GC, and is used when the response time from all applications is crucial. 
- While this GC type has the advantage of short stop-the-world time, it also has the following disadvantages.
  - It uses more memory and CPU than other GC types.
  - The compaction step is not provided by default.
- You need to carefully review before using this type. Also, if the compaction task needs to be carried out because of the many memory fragments, the stop-the-world time can be longer than any other GC types. You need to check how often and how long the compaction task is carried out.

#### G1 - Garbage First

-The G1 has a more flexible approach. The heap structured by the G1 looks more like this:

![Image](https://github.com/avineeth/gyan/blob/master/img/GC_Heap_G1.jpg?raw=true)

- As you see, the G1 partitions the heap into smaller, equal sized blocks and assigns them to a generation, instead of reserving one big space for the entire generation. The size of these blocks can be configured and varies between 1 and 32 MB.
- Now, if the Garbage Collector performs its global marking phase (which is of course concurrent), it is able to sort the blocks by the amount of garbage they contain.
- In the next step, the collector selects a number of blocks based on the pause-time-goal specified by the user. These blocks are of course the ones, that contain the most garbage. Because of this behavior, the G1 got its name “Garbage First”.
- In the final step, the collector collects the selected blocks by evacuating their regions. That means, it copies the objects that are still alive from the different blocks together into a new region and removes the old regions entirely. This results in the heap being compacted on the go and avoiding compaction pauses.



# JVM Internals
-	When you write and run a Java program, you are tapping the power of these four technologies.
    * You express the program in source files written in the Java programming language.
    * Compile the source to Java class files, and run the class files on a Java Virtual Machine.
    *	When you write your program, you access system resources (such as I/O, for example) by calling methods in the classes that implement the Java Application Programming Interface, or Java API.
    * As your program runs, it fulfills your program’s Java API calls by invoking methods in class files that implement the Java API. You can see the relationship between these four parts in below figure.
![Image](https://github.com/avineeth/gyan/blob/master/img/jvm-1_1.png?raw=true)

## JVM

-	A Java Virtual Machine’s main job is to load class files and execute the bytecodes they contain.
-	The bytecodes are executed in an execution engine, which is one part of the virtual machine that can vary in different implementations. 
-	The simplest kind of execution engine just interprets the bytecodes one at a time.
-	Another kind of execution engine, one that is faster but requires more memory, is a just-in-time compiler. In this scheme, the bytecodes of a method are compiled to native machine code the first time the method is invoked. The native machine code for the method is then cached, so it can be re-used the next time that same method is invoked.

![Image](https://github.com/avineeth/gyan/blob/master/img/jvm-1_3.png?raw=true)


## Why is Java called Platform Independent?
- Platform Independence is also called build once, run anywhere. 
- Java is one of the most popular platform independent languages. Once we compile a java program and build a jar, we can run the jar (compiled java program) in any Operating System - where a JVM is installed.
- Java achieves Platform Independence in a beautiful way. On compiling a java file the output is a class file which contains an internal java representation called bytecode. JVM converts bytecode to executable instructions. 
- The executable instructions are different in different operating systems. So, there are different JVM's for different operating systems. A JVM for windows is different from a JVM for mac. **JVM is plaform dependent **
- However, both the JVM's understand the bytecode and convert it to the executable code for the respective operating system.


## Is java compiled or interpreted?
- Java uses a two step compilation process. Java source code is compiled down to "bytecode" by the Java compiler. The bytecode is executed by Java Virtual Machine (JVM). 
- The current version of Sun HotSpot JVM uses a technique called Just-in-time (JIT) compilation to compile the bytecode to the native instructions understood by the CPU on the fly at run time.
- Some implementations of JVM might interpret the bytecode instead of JIT compiling it to machine code and running it directly. While this is still considered an "interpreter." It's significantly different from interpreters that read and execute the high level source code (i.e. in this case, Java source code is not interpreted directly, the bytecode, output of Java compiler, is.)
- Sometimes the Java Virtual Machine is called the Java interpreter; however, given the various ways in which bytecodes can be executed, this term can be misleading. While "Java interpreter" is a reasonable name for a Java Virtual Machine that interprets bytecodes, virtual machines also use other techniques (such as just-in-time compiling) to execute bytecodes. Therefore, although all Java interpreters are Java Virtual Machines, not all Java Virtual Machines are Java interpreters.
- To summarize, depending on the execution environment, bytecode can be:
  - compiled ahead of time and executed as native code (similar to C++)
  - compiled just-in-time and executed
  - interpreted
  - directly executed by a supported processor (bytecode is the native instruction set of some CPUs)

## Just In Time (JIT) Compilation
- Java byte code is interpreted however this is not as fast as directly executing native code on the JVM’s host CPU. To improve performance the Oracle Hotspot VM looks for “hot” areas of byte code that are executed regularly and compiles these to native code. The native code is then stored in the code cache in non-heap memory. In this way the Hotspot VM tries to choose the most appropriate way to trade-off the extra time it takes to compile code verses the extra time it take to execute interpreted code.

## What's the difference between the -client and -server systems?
These two systems are different binaries. They are essentially two different compilers (JITs)interfacing to the same runtime system. The client system is optimal for applications which need fast startup times or small footprints, the server system is optimal for applications where the overall performance is most important. In general the client system is better suited for interactive applications such as GUIs. Some of the other differences include the compilation policy,heap defaults, and inlining policy.

### javap - command to decompile bytecode (.class file)
```
C:\Java\Algorithms>javap SayHello.class
Compiled from "SayHello.java"
public class SayHello {
  public SayHello();
  public void doSomething();
}
```

For Disassembling the code use javap -c 

```
C:\Java\Algorithms>javap -c SayHello.class
Compiled from "SayHello.java"
public class SayHello {
  public SayHello();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public void doSomething();
    Code:
       0: getstatic     #2                  // Field java/lang/System.out:Ljava/io/PrintStream;
       3: ldc           #3                  // String Saying Hello
       5: invokevirtual #4                  // Method java/io/PrintStream.println:(Ljava/lang/String;)V
       8: return
}
```

### Difference between 32 bit and 64 bit
- 32 bit/64 bit is the size of the CPU register.
- with 32 bit registers CPU can hold only 2^32 address locations -max 4 GB RAM
- with 64 bit registers CPU can hold 2^64 address locations - 17.2 billion GB RAM
- additionally with 32 bit CPU must process 64 bit numbers are 2 stepp process, where as  64 bit processors are able to perform 64-bit number operations as a single step.

## Class Loader Architecture
- There may be more than one class loader inside a Java Virtual Machine.
- The primordial class loader (there is only one of them) is a part of the Java Virtual Machine implementation.
- also called bootstrap class loader (also known as the system class loader).
- The Java Virtual Machine considers any class it loads through the primordial class loader to be trusted, regardless of whether or not the class is part of the Java API.
- Classes it loads through class loader objects, however, it views with suspicion--by default, it considers them to be untrusted.

![Image](https://github.com/avineeth/gyan/blob/master/img/class-loader.png?raw=true)

- For each class it loads, the Java Virtual Machine keeps track of which class loader--whether primordial or object--loaded the class.
- When a loaded class first refers to another class, the virtual machine requests the referenced class from the same class loader that originally loaded the referencing class.
- For example, if the virtual machine loads class Volcano through a particular class loader, it will attempt to load any classes Volcano refers to through the same class loader. If Volcano refers to a class named Lava, perhaps by invoking a method in class Lava, the virtual machine will request Lava from the class loader object that loaded Volcano. The Lava class returned by the class loader is dynamically linked with class Volcano.
- Because the Java Virtual Machine takes this approach to loading classes, classes can by default only see other classes that were loaded by the same class loader. This is how Java’s architecture enables you to create multiple name-spaces inside a single Java application. Each class loader in your running Java program maintains its own name-space, which is populated by the names of all the classes it has loaded.
- One example of dynamic extension is the web browser, which uses class loader objects to download the class files for an applet across a network. A web browser fires off a Java application that installs a class loader object--usually called an applet class loader--that knows how to request class files from an HTTP server. Applets are an example of dynamic extension, because the Java application doesn’t know when it starts which class files the browser will ask it to download across the network. The class files to download are determined at run-time, as the browser encounters pages that contain Java applets.
- The Java application started by the web browser usually creates a different applet class loader object for each location on the network from which it retrieves class files. As a result, class files from different sources are loaded by different class loader objects. This places them into different name-spaces inside the host Java application. Because the class files for applets from different sources are placed in separate name-spaces, the code of a malicious applet is restricted from interfering directly with class files downloaded from any other source. This puts the class files from different sources into different name-spaces, which allows you to restrict or prevent access between code loaded from different sources.
- HotSpot is an an implementation of the JVM concept, originally developed by Sun and now owned by Oracle. There are other implementations of the JVM specification, like JRockit, IBM J9, among many others.


### Dynamically Load Classes using ClassLoader and Reflection API

- below code shows how a class can be loaded at runtime without actually creating an object of the class using new Operator.

```
public class ClassLoaderExamples {

	public static void main(String[] args) throws Exception{
  
		ClassLoader mainClassLoader = ClassLoaderExamples.class.getClassLoader();
		Class HelloWorldClass = mainClassLoader.loadClass("practice.HelloWorld");
		System.out.println(HelloWorldClass.getName());
		
		Constructor<HelloWorld> constructor = HelloWorldClass.getConstructor();
		Object myObject = constructor.newInstance();
		((HelloWorld)myObject).sayHello();
	}
}
```

## Java Memory Model
[Credits: http://blog.jamesdbloom.com/JVMInternals.html#threads ]

![Image](https://github.com/avineeth/gyan/blob/master/img/JVM_Internal_Architecture.png?raw=true)

- Each Java Virtual Machine has a class loader subsystem: a mechanism for loading types (classes and interfaces) given fully qualified names.
- Each Java Virtual Machine also has an execution engine: a mechanism responsible for executing the instructions contained in the methods of loaded classes.
- When a Java Virtual Machine runs a program, it needs memory to store many things, including bytecodes and other information it extracts from loaded class files, objects the program instantiates, parameters to methods, return values, local variables, and intermediate results of computations. The Java Virtual Machine organizes the memory it needs to execute a program into several runtime data areas.
![Image](https://github.com/avineeth/gyan/blob/master/img/jvm-internal-architechure.gif?raw=true)

### Method Area and Heap
- Each instance of the Java Virtual Machine has one method area and one heap.
- These areas are shared by all threads running inside the virtual machine.
- When the virtual machine loads a class file, it parses information about a type from the binary data contained in the class file. It places this type information into the method area.
- As the program runs, the virtual machine places all objects the program instantiates onto the heap. 
![Image](https://github.com/avineeth/gyan/blob/master/img/methodarea_heap.gif?raw=true)

### Run-Time Constant Pool
- A run-time constant pool is a per-class or per-interface run-time representation of the constant_pool table in a class file (§4.4).
- It contains several kinds of constants, ranging from numeric literals known at compile-time to method and field references that must be resolved at run-time.
- The run-time constant pool serves a function similar to that of a symbol table for a conventional programming language, although it contains a wider range of data than a typical symbol table.

### Stack and Program Counter
- As each new thread comes into existence, it gets its own pc register (program counter) and Java stack.
- If the thread is executing a Java method (not a native method), the value of the pc register indicates the next instruction to execute.
- A threadís Java stack stores the state of Java (not native) method invocations for the thread. The state of a Java method invocation includes its local variables, the parameters with which it was invoked, its return value (if any), and intermediate calculations. 
- The Java stack is composed of stack frames (or frames). A stack frame contains the state of one Java method invocation. When a thread invokes a method, the Java Virtual Machine pushes a new frame onto that threadís Java stack. When the method completes, the virtual machine pops and discards the frame for that method.
- If a thread requires a larger stack than allowed a StackOverflowError is thrown. One of the main reason for this error is incorrect  Recursion programs. If a thread requires a new frame and there isn’t enough memory to allocate it then an OutOfMemoryError is thrown.
- Each frame contains:
  - Local variable array
  - Return value
  - Operand stack
  - Reference to runtime constant pool for class of the current method
- Arrays and objects can never be stored on the stack because a frame is not designed to change in size after it has been created. The frame only stores references that point to objects or arrays on the heap.
![Image](https://github.com/avineeth/gyan/blob/master/img/stack-area.gif?raw=true)

### Data types

- The data types can be divided into a set of primitive types and a reference type. 
- Variables of the primitive types hold primitive values, and variables of the reference type hold reference values.
- Reference values refer to objects, but are not objects themselves.
- Primitive values, by contrast, do not refer to anything. They are the actual data themselves. 
- All the primitive types of the Java programming language, **except boolean** , are primitive types of the Java Virtual Machine. When a compiler translates Java source code into bytecodes, it uses ints or bytes to represent booleans.
- Values of type reference come in three flavors: the class type, the interface type, and the array type. 

![Image](https://github.com/avineeth/gyan/blob/master/img/data-types.gif?raw=true)

    * byte     8-bit signed two's complement integer (-27 to 27 - 1, inclusive)
    * short    16-bit signed two's complement integer (-215 to 215 - 1, inclusive)
    * int      32-bit signed two's complement integer (-231 to 231 - 1, inclusive)
    * long     64-bit signed two's complement integer (-263 to 263 - 1, inclusive)
    * char     16-bit unsigned Unicode character (0 to 216 - 1, inclusive)
    * float    32-bit IEEE 754 single-precision float
    * double   64-bit IEEE 754 double-precision float
    * returnValue address of an opcode within the same method
    * reference reference to an object on the heap, or null



## Thread
- A thread is a thread of execution in a program. 
- The JVM allows an application to have multiple threads of execution running concurrently.
- In the Hotspot JVM there is a direct mapping between a Java Thread and a native operating system Thread.
- After preparing all of the state for a Java thread such as thread-local storage, allocation buffers, synchronization objects, stacks and the program counter, the native thread is created.
- The native thread is reclaimed once the Java thread terminates.
- The operating system is therefore responsible for scheduling all threads and dispatching them to any available CPU.
- Once the native thread has initialized it invokes the run() method in the Java thread. When the run() method returns, uncaught exceptions are handled, then the native thread confirms if the JVM needs to be terminated as a result of the thread terminating (i.e. is it the last non-deamon thread).
- When the thread terminates all resources for both the native and Java thread are released.
- Inside the Java Virtual Machine, threads come in two flavors: daemon and non-daemon.
- A daemon thread is ordinarily a thread used by the virtual machine itself, such as a thread that performs garbage collection.The application, however, can mark any threads it creates as daemon threads. The initial thread of an application--the one that begins at main()--is a non-daemon thread.
- A Java application continues to execute (the virtual machine instance continues to live) as long as any non-daemon threads are still running. When all non-daemon threads of a Java application terminate, the virtual machine instance will exit. If permitted by the security manager, the application can also cause its own demise by invoking the exit() method of class Runtime or System.
- You must in some implementation-dependent way give a Java Virtual Machine the name of the initial class that has the main() method that will start the entire application.

- Different types of threads in HotSpot JVM:
  - VM thread : This thread waits for operations to appear that require the JVM to reach a safe-point. The reason these operations have to happen on a separate thread is because they all require the JVM to be at a safe point where modifications to the heap can not occur. The type of operations performed by this thread are "stop-the-world" garbage collections, thread stack dumps, thread suspension and biased locking revocation.
  - Periodic task thread: This thread is responsible for timer events (i.e. interrupts) that are used to schedule execution of periodic operations
  - GC threads: These threads support the different types of garbage collection activities that occur in the JVM
  - Compiler threads: These threads compile byte code to native code at runtime
  - Signal dispatcher thread: This thread receives signals sent to the JVM process and handle them inside the JVM by calling the appropriate JVM methods.

### The Method Area
- Inside a Java Virtual Machine instance, information about loaded types is stored in a logical area of memory called the method area.
- When the Java Virtual Machine loads a type, it uses a class loader to locate the appropriate class file. 
- The class loader reads in the class file--a linear stream of binary data--and passes it to the virtual machine.
- The virtual machine extracts information about the type from the binary data and stores the information in the method area.
- Memory for class (static) variables declared in the class is also taken from the method area. (Static variables are Methods are stored in Permanent Generation area).
- All threads share the same method area, so access to the method area's data structures must be designed to be thread-safe. If two threads are attempting to find a class named Lava, for example, and Lava has not yet been loaded, only one thread should be allowed to load it while the other one waits.

- The method area stores per-class information such as:
  - Classloader Reference - The virtual machine uses this information during dynamic linking. When one type refers to another type, the virtual machine requests the referenced type from the same class loader that loaded the referencing type.
  - Run Time Constant Pool
  - Field data  - Field Name, Type, Modifiers, Attributes
  - Method data - Method Name, return type, parameters, Modifiers, Attributes
  - Method code

### Constant Pool
- Constants such as literal strings, final variable values
- fully qualified names of classes and interfaces 
- field names and descriptors 
- method names and descriptors 

### Dynamic Linking
- The constant pool plays an important role in the dynamic linking of Java programs.
- The constant pool's fully qualified names and method and field descriptors are used at run time to link code in this class or interface with code and data in other classes and interfaces.
- The class file contains no information about the eventual memory layout of its components, so classes, fields, and methods cannot be referenced directly by the bytecodes in the class file.
- The Java Virtual Machine resolves the actual address of any referenced item at run time given a symbolic reference from the constant pool.
- For example, bytecode instructions that invoke a method give constant pool index of a symbolic reference to the method to invoke.
- A symbolic reference is a logical reference not a reference that actually points to a physical memory location. The JVM implementation can choose when to resolve symbolic references, this can happen when the class file is verified, after being loaded, called eager or static resolution, instead this can happen when the symbolic reference is used for the first time called lazy or late resolution. 
```
class Lava {
    private int speed = 5; // 5 kilometers per hour
    void flow() {
    }
}

class Volcano {
    public static void main(String[] args) {
        Lava lava = new Lava();
        lava.flow();
    }
}
```
1. To run the Volcano application, you give the name "Volcano" to a Java Virtual Machine - "java Volcano"
2. It extracts the definition of class Volcano from the binary data in the imported class file and places the information into the method area.
3. The virtual machine then invokes the main() method, by interpreting the bytecodes stored in the method area. 
4. As the virtual machine executes main(), it maintains a pointer to the constant pool (a data structure in the method area) for the current class (class Volcano).
5. Note that this Java Virtual Machine has already begun to execute the bytecodes for main() in class Volcano even though it hasn't yet loaded class Lava. It loads classes only as it needs them.
6. The virtual machine uses its pointer into Volcano's constant pool to look up entry one and finds a symbolic reference to class Lava. It checks the method area to see if Lava has already been loaded.
7. When the virtual machine discovers that it hasnít yet loaded a class named "Lava," it proceeds to find and read in file Lava.class. It extracts the definition of class Lava from the imported binary data and places the information into the method area.
8. The Java Virtual Machine then replaces the symbolic reference in Volcano's constant pool entry one, which is just the string "Lava", with a pointer to the class data for Lava. This process of replacing symbolic references with direct references (in this case, a native pointer) is called constant pool resolution. The symbolic reference is resolved into a direct reference by searching through the method area until the referenced entity is found, loading new classes if necessary.
9. Finally, the virtual machine is ready to actually allocate memory for a new Lava object.
10.  Initializes the instance variable speed.
11. The first instruction of main() completes by pushing a reference to the new Lava object onto the stack. 
12. A later instruction will use the reference to invoke Java code that initializes the speed variable to its proper initial value, five.
13. Another instruction will use the reference to invoke the flow() method on the referenced Lava object.

### Memory Leak

- A memory leak in Java can occur if you forget to close a resource, or a reference to an object is not released. e.g.
  - File/Text buffers not closed.
  - Hash maps keeping references alive if equals() and hashcode() are not implemented, e.g.

- Of course, there are a number of ways to create memory leaks in Java. For simplicity we will define a class to be a key in a HashMap, but we will not define the equals() and hashcode() methods.
- A HashMap is a hash table implementation for the Map interface, and as such it defines the basic concepts of key and value: each value is related to a unique key, so if the key for a given key-value pair is already present in the HashMap, its current value is replaced.
- It’s mandatory that our key class provides a correct implementation of the equals() and hashcode() methods. Without them, there is no guarantee that a good key will be generated.
- By not defining the equals() and hashcode() methods, we add the same key to the HashMap over and over and, instead of replacing the key as it should, the HashMap grows continuously, failing to identify these identical keys and throwing an OutOfMemoryError.

```
package com.post.memory.leak;
import java.util.Map;

public class MemLeak {
    public final String key;
    
    public MemLeak(String key) {
        this.key =key;
    }
    
    public static void main(String args[]) {
        try {
            Map map = System.getProperties();
            
            for(;;) {
                map.put(new MemLeak("key"), "value");
            }
        } catch(Exception e) {
            e.printStackTrace();
        }
    }
}
```
- Note: the memory leak is not due to the infinite loop on line 14: the infinite loop can lead to a resource exhaustion, but not a memory leak. If we had properly implemented equals() and hashcode() methods, the code would run fine even with the infinite loop as we would only have one element inside the HashMap.

