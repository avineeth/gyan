
# CODING CHECKLIST

#### To print an array
```
import java.util.Arrays;
System.out.println(Arrays.toString(int[] a));
```  
Returns a string representation of the contents of the specified array.

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
  
