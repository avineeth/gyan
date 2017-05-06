TODO:
- Garbage Collection
- Thread Dumps
- GC Pauses

http://javarevisited.blogspot.in/2011/04/garbage-collection-in-java.html

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

### Difference between 32 bit and 64 bit
- 32 bit/64 bit is the size of the CPU register.
- with 32 bit registers CPU can hold only 2^32 address locations -max 4 GB RAM
- with 64 bit registers CPU can hold 2^64 address locations - 17.2 billion GB RAM
- additionally with 32 bit CPU must process 64 bit numbers are 2 stepp process, where as  64 bit processors are able to perform 64-bit number operations as a single step.

## Class Loader Architecture
- There may be more than one class loader inside a Java Virtual Machine.
- The primordial class loader (there is only one of them) is a part of the Java Virtual Machine implementation.
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

## Java Memory Model
[Credits: http://blog.jamesdbloom.com/JVMInternals.html#threads ]

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

