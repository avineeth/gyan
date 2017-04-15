# JVM Internals
-	When you write and run a Java program, you are tapping the power of these four technologies.
  - You express the program in source files written in the Java programming language.
  - Compile the source to Java class files, and run the class files on a Java Virtual Machine.
  -	When you write your program, you access system resources (such as I/O, for example) by calling methods in the classes that implement the Java Application Programming Interface, or Java API.
  - As your program runs, it fulfills your program’s Java API calls by invoking methods in class files that implement the Java API. You can see the relationship between these four parts in below figure.
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

Thread
- A thread is a thread of execution in a program. 
- The JVM allows an application to have multiple threads of execution running concurrently.
- In the Hotspot JVM there is a direct mapping between a Java Thread and a native operating system Thread.
- After preparing all of the state for a Java thread such as thread-local storage, allocation buffers, synchronization objects, stacks and the program counter, the native thread is created.
- The native thread is reclaimed once the Java thread terminates.
- The operating system is therefore responsible for scheduling all threads and dispatching them to any available CPU.
- Once the native thread has initialized it invokes the run() method in the Java thread. When the run() method returns, uncaught exceptions are handled, then the native thread confirms if the JVM needs to be terminated as a result of the thread terminating (i.e. is it the last non-deamon thread).
- When the thread terminates all resources for both the native and Java thread are released.
- If a thread requires a larger stack than allowed a StackOverflowError is thrown. One of the main reason for this error is incorrect  Recursion programs.


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

`lock.lock();
try {
  //do stuff
} catch (SomeException se) {
  //handle se
} finally {
  lock.unlock(); //always executed, even if Exception or Error or se
}`

https://docs.oracle.com/javase/tutorial/essential/exceptions/tryResourceClose.html

## finalize
- finalize is called when an object is garbage collected. You rarely need to override it. An example:
`public void finalize() {
  //free resources (e.g. unallocate memory)
  super.finalize();
}`


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

