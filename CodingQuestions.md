
### What is the output for the following program


```
public class JavaHungry {
    public static void doSomething(Object s) {
        System.out.println("Object :" + s);
    }

    public static void doSomething(String s) {
        System.out.println("String :" + s);
    }

    public static void main(String[] args) {
        doSomething(null);
    }
}
```
Ans: String :null

### What is the output for the following program

```
public class JavaHungry {
    public static void doSomething(Object s) {
        System.out.println("Object :" + s);
    }

    public static void doSomething(String s) {
        System.out.println("String :" + s);
    }

    public static void doSomething(Integer s) {
        System.out.println("Integer :" + s);
    }

    public static void main(String[] args) {
        doSomething(null);
    }
}
```
Ans: Does not compile. "Ambiguous method call"

