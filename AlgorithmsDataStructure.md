
# CODING CHECKLIST

#### String to char array
```
	char[] val = "Hello World".toCharArray();
	for(char c : val) {
		System.out.println(c);
	}
```

#### To print an array
```
import java.util.Arrays;
System.out.println(Arrays.toString(int[] a));
```  
Returns a string representation of the contents of the specified array.

#### To copy an array
```
int[] arr= {12, 3, 4, 1, 6, 9};
int[] arr2 = Arrays.copyOf(arr, arr.length);
```
#### To convert an array of String to ArrayList?
```
//String array
String[] words = {"ace", "boom", "crew", "dog", "eon"};
//Use Arrays utility class
List wordList = Arrays.asList(words);
//Now you can iterate over the list
```

#### To reverse a List
`Collections.reverse(list);`

#### Integer to int
Integer.intValue()

#### Middle of an array
    int mid = lo + (hi - lo) / 2;

#### HashMap traversal
If you're only interested in the keys, you can iterate through the keySet() of the map:
```
Map<String, Object> map = ...;

for (String key : map.keySet()) {
    // ...
}
```

If you only need the values, use values():
```
for (Object value : map.values()) {
    // ...
}
```
Finally, if you want both the key and value, use entrySet():
```
for (Map.Entry<String, Object> entry : map.entrySet()) {
    String key = entry.getKey();
    Object value = entry.getValue();
    // ...
}
```


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
    /**
     * Default initial capacity.
     */
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
  ```
  	public void ensureCapacity(int minCapacity) {
	         modCount++;
		 int oldCapacity = elementData.length;
	         if (minCapacity > oldCapacity) {
	             Object oldData[] = elementData;
	             int newCapacity = (oldCapacity * 3)/2 + 1;
	             if (newCapacity < minCapacity)
	                 newCapacity = minCapacity;
	             // minCapacity is usually close to size, so this is a win:
	             elementData = Arrays.copyOf(elementData, newCapacity);
	        }
	    }
```

##### Can we change default initial capacity of ArrayList in java?
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


# Sorting

## Elementary Sorts

### Selection Sort
```
import java.util.Arrays;
public class SelectionSort {
	public static void main(String[] args) {
		int[] arr = {3,2,1,4,5};
		int n = arr.length;
		for(int i=0; i<n; i++) 
			for(int j=i+1; j<n; j++) {
				if(arr[i] > arr[j]) {
					int temp = arr[i];
					arr[i] = arr[j];
					arr[j] = temp;
				}
			}
		System.out.println(Arrays.toString(arr));
	}
}
```
### Insertion Sort

```
import java.util.Arrays;
public class InsertionSort {
	public static void main(String[] args) {
		int[] arr = {4,1,2,3,8,6,4,1};
		int n = arr.length;
		for(int i=0; i< n; i++) {
			for(int j=i; j>0; j--) {
				if(arr[j] < arr[j-1]) {
					int temp =arr[j];
					arr[j] = arr[j-1];
					arr[j-1] = temp;
				}
				else 
					break;
			}
		}
		System.out.println(Arrays.toString(arr));
	}
}
```

### Merge Sort

#### Definition:
- A comparison based sorting algorithm
- Divides entire dataset into groups of at most two.
- Compares each number one at a time, moving the smallest number to left of the pair.
- Once all pairs sorted it then compares left most elements of the two leftmost pairs creating a sorted group of four with the smallest numbers on the left and the largest ones on the right.
- This process is repeated until there is only one set.

#### What you need to know:

- This is one of the most basic sorting algorithms.
- Know that it divides all the data into as small possible sets then compares them.

#### Big O efficiency:

- Best Case Sort: Merge Sort: O(½ n lg n)
- Average Case Sort: Merge Sort: O(n log n)
- Worst Case Sort: Merge Sort: O(n log n)

[MergeSort.java](../../../algos/blob/master/MergeSort.java)

### Quick Sort

```
  //1. shuffle
  //2. partition
  //3. sort
  public static int partition(Comparable[] a, int lo, int hi) {
        int i= lo;
        int j=hi+1;
        while(true) {
            while(less(a[++i],a[lo]))
                if(i==hi) break;
			while(less(a[lo],a[--j])) 
				if(j==lo) break;
            if(i>=j) break;
            exch(a, i, j);
        }
        exch(a,lo,j);
		return j;
    }
 
 public static void sort(Comparable[] a, int lo, int hi) {		
		if(lo >= hi) return;
		int part = partition(a, lo, hi);
		sort(a,lo,part-1);
		sort(a, part+1, hi);
	}
```	
	
#### Java's Arrays.sort method uses Quicksort for arrays of primitives and merge sort for arrays of objects. 
The most likely reason: quicksort is not stable, i.e. equal entries can change their relative position during the sort; among other things, this means that if you sort an already sorted array, it may not stay unchanged.
Since primitive types have no identity (there is no way to distinguish two ints with the same value), this does not matter for them. But for reference types, it could cause problems for some applications. Therefore, a stable merge sort is used for those.


### Difference between Comparable and Comparator Interface. 
- a.compareTo(b): Comparable interface :
  - Compares values and returns an int which tells if the values compare less than, equal, or greater than.
  - If your class objects have a natural order, implement the Comparable<T> interface and define this method. All Java classes that have a natural ordering implement Comparable<T> - Example: String, wrapper classes, BigInteger
- compare(a, b):
  - Comparator interface : Compares values of two objects. This is implemented as part of the Comparator<T> interface, and the typical use is to define one or more small utility classes that implement this, to pass to methods such as sort() or for use by sorting data structures such as TreeMap and TreeSet. You might want to create a Comparator object for the following:
  - Multiple comparisons. To provide several different ways to sort something. For example, you might want to sort a Person class by name, ID, age, height, ... You would define a Comparator for each of these to pass to the sort() method.
  - System class To provide comparison methods for classes that you have no control over. For example, you could define a Comparator for Strings that compared them by length.
  - Strategy pattern To implement a Strategy pattern, which is a situation where you want to represent an algorithm as an object that you can pass as a parameter, save in a data structure, etc.
  
  
Binary tree: Tree where each node has up to two leaves

  1
 / \
2   3
Binary search tree: Used for searching. A binary tree where the left child contains only nodes with values less than the parent node, and where the right child only contains nodes with values greater than or equal to the parent.

  2
 / \
1   3

### Symbol Tables
- Key-value pair abstraction.
  - Insert a value with specified key.
  - Given a key, search for the corresponding value.
- Ex. DNS lookup.
  - Insert domain name with specified IP address.
  - Given domain name, find corresponding IP address.
* key domain name          IP address
* www.cs.princeton.edu     128.112.136.11
* www.princeton.edu        128.112.128.15
* www.yale.edu             130.132.143.21

### Priority Queues
- Remove the largest or smallest queue by the natural order.
- Requirement - Generic Items that are Comparable
- Example Client- Find the largest transaction in a huge set (million/billions) of transactions which cannot be stored in memory. Keep track of top 5 transactions using Priority Queues.

```
MinPQ<Transaction> pq = new MinPQ<Transaction>();
while (StdIn.hasNextLine())
{
 String line = StdIn.readLine();
 Transaction item = new Transaction(line);
 pq.insert(item);
 if (pq.size() > M)
	pq.delMin(); // Important here.
}
```
Priority queue Implementation

1. Maitain an Un-ordered array as queue (unsorted).
   - Insert to the end. When removing max, parse the array to get the maximum and remove.
   - Complexity Insert - 1 Max - N, Del Max - N
2. Maitain an Ordered array as queue (sorted).
   - When inserting, insert it at right postition. When removing max, remove from end.
   - Complexity Insert - N, Max -1, Del Max - 1
3. Binary Heap

#### Binary Heap
- based on Complete Binary Tree
- Complexity Insert - logN, Max -1, Del Max - logN

![Image](https://github.com/avineeth/gyan/blob/master/img/heap-representations.png?raw=true)

##### Heap order Binary tree
- key in nodes
- In Array Representation start from index 1
- Largest Key is a[1]
- Parents key no smaller than childrens keys.
- Parent of node k is at k/2
- Children of node k is at 2k at 2k+1
- In computer science, a heap is a specialized tree-based data structure that satisfies the heap property: If A is a parent node of B, then the key (the value) of node A is ordered with respect to the key of node B with the same ordering applying across the heap.
- The Java platform (since version 1.5) provides a binary heap implementation with class java.util.PriorityQueue in the Java Collections Framework. This class implements by default a min-heap; to implement a max-heap, programmer should write a custom comparator.
  - eg: PriorityQueue<Integer> queue = new PriorityQueue<>(10, Collections.reverseOrder());
  - add(E e)- Inserts the specified element into this priority queue.
  - peek() - Retrieves, but does not remove, the head of this queue, or returns null if this queue is empty.
  - poll() - Retrieves and removes the head of this queue, or returns null if this queue is empty.

- A heap can be classified further as either a "max heap" or a "min heap". 
- In a max heap, the keys of parent nodes are always greater than or equal to those of the children and the highest key is in the root node.
- In a min heap, the keys of parent nodes are less than or equal to those of the children and the lowest key is in the root node.

##### Swim operation
- When child's key becomes larger than its parents key
  - Exchange Child Key with Parents key.
  - Repeat until the heap order is restored.
  - analogy - an better employee is promoted above his manager.
```
private void swim(int k) {
  while (k > 1 && less(k/2, k))  {
    exch(k, k/2);
    k = k/2;
  }
 }
```
##### Insertion in a heap
- Insert. Add node at end, then swim it up.
- Cost. At most 1 + lg N compares.
```
public void insert(Key x)
{
 pq[++N] = x;
 swim(N);
}
```
##### Sink Operation
- Parent's key becomes smaller than one (or both) of its children's.
- Exchange key in parent with key in larger child.
- Repeat until heap order restored.
```
private void sink(int k)
{
 while (2*k <= N)
 {
 int j = 2*k;
 if (j < N && less(j, j+1)) j++;
 if (!less(k, j)) break;
 exch(k, j);
 k = j;
 }
}
```
##### Heap Sort
- start with a arbitary array
- heap construction - Build max heap using bottom-up method
```
for (int k = N/2; k >= 1; k--)
 sink(a, k, N);
```
 - Second pass.
   - Remove the maximum, one at a time.
   - Leave in array, instead of nulling out.
 ```  
while (N > 1)
{
 exch(a, 1, N--);
 sink(a, 1, N);
}
```
- Complexity
  - Heap construction uses ≤ 2 N compares and exchanges.
  - Heapsort uses ≤ 2 N lg N compares and exchanges.


#### Advantages of BST over Hash Table
- Hash Table supports following operations in Θ(1) time.
1) Search
2) Insert
3) Delete

- The time complexity of above operations in a self-balancing Binary Search Tree (BST) (like Red-Black Tree, AVL Tree, Splay Tree, etc) is O(Logn).
- So Hash Table seems to beating BST in all common operations. When should we prefer BST over Hash Tables, what are advantages. Following are some important points in favor of BSTs.

- We can get all keys in sorted order by just doing Inorder Traversal of BST. This is not a natural operation in Hash Tables and requires extra efforts.
- Doing order statistics, finding closest lower and greater elements, doing range queries are easy to do with BSTs. Like sorting, these operations are not a natural operation with Hash Tables.
- BSTs are easy to implement compared to hashing, we can easily implement our own customized BST. To implement Hashing, we generally rely on libraries provided by programming languages.
- With BSTs, all operations are guaranteed to work in O(Logn) time. But with Hashing, Θ(1) is average time and some particular operations may be costly, especially when table resizing happens.

### Graph Theory

- A graph   G is an ordered pair 
  - of a set V of vertices and
  - a set E of edges
  - represented as **G= (V,E)**

- Different Types of Graphs
  1. Directed Graph - Digraph - Connection is One-Way - eg - Webcrawling
  2. Undirected Graph - Connection is both ways. - eg Facebook friends
  3. Weighted - weight or cost associated with the edges - eg - Road Network


- Number of elements in a graph
  - |V| -> number of vertices
  - |E| -> number of edges
  - if |V| = n, maximum possible edges in a graph
    1. 0<= |E| <= n(n-1) , in a directed graph
    2. 0<= |E| <= n(n-1)/2 , in an un-directed graph

- Dense Graph-> too many edges - Adjacency matrix to represent the graph
- Sparse Graph -> too few edges  - Adjacency List to represnt the graph

- Path in a graph
  - Walk - a sequence of vertices where each adjacent pair is connected by an edge.
  - (Simple) Path - a walk in which no vertices (and thus no edges) are repeated.
  - Trail - a walk in which vertices (and not edges) can be repeated.
  - Closed Walk - starts and ends at the same vertex.
  - Cycle - starts and ends at the same vertex but no repetitiin other than start/end vertex.
  - Acyclic graph - a graph with no cycle - A tree is an example of directed acyclic graph.
  - Directed Acyclic graph (DAG)
  - Strongly Connected/Connection Graph - if there is a path from any vertex to any other vertex.

- How is graph represented in memory

##### Edge List and Vertex List 
- this can be implemented using arraylists
- Vertex can be just the name of the Vertex.
- Edge can be an object which has Start Vertex and End Vertex.
- Space Complexity = O(|V| + |E|)
- Time Complexity 
  - for finding adjacent vertices for a vertex. 
    - All elements in Edge List has to be scanned to find all the adjacent vertices. Complexity - O(|E|)
  - to find if 2 nodes are connected.
    - has to traverse the edge list - O(|E|)
- Space Complexity     
    - O(|E|) ~= O(|V| * |V|) -> quadratic -> not efficient
![Image](https://github.com/avineeth/gyan/blob/master/img/Graph-representation-EdgeList.PNG?raw=true)

##### Adjacency Matrix
- Vertex list same as before
- Edge list represented by a VxV matrix called A
- A(i,j) =1 if there is an edge between two vertices Vi and Vj
- A(i,j) =0 if there is no edge
- Time complexity
  - for finding adjacent vertices for a vertex. 
    - if vertex index is provided then its constant time. else time to scan the vertex list.
  - to find if 2 nodes are connected.
    - if vertex index is provided then its constant time. else time to scan the vertex list.	  
- Space Complexity  - O(|V| * |V|)
  - this is inefficient for large graphs like facebook. In which each Vertex may not be connected to all other vertices.
  - Good for dense graphs.
![Image](https://github.com/avineeth/gyan/blob/master/img/Graph-representation-AdjacencyMatrix.PNG?raw=true)

##### Adjacency List
- An array of linked lists is used.
- Size of the array is equal to number of vertices.
- Let the array be array[]. An entry array[i] represents the linked list of vertices adjacent to the ith vertex. 

![Image](https://github.com/avineeth/gyan/blob/master/img/Graph-representation-AdjacencyList.JPG?raw=true)

#### Graph traversal algorithms

##### Breadth-first Search 
- Uses Queues
- Time Complexity - O(|V| + |E|) and Space Complexity O(|V|)
- Nodes will be visit in the order imposed by the FIFO queue
1. Put an arbitrary node s in queue 
2. Mark the node s as visited
3. Repeat while queue is not empty:
   - curV = remove the first node from queue Q
   - Print and Put all of curV's unvisited neighbor nodes in a queue Q
   - Mark all inserted nodes as visited

```
public void bfsSearch(int s) {
	int[] visited = new int[V.length];
	Queue<Integer> queue = new LinkedList<Integer>();	
	queue.add(s);
	visited[s] =1;
		
	while(!queue.isEmpty()){
		Integer curV = queue.remove();
		for(Integer e: EdgeList[curV]) {
			if(visited[e] !=1) {
				System.out.println(e);					
				queue.add(e);
				visited[e] =1;
			}
		}
	}
}
```
##### Depth-first search. 
- One starts at some arbitrary node as the root and explores as far as possible along each branch before backtracking.
- Uses Stacks
- Time Complexity - O(|V| + |E|) and Space Complexity O(|V|)
- Differs from BFS 
  - it uses a stack instead of a queue, and
  - it delays checking whether a vertex has been discovered until the vertex is popped from the stack rather than making this check before pushing the vertex.
```
public void dfsSearch(int s) {
	int[] visited = new int[V.length];
	Stack<Integer> stack = new Stack<Integer>();
	visited[s] =1;
	stack.push(new Integer(s));
	
	while(!stack.empty()) {
		Integer curV = stack.pop();
		if(visited[curV] !=1) {
			visited[curV] =1; //mark as visited
			System.out.println(curV);
		}
		for(Integer e: EdgeList[curV])
			stack.push(e); //push it into stack;
		}			
	}
```
- recursive algorithm
```
public void dfsRecursion(int s, int[] visited) {
        visited[s] = 1;
		System.out.println(s);
        for(Integer e: EdgeList[s]) {
            if (visited[e] != 1) {
                dfsRecursion(e, visited);
            }
        }
    }
```    

##### Prims Minimum Spanning Tree

- An edge-weighted graph is a graph where we associate weights or costs with each edge.
- A minimum spanning tree (MST) of an edge-weighted graph is a spanning tree whose weight (the sum of the weights of its edges) is no larger than the weight of any other spanning tree.

![Image](https://github.com/avineeth/gyan/blob/master/img/edge-weighted-graph-representation.png?raw=true)
![Image](https://github.com/avineeth/gyan/blob/master/img/mst.png?raw=true)

- You need Three Data structures
   - Queue<Edge> mst - to return MST tree to client
   - PriorityQueue<Edge> pq - to hold the edges and find one of minimal weight.
  - marked[] - to keep track of visited node
  
```
public void computeMST() {
	mst = new LinkedList<Edge>();
	pq = new PriorityQueue<Edge>();
	marked = new boolean[G.getV()];
		
	visit(G,0);
	while(!pq.isEmpty()) {
		Edge e = pq.poll();
		int start = e.getStart();
		int end = e.getEnd();
		if(marked[start] && marked[end]) continue; //ignore if both endpoint in T
		mst.add(e);
		if(!marked[start]) visit(G, start);
		if(!marked[end]) visit(G, end);
	}
		
}
	
private void visit(Graph g, int v) {
	marked[v] = true;
	for(Edge e: g.getEdgeList(v)) {
		if(!marked[e.other(v)]) {
			pq.add(e);
		}
	}
}

```

#### Djikstra's Algorithm - shortest path
- Consider Vertices in the increasing order of distance from Source S.


- algorithm similar to Prim's Minimum spanning tree algoritm
- Both compute a spanning tree
- Main difference is to choose the vertex of the tree
  - Prim's chooses the vertex closest to the tree. (via undirected edge).
  - Djikstra's chooses closest to the Source. (via a directed path).
- Djikstra's algorithm is for directed graph
- Prim's algorithm is for undirected graph

### Divide and Conquer
- Like Greedy and Dynamic Programming, Divide and Conquer is an algorithmic paradigm. A typical Divide and Conquer algorithm solves a problem using following three steps.

1. Divide: Break the given problem into subproblems of same type.
2. Conquer: Recursively solve these subproblems
3. Combine: Appropriately combine the answers

- Following are some standard algorithms that are Divide and Conquer algorithms.

1) Binary Search is a searching algorithm. In each step, the algorithm compares the input element x with the value of the middle element in array. If the values match, return the index of middle. Otherwise, if x is less than the middle element, then the algorithm recurs for left side of middle element, else recurs for right side of middle element.

2) Quicksort is a sorting algorithm. The algorithm picks a pivot element, rearranges the array elements in such a way that all elements smaller than the picked pivot element move to left side of pivot, and all greater elements move to right side. Finally, the algorithm recursively sorts the subarrays on left and right of pivot element.

3) Merge Sort is also a sorting algorithm. The algorithm divides the array in two halves, recursively sorts them and finally merges the two sorted halves.

4) Closest Pair of Points The problem is to find the closest pair of points in a set of points in x-y plane. The problem can be solved in O(n^2) time by calculating distances of every pair of points and comparing the distances to find the minimum. The Divide and Conquer algorithm solves the problem in O(nLogn) time.


### Greedy Algorithms | Set 1 (Activity Selection Problem)
- Greedy is an algorithmic paradigm that builds up a solution piece by piece, always choosing the next piece that offers the most obvious and immediate benefit. Greedy algorithms are used for optimization problems.
- An optimization problem can be solved using Greedy if the problem has the following property: **At every step, we can make a choice that looks best at the moment, and we get the optimal solution of the complete problem.**
- If a Greedy Algorithm can solve a problem, then it generally becomes the best method to solve that problem as the Greedy algorithms are in general more efficient than other techniques like Dynamic Programming.
- But Greedy algorithms cannot always be applied. For example, Fractional Knapsack problem (See this) can be solved using Greedy, but 0-1 Knapsack cannot be solved using Greedy.


# Popular Algorithms

## Nuts and bolts.
A disorganized carpenter has a mixed pile of n nuts and n bolts. The goal is to find the corresponding pairs of nuts and bolts. Each nut fits exactly one bolt and each bolt fits exactly one nut. By fitting a nut and a bolt together, the carpenter can see which one is bigger (but the carpenter cannot compare two nuts or two bolts directly). Design an algorithm for the problem that uses nlogn compares (probabilistically).

### Solution.
- If we could compare nuts to nuts and bolts to bolts, we could simply sort both nuts and bolts separately and pair off the nuts and bolts in matching positions. However, since comparing identical items is not allowed, this problem appears to be harder than sorting. As we will soon see, it can indeed be reduced to sorting.
- The brute-force solution would be to compare each nut with all bolts to find the matching bolt and can be implemented in O(n2). In fact, we can do better than n2 by using a randomized algorithm similar to quicksort.
- Suppose we choose a nut and partition all bolts in {1…n}, by comparing with this nut, into three intervals : {1…i-1}, {i}, {i+1, n} such that each bolt in {1, i-1} is smaller, bolt i matches and each bolt in {i+1, n} is larger than the chosen nut. This procedure is similar to the partition procedure used in quicksort and can be implemented in O(n). Now, we can use the matching bolt to partition all nuts in three intervals in a similar manner so that the nut i and bolt i match. We have reduced the problem of finding matchings in the interval {1…n} into two smaller subproblems: finding matchings in the intervals {1…i-1} and {i+1…n}.
- If at each step, we choose the nut (to partition the bolts) randomly, we will get similar performance guarantees as quicksort i.e., randomized O(n log n) time.

## 2-Sum Problem
Given an array of integers, find two numbers such that they add up to a specific target number. Write a function twoSum that should return indices of the two numbers such that they add up to the target, where index1 must be less than index2. You may assume that each input would have exactly one solution. 
- Input: numbers={2, 7, 11, 15}, target=9 Output: index1=1, index2=2. 

- Solution 1 - Brute Force
  - for each pair (i, j) in the input, we test if arr[i] + arr[j] = target, a nested for loop like the following could easily solve the problem. 
  - The time complexity: O(N^2)
  ```
  for(int i=0; i< arr.length; i++) 
			for(int j=i; j<arr.length; ++j) {
				if((arr[i] + arr[j]) == target) {
  ```
- Solution 2 - Sorting
  - Sort the input in increasing order
  - Make two pointers i, j pointing at the head and tail elements of the sorted array
  - If the target is bigger, we move the head pointer forward by one step and thus try to increase the sum, if the target is smaller we move the tail head towards the head by one step and thus try to decrease the sum a bit, if target is equal to the sum, then we are done by the assumption that there is only one such pair
  -  Complexity O(NlogN)
  
  ```
   	int low =0;
		int hi = arr.length-1;
		
		while(low<=hi) {
			int sum = arr[low] + arr[hi];
			if(sum == target) {
				match[0] = arr[low];
				match[1] = arr[hi];
				break;
			}
			else if(sum > target) 
				hi = hi -1;
			else 
				low = low -1;
		}
	```
  
[TwoSum.java](../../../algos/blob/master/TwoSum.java)

## Binary Search
- Easiest way to find a key in a Array.
- Pre-condition - Array has to be sorted.
- Complexity - logN
- Compare key against middle entry.
  - Too small, go left.
  - Too big, go right.
  - Equal, found.
  
```
public static int binarySearch(int[] a, int key)
 {
 int lo = 0, hi = a.length-1;
 while (lo <= hi)
 {
 int mid = lo + (hi - lo) / 2;
 if (key < a[mid]) hi = mid - 1;
 else if (key > a[mid]) lo = mid + 1;
 else return mid;
 }
 return -1;
 }
```

## 3-Sum Problem

- Find a triplet that sum to a given value.
- Given an array and a value, find if there is a triplet in array whose sum is equal to the given value. If there is such a triplet present in array, then print the triplet and return true. Else return false. For example, if the given array is {12, 3, 4, 1, 6, 9} and given sum is 24, then there is a triplet (12, 3 and 9) present in array whose sum is 24.

- Solution 1 
  - Brute force nested loop. Complexity O(N^3)
- Solution 2
  - Sort the elements 
  - make one element the anchor
  - Make two pointers i, j pointing at the head and tail elements of the rest sorted array
  - move the pointers according to if the value is less or more than the total.
  
  [ThreeSum.java](../../../algos/blob/master/ThreeSum.java)
  
 ## 4-Sum Problem
 
-  4-SUM. Given an array a[] of n integers, the 4-SUM problem is to determine if there exist distinct indices i, j, k, and l such that a[i]+a[j]=a[k]+a[l]. Design an algorithm for the 4-SUM problem that takes time proportional to n2 (under suitable technical assumptions).

- Solution
  -  create a hash table with (nC2) key-value pairs.
  
  [FourSum.java](../../../algos/blob/master/FourSum.java)
  
  
  ## Social network connectivity. TODO
- Given a social network containing n members and a log file containing m timestamps at which times pairs of members formed friendships, design an algorithm to determine the earliest time at which all members are connected (i.e., every member is a friend of a friend of a friend ... of a friend). Assume that the log file is sorted by timestamp and that friendship is an equivalence relation. The running time of your algorithm should be mlogn or better and use extra space proportional to n.

Union-find

・Kruskal's minimum spanning tree algorithm.
- Reverse a linked List 
  - Time Complexity O(N) and space complexity O(1)
