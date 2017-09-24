
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

### Permuations and Combination
- permutation order matters, combination order does not matter
- trivia - a combination lock should be called a permutation lock.
- permutation formula - n!/(n-r)!
- combination formula - n!/(n-r)!r!

# Popular Questions

## Permutations of a String
- Can be solved using recursion (Base Case and build approach)
- Assume we have a string S represented by characters a1,a2,a3... aN;
- Base case N =1
  - The only permutation is S=a1
- Case N=2
  - S =a1a2 and the permutations are a1a2 and a2a1
- Case N=3 (gets tricky)
  - S = a1a2a3, here we can generate all permuations if we are given a1a2 and a2a1 and we insert a3 at all positions.
  - we solve for f(n-1) and then push aN into every spot.
  
```
  	public static List<String> getPermutations(String str) {
		
		if(str == null) 
			return null;
		
		List<String> permutations = new ArrayList<>();
		if(str.length() ==1) {
			permutations.add(str);
			return permutations;
		}
		char insertchar = str.charAt(0);
		String remainderstr = str.substring(1);
		List<String> words = getPermutations(remainderstr);
		for(String s : words) {
			 List<String> c= insertCharacter(s, insertchar);
			 permutations.addAll(c);			
		}
		
		return permutations;
		
	}
	public static List<String> insertCharacter(String str, char c) {
		
		List<String> permutations = new ArrayList<>();
		for(int i = 0; i <= str.length(); i++) {
			String s =  str.substring(0, i) + c + str.substring(i, str.length());
			permutations.add(s);
		}
		return permutations;
	}

```
  
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


### Sorting 900 megabytes of data using only 100 megabytes of RAM:
One example of external sorting is the ** external merge sort** algorithm, which sorts chunks that each fit in RAM, then merges the sorted chunks together.[1][2] For example, for sorting 900 megabytes of data using only 100 megabytes of RAM:

1. Read 100 MB of the data in main memory and sort by some conventional method, like quicksort.
2. Write the sorted data to disk.
3. Repeat steps 1 and 2 until all of the data is in sorted 100 MB chunks (there are 900MB / 100MB = 9 chunks), which now need to be merged into one single output file.
4. Read the first 10 MB (= 100MB / (9 chunks + 1)) of each sorted chunk into input buffers in main memory and allocate the remaining 10 MB for an output buffer. (In practice, it might provide better performance to make the output buffer larger and the input buffers slightly smaller.)
5. Perform a 9-way merge and store the result in the output buffer. Whenever the output buffer fills, write it to the final sorted file and empty it. Whenever any of the 9 input buffers empties, fill it with the next 10 MB of its associated 100 MB sorted chunk until no more data from the chunk is available. This is the key step that makes external merge sort work externally -- because the merge algorithm only makes one pass sequentially through each of the chunks, each chunk does not have to be loaded completely; rather, sequential parts of the chunk can be loaded as needed.


### Use a stack to sort given data. 
-- done office computer

### Given an array with positive integers only, find the MIN integer that is missing from the array.
use the formula n*(n-1)/2 to find total sum of number


