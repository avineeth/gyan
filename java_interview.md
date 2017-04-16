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

# Java Basics

## Strings
### String Constant Pool/String Interning
To make Java more memory efficient, the JVM sets aside a special area of memory called the "String constant pool." When the compiler encounters a String literal, it checks the pool to see if an identical String already exists. If a match is found, the reference to the new literal is directed to the existing String, and no new String literal object is created.

### String intern()
- A pool of strings, initially empty, is maintained privately by the class String.
- When the intern method is invoked, if the pool already contains a string equal to this String object as determined by the equals(Object) method, then the string from the pool is returned.
- Otherwise, this String object is added to the pool and a reference to this String object is returned.
```
    String s1 = "hello";
    String s2 = "hello";
    String s3 = new String("hello");
    String s4 = new String("hello").intern();
    String s5 = "hello".intern();

    if(s1 == s2) //true
       System.out.println("s1 and s2 are same");
    if(s1 == s3) //false
       System.out.println("s1 and s3 are same");
    if(s1 == s4) //true
       System.out.println("s1 and s4 are same");
    if(s1 == s5) //true
       System.out.println("s1 and s5 are same");
```
### What is the difference between? 

    String s = "hello";
    String s = new String("hello");

- When you use a literal, say String str = "abc";, the object in the pool is used.
- If you use String str = new String("abc");, a new object is created, but the existing string literal may be reused on either the JVM level or bytecode level (at compile time).

### String, StringBuffer, and StringBuilder
- Mutability Difference: String is immutable, if you try to alter their values, another object gets created, whereas StringBuffer and StringBuilder are mutable so they can change their values.
- Thread-Safety Difference: The difference between StringBuffer and StringBuilder is that StringBuffer is thread-safe. So when the application needs to be run only in a single thread then it is better to use StringBuilder. StringBuilder is more efficient than StringBuffer.
- If your string is not going to change use a String class because a String object is immutable.
- If your string can change (example: lots of logic and operations in the construction of the string) and will only be accessed from a single thread, using a StringBuilder is good enough.
- If your string can change, and will be accessed from multiple threads, use a StringBuffer because StringBuffer is synchronous so you have thread-safety.
- Also note that StringBuilder/Buffers aren't magic, they just use an Array as a backing object and that Array has to be re-allocated/re-sized when ever it gets full. 
- All three classes are final. 

### What is the main difference between Java strings and C, C++ strings?
- In C and C++, strings are terminated with null character. But in java, strings are not terminated with null character. Strings are treated as objects in java.

## Can you mark a class as both abstract and final?
- You can't mark a class as both abstract and final. They have nearly opposite meanings. An abstract class must be subclassed, whereas a final class must not be subclassed. 
- If you see this combination of abstract and final modifiers, used for a class or method declaration, the code will not compile.
- If even a single method is abstract, the whole class must be declared abstract.
- You can, however, put nonabstract methods in an abstract class.

## Difference between interface and abstract class?
- Think of an interface as a 100-percent abstract class.
- But while an abstract class can define both abstract and non-abstract methods, an interface can have only abstract methods.
- All interface methods are implicitly public and abstract. In other words,you do not need to actually type the public or abstract modifiers in the method declaration, but the method is still always public and abstract.
- All variables defined in an interface must be public, static, and final—in other words, interfaces can declare only constants, not instance variables.
- Interface methods must not be static.
- Because interface methods are abstract, they cannot be marked final,strictfp, or native. (More on these modifiers later.)
- An interface can extend one or more other interfaces.
- An interface cannot extend anything but another interface.
- An interface cannot implement another interface or class.
- An interface must be declared with the keyword interface.
- Interface types can be used polymorphically


## Object’s equal() method and when do you override it?
- If you don't override a class's equals() method, you won't be able to use those objects as a key in a hashtable and you probably won't get accurate Sets, such that there are no conceptual duplicates.
- boolean equals (Object obj) Decides whether two objects are meaningfully equivalent.
- int hashCode() Returns a hashcode int value for an object, so that the object can be used in Collection classes that use hashing, including Hashtable,HashMap, and HashSet.

## final
- final can be used to mark a variable "unchangeable"

`private final String name = "foo";  //the reference name can never change`
- final can also make a method not "overrideable"

`public final String toString() {  return "NULL"; }`
- final can also make a class not "inheritable". i.e. the class can not be subclasses.

`public final class finalClass {...}`

`public class classNotAllowed extends finalClass {...} // Not allowed`



## finally
- finally is used in a try/catch statement to execute code "always"
- Java 7 has a new try with resources statement that you can use to automatically close resources that explicitly or implicitly implement java.io.Closeable or java.lang.AutoCloseable

```
lock.lock();
try {
  //do stuff
} catch (SomeException se) {
  //handle se
} finally {
  lock.unlock(); //always executed, even if Exception or Error or se
}
```

https://docs.oracle.com/javase/tutorial/essential/exceptions/tryResourceClose.html

## finalize
- finalize is called when an object is garbage collected. You rarely need to override it. An example:
```
public void finalize() {
  //free resources (e.g. unallocate memory)
  super.finalize();
}
```

## What Is the Static Keyword in Java?

- In Java, a static member is a member of a class that isn’t associated with an instance of a class. Instead, the member belongs to the class itself. As a result, you can access the static member without first creating a class instance.
- The two types of static members are static fields and static methods:
- Static field: A field that’s declared with the static keyword, like this:
    `private static int ballCount;`    
  - The value of a static field is the same across all instances of the class. In other words, if a class has a static field named CompanyName, all objects created from the class will have the same value for CompanyName.
  - Static fields are created and initialized when the class is first loaded. That happens when a static member of the class is referred to or when an instance of the class is created, whichever comes first.
- Static method: A method declared with the static keyword. Like static fields, static methods are associated with the class itself, not with any particular object created from the class. As a result, you don’t have to create an object from a class before you can use static methods defined by the class.
  - The best-known static method is main, which is called by the Java runtime to start an application. The main method must be static, which means that applications run in a static context by default.
  - One of the basic rules of working with static methods is that you can’t access a nonstatic method or field from a static method because the static method doesn’t have an instance of the class to use to reference instance methods or fields.

## Transient Variables
- If you mark an instance variable as transient, you're telling the JVM to skip(ignore) this variable when you attempt to serialize the object containing it.
- Serialization is one of the coolest features of Java; it lets you save (sometimes called "flatten") an object by writing its state (in other words, the value of its instance variables) to a special type of I/O stream.
- With serialization you can save an object to a file, or even ship it over a wire for reinflating (deserializing) at the other end, in another JVM. 

## Volatile Variables
- The volatile modifier tells the JVM that a thread accessing the variable must always reconcile its own private copy of the variable with the master copy in memory. 
- All you need to know about volatile is that, as with transient, it can be applied only to instance variables.
- Make no mistake, the idea of multiple threads accessing an instance variable is scarystuff, and very important for any Java programmer to understand. But you'll probably use synchronization, rather than the volatile modifier, to make your data thread-safe.

## Serialization
- Serialization is simply turning an existing object into a byte array. This byte array represents the class of the object, the version of the object, and the internal state of the object. This byte array can then be used between JVM's running the same code to transmit/read the object.
- Why would we want to do this?
- There are several reasons:
  - Communication: If you have two machines that are running the same code, and they need to communicate, an easy way is for one machine to build an object with information that it would like to transmit, and then serialize that object to the other machine. It's not the best method for communication, but it gets the job done.
  - Persistence: If you want to store the state of a particular operation in a database, it can be easily serialized to a byte array, and stored in the database for later retrieval.
  - Deep Copy: If you need an exact replica of an Object, and don't want to go to the trouble of writing your own specialized clone() class, simply serializing the object to a byte array, and then de-serializing it to another object achieves this goal.
  - Caching: Really just an application of the above, but sometimes an object takes 10 minutes to build, but would only take 10 seconds to de-serialize. So, rather than hold onto the giant object in memory, just cache it out to a file via serialization, and read it in later when it's needed.
  - Cross JVM Synchronization: Serialization works across different JVMs that may be running on different architectures.

## Immutability

- Immutable classes are those class, whose object can not be modified once created, it means any modification on immutable object will result in another immutable object. 
- Best example to understand immutable and mutable objects are, String and StringBuffer. Since String is immutable class, any change on existing string object will result in another string e.g. replacing a character into String, creating substring from String, all result in a new objects. While in case of mutable object like StringBuffer, any modification is done on object itself and no new objects are created. 
- Some times this immutability of String can also cause security hole, and that the reason why password should be stored on char array instead of String.
- To Create Immutable Objects
  - Don't provide "setter" methods — methods that modify fields or objects referred to by fields.
  - Make all fields final and private.
  - Don't allow subclasses to override methods. The simplest way to do this is to declare the class as final. A more sophisticated approach is to make the constructor private and construct instances in factory methods. (Builder Design Pattern).


public final class Contacts {

    private final String name;
    private final String mobile;

    public Contacts(String name, String mobile) {
        this.name = name;
        this.mobile = mobile;
    }
  
    public String getName(){
        return name;
    }
  
    public String getMobile(){
        return mobile;
    }
} 

# Collections

The core collection interface:

![Image](https://github.com/avineeth/gyan/blob/master/img/collections.gif?raw=true)


## Traversing Collections
There are three ways to traverse collections:
- using aggregate operations ( TODO: JDK 1.8 lambda expressions)
- with the for-each construct 
`for(String t : names)
 System.out.println("ForEach = [" + t + "]");`

- by using Iterators. 
   Note that Iterator. remove is the only safe way to modify a collection during iteration; the behavior is unspecified if the underlying collection is modified in any other way while the iteration is in progress.

`Iterator<String> it = names.iterator();
while(it.hasNext()) {
   System.out.println("Iterator = [" + it.next() + "]");
   it.remove();
}
System.out.println("has elements = [" + it.hasNext() + "]");
`

