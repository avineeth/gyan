
# Collections

The core collection interface:

![Image](https://github.com/avineeth/gyan/blob/master/img/collections.gif?raw=true)


# CODING CHECKLIST

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
#### Integer to int
Integer.intValue()



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
