
# Table of Contents
 - [Coding Checklist](#coding-checklist)
 - [Check if a String is a Palindrome](#check-if-a-string-is-a-palindrome)
 - [Longest Palindromic Substring](#longest-palindromic-substring)
 - [Permutations of a String](#permutations-of-a-string)
 - [Given 2 strings find if one string is a permutation (Anagram) of another](#given-2-strings-find-if-one-string-is-a-permutation-of-another)
 - [Determine if a string has all unique characters.](#determine-if-a-string-has-all-unique-characters)
 - [Basic String Compression](#basic-string-compression)
 - [String pattern match indexOf](#string-indexof)
 - [Nuts and bolts](#nuts-and-bolts)
 - [2-Sum Problem](#2-sum-problem)
 - [3-Sum Problem](#3-sum-problem)
 - [4-Sum Problem](#4-sum-problem)
 - [Sorting 900 megabytes of data using only 100 megabytes of RAM](#sorting-900-megabytes-of-data-using-only-100-megabytes-of-ram)
 - [Given an array with positive integers only, find the MIN integer that is missing from the array.](#given-an-array-with-positive-integers-only-find-the-min-integer-that-is-missing-from-the-array)

 - [Algorithm Approaches](#algorithm-approaches)
     - [Sliding Window](#sliding-window)
     - [Linked List Runner Technique](#linked-list---runner-technique)
 
## CODING CHECKLIST

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

#### int to String
Integer.toString(intvalue);

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

### ASCII Table:

http://www.asciitable.com/

### Comparator Example

```
Collections.sort(palindromes, new SORTBYLENGTH());

class SORTBYLENGTH implements Comparator<String> {
	@Override
	public int compare(String o1, String o2) {
		int retval = 0;
				
		if(o1.length() < o2.length()) retval = -1;
		else if(o1.length() > o2.length()) retval = 1;
		else retval =0;
		
		return retval;
	}
}
```

# Popular Questions

## Check if a String is a Palindrome

```
	public static boolean isPalindrome(String str) {
		char[] arr = str.toCharArray();
		int i=0,j=str.length()-1;
		while(i <= j) {
			System.out.println("i :[" + arr[i] + "] j:" + arr[j]);
			if(arr[i++] != arr[j--]) return false;
		}
		return true;
	}
```
## Longest Palindromic Substring
1. Brute Force approach - Complexity O(N^3)
2. Expand around center - Complexity O(N^2)
3. Manachers Algorithm  - Complexity O(N)


```
	public static void longestPalindrome2(String str) {
		int start =0;
		int maxlength =1;
		
		for(int i=1; i< str.length(); i++) {
			int lo =i-1;
			int hi =i+1;
			
			while(lo > 0 && hi < str.length()
					&& str.charAt(lo) == str.charAt(hi)) {
			
				if((hi-lo) > maxlength ) {
					maxlength = hi-lo;
					start = lo;
				}
				lo--;
				hi++;
			}
		}
		System.out.println(str.substring(start, start+maxlength +1));
	}	
```	

```
	public static void longestPalindrome(String str) {
		List<String> palindromes = new ArrayList<>();
		for(int i = 0; i< str.length(); i++) {
			for(int j = i+1; j <= str.length(); j++) {
				//System.out.println(str.substring(i, j));
				String s = str.substring(i, j);
				if(isPalindrome(s)) {
					palindromes.add(s);
				}
				
			}
		}
		Collections.sort(palindromes, new SORTBYLENGTH());
		Collections.reverse(palindromes);
		System.out.println(palindromes.get(0));
	}
```

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
## Given 2 strings find if one string is a permutation of another
- the same question can be asked "How to check if two Strings are anagrams of each other?"
- for 2 strings to be permutations of each other the length should be same. return false if length is not same.
- Sort both the strings if the sorted order is equal then both strings are permutations of each other.

## Determine if a string has all unique characters.
- the same question can be asked 
- pangram is a sentence using every letter of a given alphabet at least once. 
- The best-known English pangram is "The quick brown fox jumps over the lazy dog."
```
public static boolean uniquechars(String str) {
		
		boolean[] chararr = new boolean[128];
		char[] strarr = str.toCharArray();
		for(char c: strarr) {
			if(chararr[c])
				return false;
			chararr[c] = true;
		}	
				
		return true;
}
```
## Basic String Compression
- easy problem
- go through the string and keep counting.

```
public static String getCompressedString(String str) {
		
		char[] strarr = str.toCharArray();
		char currchar = strarr[0];
		String curstr = str.substring(0,1);
		int count =1;
		
		for(int i=1; i< str.length(); i++) {
			if(strarr[i] == currchar) {
				count++;
			}
			else {
				currchar = strarr[i];
				curstr = curstr + count + currchar;
				count =1;
			}			
		}	
		return curstr + count;
}
```	
## String Indexof
- Naive implementation is easy, but takes O(NxM) time.
- More sophesticated algorithms exist like KMP pattern matching.
```
 public static int indexOf(String str1, String str2) {
        char[] str1arr = str1.toCharArray();
        char[] str2arr = str2.toCharArray();
        int N = str1arr.length;
        int M = str2arr.length;

        for(int i =0 ; i< N ; i++) {
            int j=0;
            while(j < M){
                 if(str1arr[i+j] != str2arr[j])
                     break;
                j++;
            }
            if(j == M) return i;
        }
       return  -1;
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

### Greatest Common Divisor (GCD) - Euclids algorithm
- The gcd of two numbers also divides their difference.
- For example, to compute gcd(48,18), divide 48 by 18 to get a quotient of 2 and a remainder of 12. Then divide 18 by 12 to get a quotient of 1 and a remainder of 6. Then divide 12 by 6 to get a remainder of 0, which means that 6 is the gcd. Here, we ignored the quotient in each step, except to notice when the remainder reached 0, signalling that we had arrived at the answer. Formally, the algorithm can be described as:
```
gcd(a, 0) = a
gcd(a, b) = gcd(b, a mod b) 
def gcd(a: Int, b: Int): Int = if(b ==0) a else gcd(b, a % b)
```
## Algorithm Approaches

### Sliding Window

https://medium.com/outco/how-to-solve-sliding-window-problems-28d67601a66

##### How do you identify them?
So the first thing you want to be able to do is to identify a problem that uses a sliding window paradigm. Luckily, there are some common giveaways:
- The problem will involve a data structure that is ordered and iterable like an array or a string
- You are looking for some subrange in that array/string, like a longest, shortest or target value.
- There is an apparent naive or brute force solution that runs in O(N²), O(2^N) or some other large time complexity.
- But the biggest giveaway is that the thing you are looking for is often some kind of optimal, like the longest sequence or shortest sequence of something that satisfies a given condition exactly.
And the amazing thing about sliding window problems is that most of the time they can be solved in O(N) time and O(1) space complexity.

### Linked List - Runner Technique


 ##### Find kth node from last in a single linked list.
 - the main problem here is single linked list is unidirectional, so we cannot walk back the list from the end to find kth node.
 - if we know the length of the linkedlist then its easy - find length-kth node. - trivial solution
 - have 2 pointers, move first pointer k positions ahead. then move both the pointers equally, when the first pointer hits the end of the list, the second pointer is in Kth location.
 
