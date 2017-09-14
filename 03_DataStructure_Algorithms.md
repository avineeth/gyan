# Collections

The core collection interface:

![Image](https://github.com/avineeth/gyan/blob/master/img/CollectionHierarchy.png?raw=true)

```
public interface Collection extends Iterable {
//method definitions
}
```

#### Why Map interface does not extend Collection interface?
- A good answer to this interview question is “because they are incompatible“.
- Collection has a method add(Object o). Map can not have such method because it need key-value pair.
- There are other reasons also such as Map supports keySet, valueSet etc. Collection classes does not have such views.
- Due to such big differences, Collection interface was not used in Map interface, and it was build in separate hierarchy.

### List Interface
- A java list is a “ordered” collection of elements. 
- This ordering is a zero based index.
- It does not care about duplicates.
- Apart from methods defined in Collection interface, it does have its own methods also which are largely to manipulate the collection based on index location of element. These methods can be grouped as search, get, iteration and range view. All above operations support index locations.
- The main classes implementing List interface are: Stack, Vector, ArrayList and LinkedList. 

### Set Interface
- It models the mathematical set in set theory. Set interface is like List interface but with some differences. 
- First, it is not ordered collection. **So no ordering is preserved while adding or removing elements.**
- The main feature it does provide is **uniqueness of elements**. It does not support duplicate elements.
- Set also adds a stronger contract on the behavior of the equals and hashCode operations, allowing Set instances to be compared meaningfully even if their implementation types differ. Two Set instances are equal if they contain the same elements.
- Based on above reasons, it does not have operations based on indexes of elements like List. It only has methods which are inherited by Collection interface.
- Main classes implementing Set interface are : EnumSet, HashSet, LinkedHashSet, TreeSet. Read more on related java documentation

#### How HashSet store elements?
- HashSet uses HashMap internally to store values.
- You must know that HashMap store key-value pairs, with one condition i.e. keys will be unique.
- HashSet uses Map’s this feature to ensure uniqueness of elements. In HashSet class, a map declaration is as below:
```
private transient HashMap<E,Object> map;
 
//This is added as value for each key
private static final Object PRESENT = new Object();
```
- So when you store a element in HashSet, it stores the element as key in map and “PRESENT” object as value. (See declaration above).
```
public boolean add(E e) {
return map.put(e, PRESENT)==null;
}
```

#### Can a null element added to a TreeSet or HashSet?
- As you see, There is no null check in add() method in previous question. And HashMap also allows one null key, so one “null” is allowed in HashSet.


#### WeakHashMap 
WeakHashMap is an implementation of the Map interface that stores only weak references to its keys. Storing only weak references allows a key-value pair to be garbage collected when its key is no longer referenced outside of the WeakHashMap. This class is intended primarily for use with key objects whose equals methods test for object identity using the == operator. Once such a key is discarded it can never be recreated, so it is impossible to do a look-up of that key in a WeakHashMap at some later time and be surprised that its entry has been removed.

#### TreeMap?

- TreeMap is special form of HashMap. It maintains the ordering of keys which is missing in HashMap class. This ordering is by default “natural ordering”. The default ordering can be override by providing an instance of Comparator class, whose compare method will be used to maintain ordering of keys.
- Please note that all keys inserted into the map must implement the Comparable interface (this is necessary to decide the ordering). 
- TreeMap is a Red-Black tree based NavigableMap implementation.In other words , it sorts the TreeMap object keys using Red-Black tree algorithm.
- Complexity - TreeMap : log(n)   HashMap : Constant time performance assuming elements disperses properly

#### Difference between Set and List?
1. Set is unordered collection where List is ordered collection based on zero based index.
2. List allow duplicate elements but Set does not allow duplicates.
3. List does not prevent inserting null elements (as many you like), but Set will allow only one null element.

#### Difference between List and Map?

1. Perhaps most easy question. List is collection of elements where as map is collection of key-value pairs.
2. There is actually lots of differences which originate from first statement. They have separate top level interface, separate set of generic methods, different supported methods and different views of collection.

#### Difference between HashMap and HashTable?
1. Hashtable is synchronized, whereas HashMap is not.
2. Hashtable does not allow null keys or values. HashMap allows one null key and any number of null values.
3. The third significant difference between HashMap vs Hashtable is that Iterator in the HashMap is a fail-fast iterator while the enumerator for the Hashtable is not.

#### Difference between Iterator and Enumeration?

1. Iterators allow the caller to remove elements from the underlying collection during the iteration with its remove() method. You can not add/remove elements from a collection when using enumerator.
2. Enumeration is available in legacy classes i.e Vector/Stack etc. whereas Iterator is available in all modern collection classes.
3. Another minor difference is that Iterator has improved method names e.g. Enumeration.hasMoreElement() has become Iterator.hasNext(), Enumeration.nextElement() has become Iterator.next() etc

#### Difference between TreeSet and SortedSet?

SortedSet is an interface which TreeSet implements. That’ it !!

#### Difference between ArrayList and LinkedList?

1. LinkedList store elements within a doubly-linked list data structure. ArrayList store elements within a dynamically resizing array.
2. LinkedList allows for constant-time insertions or removals, but only sequential access of elements. In other words, you can walk the list forwards or backwards, but grabbing an element in the middle takes time proportional to the size of the list. ArrayLists, on the other hand, allow random access, so you can grab any element in constant time. But adding or removing from anywhere but the end requires shifting all the latter elements over, either to make an opening or fill the gap.
3. LinkedList has more memory overhead than ArrayList because in ArrayList each index only holds actual object (data) but in case of LinkedList each node holds both data and address of next and previous node.

#### How to make a collection read only?
- Collections.unmodifiableList(list);
- Collections.unmodifiableSet(set);
- Collections.unmodifiableMap(map);
These methods takes collection parameter and return a new read-only collection with same elements as in original collection.

#### How to make a collection thread safe?
- Collections.synchronizedList(list);
- Collections.synchronizedSet(set);
- Collections.synchronizedMap(map);
Above methods take collection as parameter and return same type of collection which are synchronized and thread safe.

#### Queue 
- Queue is an interface cannot instantiate
- Implement as a **Priority Queue** or as a **LinkedList**
- The elements of the **Priority Queue** are ordered according to their natural ordering, or by a Comparator provided at queue construction time, depending on which constructor is used.
- Operations on queue
  - **add()** -Adds an element at the tail of queue. More specifically, at the last of linkedlist if it is used, or according to the priority in case of priority queue implementation.
  - **peek()** -To view the head of queue without removing it. Returns null if queue is empty.
  - **remove()** -Removes and returns the head of the queue. Throws NoSuchElementException when queue is impty.
  - **poll()** -Removes and returns the head of the queue. Returns null if queue is empty.
  - Since it is a subtype of Collections class, it inherits all the methods of it namely size(), **isEmpty()**, contains() etc.

## Linked list as a Stack and Queue
- A double-linked list such as Java's LinkedList is a rather flexible data structure, so it makes sense to implement several data structures using it as a base. 
-So if you want to view it as a Queue, you'd say something like this:

`Queue q = new LinkedList();`

- If you want to use it as a stack, you would declare it like this:

`Deque s = new LinkedList();`

- The methods (push() and pop()) are from the Deque interface, which is also implemented by LinkedList. 
- The Javadoc for Deque states:
`
A linear collection that supports element insertion and removal at both ends. The name deque is short for "double ended queue" and is usually pronounced "deck". Most Deque implementations place no fixed limits on the number of elements they may contain, but this interface supports capacity-restricted deques as well as those with no fixed size limit.
`

### Default Capacity of ArrayList

1 What is meaning of capacity in ArrayList in java?
- Capacity is the size of the array which is used to store elements in the ArrayList.

2 Does size of ArrayList grows automatically in java?
- Yes, size of ArrayList grows automatically in java. ArrayList resizes itself dynamically in java.

3 What is default initial capacity of ArrayList in java?
- Default initial capacity of ArrayList is 10.
- java.util.ArrayList defines private static final variable DEFAULT_CAPACITY to define initial capacity of ArrayList.
```

 private static final int DEFAULT_CAPACITY = 10;
```
4 By what size ArrayList is resized in java? How much size increases when ArrayList is resized in java?
- ArrayList is resized by 50% of it’s current size. 
- So, ArrayList will be resized from 10, to 15, to 22, to 33 and so on.

5 But how ArrayList is resized in java?
- ArrayList’s add method internally calls ensureCapacityInternal method, which calls ensureExplicitCapacity method, which calls grow method,
  - grow method creates new array of higher capacity 
  - copies existing array to new one
  - return the new array.
  
6 Can we change default initial capacity of ArrayList in java?
Yes, rather than using new ArrayList(), you can use other constructor specified in java.util.ArrayList 
`List<Integer> list = new ArrayList<>(20);`

### Default Capacity, Load Factor and Rehashing of HashMap

- Default Capacity of HashMap is 16 (2^4). It is increased in a power of 2.
- Load Factor is a measure, which decides when exactly to increase the capacity(buckets) of hashmap, so as to maintain get and put operation complexity as O(1).
- Default load factor of Hashmap is 0.75f (i.e 75% of current map size).
- Lets say we have a well defined hash function which distrubes the keys equally in each bucket.
   - if there are 16 items in the hashmap each bucket will have 1 item. So get operation will take contant time of 1.
   - if there are 32 items in the hashmap each bucket will have 2 items, So get operation will take contant time of 2.
   - so on, if there are 50,000 items in the hashmap each bucket will have 50,000/2 items and so get operation is going to be costly.
   - if we increase the number of buckets then complextity of O(1) can be maintained. Load factor determines when to increase the number of buckets.
- So, when to increase the hashmap size is decided by product of,  **(initial capacity of hashmap * Load factor of hashmap).**
- `16 (initial capacity) * 0.75 (loadfactor) = 12 `
- This represents that uptil 12th key-value pair hashmap will keep its size to 16 and as soon as 13th item(key-value pair) will come into the Hashmap,  it will increase its size from default 2^4 = 16 buckets to 2^5 = 32 buckets.

- Once the capacity of the hashmap is increased, the hash values has to be calculated again for the existing key-value pairs. This is called rehashing.


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


## Object’s equal() method and when do you override it?
- If you don't override a class's equals() method, you won't be able to use those objects as a key in a hashtable and you probably won't get accurate Sets, such that there are no conceptual duplicates.
- boolean equals (Object obj) Decides whether two objects are meaningfully equivalent.
- int hashCode() Returns a hashcode int value for an object, so that the object can be used in Collection classes that use hashing, including Hashtable,HashMap, and HashSet.

- This is what the JDK 1.4 API documentation says about the equals method of Object class-

- Indicates whether some other object is "equal to" this one.

- The equals method implements an equivalence relation:
  - It is reflexive: for any reference value x, x.equals(x) should return true.
  - It is symmetric: for any reference values x and y, x.equals(y) should return true if and only if y.equals(x) returns true.
  - It is transitive: for any reference values x, y, and z, if x.equals(y) returns true and y.equals(z) returns true, then x.equals(z) should return true.
  - It is consistent: for any reference values x and y, multiple invocations of x.equals(y) consistently return true or consistently return false, provided no information used in equals comparisons on the object is modified.
  - For any non-null reference value x, x.equals(null) should return false.
  - The equals method for class Object implements the most discriminating possible equivalence relation on objects; that is, for any reference values x and y, this method returns true if and only if x and y refer to the same object (x==y has the value true).

Note that it is generally necessary to override the hashCode method whenever this method is overridden, so as to maintain the general contract for the hashCode method, which states that equal objects must have equal hash codes.

```
     public boolean equals(Object obj)
		{
			if(this == obj)
				return true;
			if((obj == null) || (obj.getClass() != this.getClass()))
				return false;
			// object must be Test at this point
			Test test = (Test)obj;
			return num == test.num &&
			(data == test.data || (data != null && data.equals(test.data)));
		}
```

### Hascode fuction for String class. - hashCode()
- Returns a hash code for this string. The hash code for a String object is computed as
  `s[0]*31^(n-1) + s[1]*31^(n-2) + ... + s[n-1]`
 
 -using int arithmetic, where s[i] is the ith character of the string, n is the length of the string, and ^ indicates exponentiation. (The hash value of the empty string is zero.)

- The value 31 was chosen because it is an odd prime. If it were even and the multiplication overflowed, information would be lost, as multiplication by 2 is equivalent to shifting. The advantage of using a prime is less clear, but it is traditional. A nice property of 31 is that the multiplication can be replaced by a shift and a subtraction for better performance: 31 * i == (i << 5) - i. Modern VMs do this sort of optimization automatically.

Code from String class is different from the logic mentioned above.
```
 public int hashCode() {
        int h = hash;
        if (h == 0 && value.length > 0) {
            char val[] = value;

            for (int i = 0; i < value.length; i++) {
                h = 31 * h + val[i];
            }
            hash = h;
        }
        return h;
    }
```

#### User defined hashcode implementation
```
public int hashCode()
 {
 int hash = 17;
 hash = 31*hash + who.hashCode();
 hash = 31*hash + when.hashCode();
 hash = 31*hash + ((Double) amount).hashCode();
 return hash;
 }
 ```
 
#### Hashing with wrong hashCode() or equals(). Suppose that you implement a data type OlympicAthlete for use in a java.util.HashMap.

- What happens if you override hashCode() but not equals().
  - If only equals is overriden, then when you call myMap.put(first,someValue) first will hash to some bucket and when you call myMap.put(second,someOtherValue) it will hash to some other bucket (as they have a different hashCode). So, although they are equal, as they don't hash to the same bucket, the map can't realize it and both of them stay in the map.

- What happens if you override equals() but not hashCode().
  - If you only override hashCode then when you call myMap.put(first,someValue) it takes first, calculates its hashCode and stores it in a given bucket. Then when you call myMap.put(second,someOtherValue) it should replace first with second as per the Map Documentation because they are equal (according to the business requirement). But the problem is that equals was not redefined, so when the map hashes second and iterates through the bucket looking if there is an object k such that second.equals(k) is true it won't find any as second.equals(first) will be false.

- What happens if you override hashCode() but implement public boolean equals(OlympicAthlete that) instead of public boolean equals(Object that).
  - it wont overide the original equals method.
