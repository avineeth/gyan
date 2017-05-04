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
```
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
-         return mobile;
    }
   }
   ```

### Floating Point Numbers

The Java programming language provides two built-in classes for representing floating-point numbers: float, and double. The "float" class takes 4 bytes of storage, and have 23 binary digits of precision. The "double" class takes 8 bytes of storage, and have 52 binary digits of precision.

3.0d is a double precision floating-point number constant. 3.0, or alternatively 3.0f is a single precision floating-point number constant.

float f = 3.0f;
double d = 3.0d;
The Java core library also provides two wrapped classes java.lang.Float, and java.lang.Double. These two classes allow floating-point objects to be stored in Java collection objects, such as hash tables. These two classes also provides parsing, and conversion helper methods.

Float ff = new Float(1.0f); // creates a Java "Float" object
Double dd = new Double(2.0d); // creates a Java "Double" object

#### Not A Number

"NaN" stands for "not a number". "Nan" is produced if a floating point operation has some input parameters that cause the operation to produce some undefined result. For example, 0.0 divided by 0.0 is arithmetically undefined. Taking the square root of a negative number is also undefined.

0.0 / 0.0   ->  NaN
Math.sqrt(-2.0)  ->  NaN

All boolean operations involving "NaN" results in a false value.

Double.NaN > 1.0  ->  false
Double.NaN < 1.0  ->  false

A "NaN" value is not equal to itself. However, a "NaN" Java "Float" object is equal to itself. The semantic is defined this way, because otherwise "NaN" Java "Float" objects cannot be retrieved from a hash table.

(new Float(0.0 / 0.0)).equals(new Float(0.0 / 0.0))  ->  true

#### Infinity

"Infinity" is produced if a floating point operation creates such a large floating-point number that it cannot be represented normally. "Infinity" is a special value that represent the concept of positive infinity.

1.0 / 0.0  ->  Infinity

"-Infinity" is a special value that represent the concept of negative infinity.

-1.0 / 0.0  ->  -Infinity

Negative Zero "-0.0" is numerically identical to "0.0". However, some operations involving "-0.0" are different than the same operation with "0.0".

(-0.0) == 0.0  ->  true
new Double(0.0).equals(new Double(-0.0)) -> false
  
  
### What is Anonymous Inner Class?

- Anonymous Inner Class is used often as single-use classes for convenience, that help make your code more concise. 
- An anonymous inner class can come useful when making an instance of an object with certain "extras" such as overloading methods, without having to actually subclass a class.
- Example attaching an event listener:
```
button.addActionListener(new ActionListener() {
    @Override
    public void actionPerformed(ActionEvent e) {
        // do something
    }
});
```
- Using this method makes coding a little bit quicker, as I don't need to make an extra class that implements ActionListener. I can just instantiate an anonymous inner class without actually making a separate class.
- I only use this technique for "quick and dirty" tasks where making an entire class feels unnecessary. Having multiple anonymous inner classes that do exactly the same thing should be refactored to an actual class, be it an inner class or a separate class.


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

