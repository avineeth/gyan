### LRU Cache Implementation
- How to implement LRU caching scheme? What data structures should be used?
- We are given total possible page numbers that can be referred. We are also given cache (or memory) size (Number of page frames that cache can hold at a time). The LRU caching scheme is to remove the least recently used frame when the cache is full and a new page is referenced which is not there in cache. Please see the Galvin book for more details (see the LRU page replacement slide here).

- We use two data structures to implement an LRU Cache.
- Queue which is implemented using a doubly linked list. The maximum size of the queue will be equal to the total number of frames available (cache size).The most recently used pages will be near front end and least recently pages will be near rear end.
- A Hash with page number as key and address of the corresponding queue node as value.
- When a page is referenced, the required page may be in the memory. If it is in the memory, we need to detach the node of the list and bring it to the front of the queue.
- If the required page is not in the memory, we bring that in memory. In simple words, we add a new node to the front of the queue and update the corresponding node address in the hash. If the queue is full, i.e. all the frames are full, we remove a node from the rear of queue, and add the new node to the front of queue.


### Content delivery network - AKAMAI

A content delivery network or content distribution network (CDN) is a geographically distributed network of proxy servers and their data centers. The goal is to distribute service spatially relative to end-users to provide high availability and high performance. CDNs serve a large portion of the Internet content today, including web objects (text, graphics and scripts), downloadable objects (media files, software, documents), applications (e-commerce, portals), live streaming media, on-demand streaming media, and social networks.


### TinyURL

The main challenge here is to convert a URL string to shorter string.

#### How to convert the ID to a shortened URL
- Think of an alphabet we want to use. In your case that's [a-zA-Z0-9]. It contains 62 letters.
- Take an auto-generated, unique numerical key (the auto-incremented id of a MySQL table for example).

For this example I will use 12510 (125 with a base of 10).
Now you have to convert 12510 to X62 (base 62).
```
12510 = 2×621 + 1×620 = [2,1]
```
This requires use of integer division and modulo. A pseudo-code example:
```
digits = []

while num > 0
  remainder = modulo(num, 62)
  digits.push(remainder)
  num = divide(num, 62)

digits = digits.reverse
```
Now map the indices 2 and 1 to your alphabet. This is how your mapping (with an array for example) could look like:
```
0  → a
1  → b
...
25 → z
...
52 → 0
61 → 9
```
With 2 → c and 1 → b you will receive cb62 as the shortened URL.
http://shor.ty/cb
#### How to resolve a shortened URL to the initial ID

The reverse is even easier. You just do a reverse lookup in your alphabet.

- e9a62 will be resolved to "4th, 61st, and 0th letter in alphabet".
```
e9a62 = [4,61,0] = 4×622 + 61×621 + 0×620 = 1915810
```
Now find your database-record with WHERE id = 19158 and do the redirect.
