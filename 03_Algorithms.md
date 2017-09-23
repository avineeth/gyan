

# Table of Contents
- [Array Sorting](#sorting)
   - [Selection Sort](#selection-sort)
   - [Insertion Sort](#insertion-sort)
   - [Merge Sort](#merge-sort)
   - [Quick Sort](#quick-sort)
- [Symbol Tables](#symbol-tables)
  - [Binary Search](#binary-search)
  - [Binary Search Tree](#binary-search-tree)
- [Priorty Queues - Binary Heap](#priority-queues)
- [Graph Theory](#graph-theory)
- Graph traversal algorithms
  - [Breadth-first Search](#breadth-first-search)
  - [Depth-first search](#depth-first-search)
  - [Prims Minimum Spanning Tree](#prims-minimum-spanning-tree)
  - [Djikstra's Algorithm - shortest path](#djikstras-algorithm---shortest-path)


# Array Sorting

## Elementary Sorts

### Selection Sort

- Time Complexity 
 - Best - O(n^2)
 - Average - O(n^2)
 - Worst - O(n^2)
- Space Complexity
   - O(1) - in place

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
- Time Complexity 
 - Best - O(n)
 - Average - O(n^2)
 - Worst - O(n^2)
- Space Complexity
   - O(1) - in place

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
- Space complexity : O(n)

[MergeSort.java](../../../algos/blob/master/MergeSort.java)

### Quick Sort

1. Shuffle the array. (optional)
2. Partition so that, for some j
  – entry a[j] is in place
  – no larger entry to the left of j
  – no smaller entry to the right of j
3. Sort each piece recursively.
![Image](https://github.com/avineeth/gyan/blob/master/img/quicksort.PNG?raw=true)
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

## Binary tree
Tree where each node has up to two leaves

  1
 / \
2   3

## Binary search tree
- A binary search tree (BST) is a binary tree where each node has a Comparable key (and an associated value) and satisfies the restriction that the key in any node is larger than the keys in all nodes in that node's left subtree and smaller than the keys in all nodes in that node's right subtree.
 
Used for searching. A binary tree where the left child contains only nodes with values less than the parent node, and where the right child only contains nodes with values greater than or equal to the parent.

![Image](http://algs4.cs.princeton.edu/32bst/images/bst-anatomy.png?raw=true)

##### Insert Operation
- pay attention the recursion is tricky.
```
	public void put(char key, int val) {
		root = put(root, key, val);
	}
	
	private Node put(Node node, char key, int val) {
		
		if(node ==  null) {						
			return  new Node(key, val);
		}
		
		if(key < node.key) {
			node.left = put(node.left, key, val);
		}
		else if(key >= node.key) {
			node.right = put(node.right, key, val);
		}		
		
		return node;
	}

```

##### Search Operation 

```
private Value get(Node node, char key) {
		
		if(node == null) {  //not present
			return null;
		}
		
		if(key < node.key) {  //search left
			return get(node.left, key);
		}
		else if(key > node.key) { //search right
			return get(node.right, key);
		}	
		else  //found
			return node.key;	
	
	}
```
##### Complexity
- Worst Case:
  - Search: N
  - Insert: N
  - Delete: N

- Avg Case:
  - Search: logN (depth of the tree)
  - Insert: logN
  - Delete: sqrt(N)
	

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

