
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


### The Observer Pattern
- The Observer Pattern defines a one-to-many dependency between objects so that when one object changes state, all of its dependents are notified and updated automatically.
