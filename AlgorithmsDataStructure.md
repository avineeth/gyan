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

