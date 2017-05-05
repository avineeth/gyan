
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

