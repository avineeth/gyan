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



## Things to keep in mind for Design questions involving large scale systems like Twitter, Netflix

The key here is to understand what your interviewer is looking for. He wants you to give him a 50,000 ft overview, identify high-level components and describe the interactions between components as succinctly as possible. Here are 3 ​phases of such a discussion.
  - Draw a big box that represents the system.
  - Zoom-in and break that big box into 5–6 components.
  - Briefly discuss the role of each component e.g. compute, storage, front-end, back-end, caching, queueing, networking, load-balancing, etc.
