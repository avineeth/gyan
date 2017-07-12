
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
## Creational Design Patterns 
- These design patterns provide a way to create objects while hiding the creation logic, rather than instantiating objects directly using new opreator.

##### Singleton
- Ensure a class only has one instance, and provide a global point of access to it. 

##### Simple Factory
- All factory patterns encapsulate object creation. The Factory Method Pattern encapsulates object creation by letting subclasses decide what objects to create.

##### Factory Method
- The Factory Method Pattern defi nes an interface for creating an object, but lets subclasses decide which class to instantiate. Factory Method lets a class defer instantiation to subclasses.

##### Abstract Factory
- The Abstract Factory Pattern provides an interface for creating families of related or dependent objects without specifying their concrete classes.

## Structural Design Patterns
- These design patterns concern class and object composition. Concept of inheritance is used to compose interfaces and define ways to compose objects to obtain new functionalities.

## Behavioral Design Patterns
- These design patterns are specifically concerned with communication between objects.


### The Strategy Pattern
- The Strategy Pattern defines a family of algorithms, encapsulates each one, and makes them interchangeable.
- The Strategy pattern is used in situations where you know that you want to swap out implementations. As an example, you might want to format data in different ways - you could use the strategy pattern to swap out an XML formatter or CSV formatter, etc.
- In Strategy pattern, a class behavior or its algorithm can be changed at run time. This type of design pattern comes under behavior pattern.
- In Strategy pattern, we create objects which represent various strategies and a context object whose behavior varies as per its strategy object. The strategy object changes the executing algorithm of the context object.
- Duck example
- Similar to Dependency Injection.

### The Observer Pattern
- The Observer Pattern defines a one-to-many dependency between objects so that when one object changes state, all of its dependents are notified and updated automatically.
- Weather Station

### Decorator Pattern
- Decorator pattern allows a user to add new functionality to an existing object without altering its structure. This type of design pattern comes under structural pattern as this pattern acts as a wrapper to existing class.
- This pattern creates a decorator class which wraps the original class and provides additional functionality keeping class methods signature intact.
- Starbuck example
- new BufferedReader( new FileInputStream());


### Singleton Pattern
- The Singleton Pattern ensures a class has only one instance, and provides a global point of access to it.

#### Straightforward way of creating Singleton Class. (Non-threadsafe.)
- Private static instance variable. (static because getInstance() should be static).
- private constructor.
- public getInstance method which lazyily creates instance.

```
public class SingletonExample {

    private static SingletonExample singleton;

    private SingletonExample() {}

    public static SingletonExample getInstance() {
        if(singleton == null) {
            return new SingletonExample();
        }
        else
            return singleton;
    }

}
```

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

#### Double check locking
- Check that the variable is initialized (without obtaining the lock). If it is initialized, return it immediately.
- Obtain the lock.
- Double-check whether the variable has already been initialized: if another thread acquired the lock first, it may have already done the initialization. If so, return the initialized variable.
- Otherwise, initialize and return the variable.
```
public class SingletonExample {

    private static SingletonExample singleton;

    private SingletonExample() {}

    public static SingletonExample getInstance() {
        if(singleton == null) {
            synchronized (SingletonExample.class) {
                if(singleton == null)  //Double check locking
                    return new SingletonExample();
            }
        }

        return singleton;
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

## Proxy Design Pattern

- Proxy design pattern intent according to GoF is: 
**Provide a surrogate or placeholder for another object to control access to it. **
- proxy design pattern is used when we want to provide controlled access of a functionality.
- Let’s say we have a class that can run some command on the system. Now if we are using it, its fine but if we want to give this program to a client application, it can have severe issues because client program can issue command to delete some system files or change some settings that you don’t want.
- Proxy design pattern common uses are to control access or to provide a wrapper implementation for better performance.
- Java RMI package uses proxy pattern. 
- The following Java example illustrates the "virtual proxy" pattern. The ProxyImage class is used to access a remote method.
- The example creates first an interface against which the pattern creates the classes. This interface contains only one method to display the image, called displayImage(), that has to be coded by all classes implementing it.
- The proxy class ProxyImage is running on another system than the real image class itself and can represent the real image RealImage over there. The image information is accessed from the disk. Using the proxy pattern, the code of the ProxyImage avoids multiple loading of the image, accessing it from the other system in a memory-saving manner. It should be noted, however, that the lazy loading demonstrated in this example is not part of the proxy pattern, but is merely an advantage made possible by the use of the proxy.

![Image](https://github.com/avineeth/gyan/blob/master/img/400px-Proxy_pattern_diagram.svg.png?raw=true)
![Image](https://github.com/avineeth/gyan/blob/master/img/proxydesignpattern.PNG?raw=true)

```
interface Image {
    public void displayImage();
}

```

```
// On System A
class RealImage implements Image {

    private String filename = null;
    /**
     * Constructor
     * @param filename
     */
    public RealImage(final String filename) {
        this.filename = filename;
        loadImageFromDisk();
    }

    /**
     * Loads the image from the disk
     */
    private void loadImageFromDisk() {
        System.out.println("Loading   " + filename);
    }

    /**
     * Displays the image
     */
    public void displayImage() {
        System.out.println("Displaying " + filename);
    }

}
```
```
// On System B
class ProxyImage implements Image {

    private RealImage image = null;
    private String filename = null;
    /**
     * Constructor
     * @param filename
     */
    public ProxyImage(final String filename) {
        this.filename = filename;
    }

    /**
     * Displays the image
     */
    public void displayImage() {
        if (image == null) {
           image = new RealImage(filename);
        }
        image.displayImage();
    }

}
```
```
class ProxyExample {

   /**
    * Test method
    */
   public static void main(final String[] arguments) {
        final Image image1 = new ProxyImage("HiRes_10MB_Photo1");
        final Image image2 = new ProxyImage("HiRes_10MB_Photo2");

        image1.displayImage(); // loading necessary
        image1.displayImage(); // loading unnecessary
        image2.displayImage(); // loading necessary
        image2.displayImage(); // loading unnecessary
        image1.displayImage(); // loading unnecessary
    }
}
```
The program's output is:

Loading   HiRes_10MB_Photo1
Displaying HiRes_10MB_Photo1
Displaying HiRes_10MB_Photo1
Loading   HiRes_10MB_Photo2
Displaying HiRes_10MB_Photo2
Displaying HiRes_10MB_Photo2
Displaying HiRes_10MB_Photo1



## J2EE Design Patterns

#### Front Controller pattern
DispatcherServlet in Spring

#### View Helper
ViewResolver in Spring

#### Dependency Injection

#### Data Access Object(DAO) pattern
It provides a flexible and transparent access to the data, abstracts the data sources and hides the complexity of Data persistence layer. This pattern provides for loose coupling between business and data persistence layer.

#### Intercepting Filter pattern
Provides a solution for pre-processing and post-processing a request. It allows us to declaratively apply filters for intercepting requests and responses. For ex. Servlet filters.

#### Session Façade pattern
This pattern hides the complexity of business components and centralizes the workflow. It provides course-grained interfaces to the clients which reduces the remote method overhead. This pattern fits well with declarative transactions and security management.




