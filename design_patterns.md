
### Polymorphism 

Polymorphism is the ability of an object to take on many forms. The most common use of polymorphism in OOP occurs when a parent class reference is used to refer to a child class object.

```
public class Animal {
  public String shout() {
    return "Don't Know!";
  }
}

class Dog extends Animal {
  public String shout() {
    return "BOW BOW";
  }
}

Animal animal2 = new Dog();

```

### The Strategy Pattern
- The Strategy Pattern defines a family of algorithms, encapsulates each one, and makes them interchangeable.
- Strategy lets the algorithm vary independently from clients that use it.
- In Strategy pattern, a class behavior or its algorithm can be changed at run time. This type of design pattern comes under behavior pattern.
- In Strategy pattern, we create objects which represent various strategies and a context object whose behavior varies as per its strategy object. The strategy object changes the executing algorithm of the context object.
- Duck example

### The Observer Pattern
- The Observer Pattern defines a one-to-many dependency between objects so that when one object changes state, all of its dependents are notified and updated automatically.
- Weather Station

### Decorator Pattern
- Decorator pattern allows a user to add new functionality to an existing object without altering its structure. This type of design pattern comes under structural pattern as this pattern acts as a wrapper to existing class.
- This pattern creates a decorator class which wraps the original class and provides additional functionality keeping class methods signature intact.
- Starbuck example
- new BufferedReader( new FileInputStream());


### Simple Factory
- All factory patterns encapsulate object creation. The Factory Method Pattern encapsulates object creation by letting subclasses decide what objects to create.

### Factory Method
- The Factory Method Pattern defi nes an interface for creating an object, but lets subclasses decide which class to instantiate. Factory Method lets a class defer instantiation to subclasses.

### Abstract Factory
- The Abstract Factory Pattern provides an interface for creating families of related or dependent objects without specifying their concrete classes.

### Singleton Pattern
- The Singleton Pattern ensures a class has only one instance, and provides a global point of access to it.

#### Synchronize the getInstance() method:
- A straightforward technique that is guaranteed to work.
- this has performance overhead because of the synchronized method

```
public class Singleton {
  private static Singleton uniqueInstance;
  // other useful instance variables here

  private Singleton() {}
  
  public static synchronized Singleton getInstance() {
    if (uniqueInstance == null) {
    uniqueInstance = new Singleton();
    }
    return uniqueInstance;
  }
  
  // other useful methods here
}
```

#### Use eager instantiation:

- not useful if the object created is an expensive one. Since the object is statically initialized it is a waste of resources in case the it is not used at all during the lifecycle of the application.

```
public class Singleton {
  private static Singleton uniqueInstance = new Singleton();

  private Singleton() {}
  
  public static Singleton getInstance() {
    return uniqueInstance;
  }
}
```

#### Initialization-on-demand holder idiom 

- In software engineering, the initialization-on-demand holder (design pattern) idiom is a **lazy-loaded singleton**.
- In all versions of Java, the idiom enables a safe, highly concurrent lazy initialization with good performance.

```
public class Something {
    private Something() {}

    private static class LazyHolder {
        static final Something INSTANCE = new Something();
    }

    public static Something getInstance() {
        return LazyHolder.INSTANCE;
    }
}
```
- The implementation of the idiom relies on the initialization phase of execution within the Java Virtual Machine (JVM) as specified by the Java Language Specification (JLS).[3] 
- When the class Something is loaded by the JVM, the class goes through initialization. Since the class does not have any static variables to initialize, the initialization completes trivially. 
- The static class definition LazyHolder within it is not initialized until the JVM determines that LazyHolder must be executed. The static class LazyHolder is only executed when the static method getInstance is invoked on the class Something, and the first time this happens the JVM will load and initialize the LazyHolder class. 
- The initialization of the LazyHolder class results in static variable INSTANCE being initialized by executing the (private) constructor for the outer class Something. 
- Since the **class initialization phase is guaranteed by the JLS to be serial, i.e., non-concurrent**, no further synchronization is required in the static getInstance method during loading and initialization. 
- And since the initialization phase writes the static variable INSTANCE in a serial operation, all subsequent concurrent invocations of the getInstance will return the same correctly initialized INSTANCE without incurring any additional synchronization overhead.

- While the implementation is an efficient thread-safe "singleton" cache without synchronization overhead, and better performing than uncontended synchronization,[4] the idiom can only be used when the construction of Something can be guaranteed to not fail. In most JVM implementations, if construction of Something fails, subsequent attempts to initialize it from the same class-loader will result in a NoClassDefFoundError failure.


