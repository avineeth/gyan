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
