# Sorting

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