## Things to keep in mind for Design questions involving large scale systems like Twitter, Netflix

The key here is to understand what your interviewer is looking for. He wants you to give him a 50,000 ft overview, identify high-level components and describe the interactions between components as succinctly as possible. Here are 3 ​phases of such a discussion.
  - Draw a big box that represents the system.
  - Zoom-in and break that big box into 5–6 components.
  - Briefly discuss the role of each component e.g. compute, storage, front-end, back-end, caching, queueing, networking, load-balancing, etc.
  


## Caching

### Application server cache
- Placing a cache directly on a request layer node enables the local storage of response data.
- Each time a request is made to the service, the node will quickly return local, cached data if it exists.
- If it is not in the cache, the requesting node will query the data from disk.
- The cache on one request layer node could also be located both in memory (which is very fast) and on the node’s local disk (faster than going to network storage).

- What happens when you expand this to many nodes? If the request layer is expanded to multiple nodes, it’s still quite possible to have each node host its own cache. However, if your load balancer randomly distributes requests across the nodes, the same request will go to different nodes, thus increasing cache misses. Two choices for overcoming this hurdle are global caches and distributed caches.

### Distributed cache
- In a distributed cache, each of its nodes own part of the cached data.
- Typically, the cache is divided up using a consistent hashing function, such that if a request node is looking for a certain piece of data, it can quickly know where to look within the distributed cache to determine if that data is available.
- In this case, each node has a small piece of the cache, and will then send a request to another node for the data before going to the origin. Therefore, one of the advantages of a distributed cache is the increased cache space that can be had just by adding nodes to the request pool.

- A disadvantage of distributed caching is remedying a missing node. Some distributed caches get around this by storing multiple copies of the data on different nodes; however, you can imagine how this logic can get complicated quickly, especially when you add or remove nodes from the request layer.
- Although even if a node disappears and part of the cache is lost, the requests will just pull from the origin—so it isn’t necessarily catastrophic!

### Global Cache
- A global cache is just as it sounds: all the nodes use the same single cache space. This involves adding a server, or file store of some sort, faster than your original store and accessible by all the request layer nodes.
- Each of the request nodes queries the cache in the same way it would a local one. This kind of caching scheme can get a bit complicated because it is very easy to overwhelm a single cache as the number of clients and requests increase, but is very effective in some architectures (particularly ones with specialized hardware that make this global cache very fast, or that have a fixed dataset that needs to be cached).

There are two common forms of global caches depicted in the following diagram. First, when a cached response is not found in the cache, the cache itself becomes responsible for retrieving the missing piece of data from the underlying store. Second, it is the responsibility of request nodes to retrieve any data that is not found in the cache.


### Cache Invalidation
- While caching is fantastic, it does require some maintenance for keeping cache coherent with the source of truth (e.g., database). If the data is modified in the database, it should be invalidated in the cache, if not, this can cause inconsistent application behavior.
- Solving this problem is known as cache invalidation, there are three main schemes that are used:

##### Write-through cache:
Under this scheme data is written into the cache and the corresponding database at the same time. The cached data allows for fast retrieval, and since the same data gets written in the permanent storage, we will have complete data consistency between cache and storage. Also, this scheme ensures that nothing will get lost in case of a crash, power failure, or other system disruptions.

Although write through minimizes the risk of data loss, since every write operation must be done twice before returning success to the client, this scheme has the disadvantage of higher latency for write operations.

##### Write-around cache:
This technique is similar to write through cache, but data is written directly to permanent storage, bypassing the cache. This can reduce the cache being flooded with write operations that will not subsequently be re-read, but has the disadvantage that a read request for recently written data will create a “cache miss” and must be read from slower back-end storage and experience higher latency.

##### Write-back cache:
Under this scheme, data is written to cache alone, and completion is immediately confirmed to the client. The write to the permanent storage is done after specified intervals or under certain conditions. This results in low latency and high throughput for write-intensive applications, however, this speed comes with the risk of data loss in case of a crash or other adverse event because the only copy of the written data is in the cache.


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

### What IP Addresses Are Private?
- A private IP address is an IP address that's reserved for internal use behind a router or other Network Address Translation (NAT) device, apart from the public.
- The Internet Assigned Numbers Authority (IANA) reserves the following IP address blocks for use as private IP addresses:
  - 10.0.0.0 to 10.255.255.255
  - 172.16.0.0 to 172.31.255.255
  - 192.168.0.0 to 192.168.255.255

### What Is A Domain Name Server (DNS) And How Does It Work

- Domain Name Servers (DNS) are the Internet's equivalent of a phone book. They maintain a directory of domain names and translate them to Internet Protocol (IP) addresses.
- This is necessary because, although domain names are easy for people to remember, computers or machines, access websites based on IP addresses. 
- Information from all the domain name servers across the Internet are gathered together and housed at the Central Registry. Host companies and Internet Service Providers interact with the Central Registry on a regular schedule to get updated DNS information. 
- When you type in a web address, e.g., www.jimsbikes.com, your Internet Service Provider views the DNS associated with the domain name, translates it into a machine friendly IP address (for example 216.168.224.70 is the IP for jimsbikes.com) and directs your Internet connection to the correct website. 
- After you register a new domain name or when you update the DNS servers on your domain name, it usually takes about 12-36 hours for the domain name servers world-wide to be updated and able to access the information. This 36-hour period is referred to as propagation.


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




