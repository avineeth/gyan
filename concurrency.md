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
