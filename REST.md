
- Roy Fielding -  Principal authors of the HTTP specification and the originator of the Representational State Transfer (REST) architectural style.

### HTTP

- HTTP stands for Hypertext Transfer Protocol.
- It's a stateless, application-layer protocol for communicating between distributed systems, and is the foundation of the modern web.
- The communication usually takes place over TCP/IP, but any reliable transport can be used.
- The default port for TCP/IP is 80, but other ports can also be used.
- The current version of the protocol is HTTP/1.1

![Image](https://github.com/avineeth/gyan/blob/master/img/http1-req-res-details.png?raw=true)

### HTTP Connections
- A connection must be established between the client and server before they can communicate with each other, and HTTP uses the reliable TCP transport protocol to make this connection.
- By default, web traffic uses TCP port 80. 
- A TCP stream is broken into IP packets, and it ensures that those packets always arrive in the correct order without fail.
- HTTP is an application layer protocol over TCP, which is over IP.
- HTTPS is a secure version of HTTP, inserting an additional layer between HTTP and TCP called TLS or SSL (Transport Layer Security or Secure Sockets Layer, respectively).
- HTTPS communicates over port 443 by default.

![Image](https://github.com/avineeth/gyan/blob/master/img/http2-http-https.png?raw=true)

#### Difference between TCP and UDP
- TCP is all about reliability —packets sent with TCP are tracked so no data is lost or corrupted in transit. 
- This is why file downloads don’t become corrupted even if there are network hiccups. Of course, if the recipient is completely offline, your computer will give up and you’ll see an error message saying it can’t communicate with the remote host.
- TCP achieves this in two ways. First, it orders packets by numbering them. 
- Second, it error-checks by having the recipient send a response back to the sender saying that it has received the message. If the sender doesn’t get a correct response, it can resend the packets to ensure the recipient receives them correctly.
- The UDP protocol works similarly to TCP, but it throws out all the error-checking stuff.
- All the back-and-forth communication introduce latency, slowing things down.
- When an app uses UDP, packets are just sent to the recipient. The sender doesn’t wait to make sure the recipient received the packet—it just continues sending the next packets. If the recipient misses a few UDP packets here and there, they are just lost—the sender won’t resend them.
- Losing all this overhead means the devices can communicate more quickly.
- UDP is used when speed is desirable and error correction isn’t necessary. 
- For example, UDP is frequently used for live broadcasts and online games.
- For example, let’s say you’re watching a live video stream, which are often broadcast using UDP instead of TCP. The server just sends a constant stream of UDP packets to computers watching. If you lose your connection for a few seconds, the video may freeze or get jumpy for a moment and then skip to the current bit of the broadcast. If you experience minor packet-loss, the video or audio may be distorted for a moment as the video continues to play without the missing data.


### Status Codes
With URLs and verbs, the client can initiate requests to the server. In return, the server responds with status codes and message payloads. The status code is important and tells the client how to interpret the server response. The HTTP spec defines certain number ranges for specific types of responses:

#### 1xx: Informational Messages

All HTTP/1.1 clients are required to accept the Transfer-Encoding header.

This class of codes was introduced in HTTP/1.1 and is purely provisional. The server can send a Expect: 100-continue message, telling the client to continue sending the remainder of the request, or ignore if it has already sent it. HTTP/1.0 clients are supposed to ignore this header.

#### 2xx: Successful

This tells the client that the request was successfully processed. 
- The most common code is 200 OK. For a GET request, the server sends the resource in the message body. The request has succeeded. The information returned with the response is dependent on the method used in the request, for example:
  - GET an entity corresponding to the requested resource is sent in the response;
  - HEAD the entity-header fields corresponding to the requested resource are sent in the response without any message-body;
  - POST an entity describing or containing the result of the action;
  - TRACE an entity containing the request message as received by the end server.
 
- 201 Created - Used for POST requests. The request has been fulfilled and resulted in a new resource being created. The newly created resource can be referenced by the URI(s) returned in the entity of the response, with the most specific URI for the resource given by a Location header field. The response SHOULD include an entity containing a list of resource characteristics and location(s) from which the user or user agent can choose the one most appropriate. The entity format is specified by the media type given in the Content-Type header field. The origin server MUST create the resource before returning the 201 status code. If the action cannot be carried out immediately, the server SHOULD respond with 202 (Accepted) response instead.

There are other less frequently used codes:
- 202 Accepted: the request was accepted but may not include the resource in the response. This is useful for async processing on the server side. The server may choose to send information for monitoring.
- 204 No Content: there is no message body in the response.
- 205 Reset Content: indicates to the client to reset its document view.
- 206 Partial Content: indicates that the response only contains partial content. Additional headers indicate the exact range and content expiration information.


#### 3xx: Redirection

This requires the client to take additional action. The most common use-case is to jump to a different URL in order to fetch the resource.
- 301 Moved Permanently: the resource is now located at a new URL.
- 303 See Other: the resource is temporarily located at a new URL. The Location response header contains the temporary URL.
- 304 Not Modified: the server has determined that the resource has not changed and the client should use its cached copy. This relies on the fact that the client is sending ETag (Enttity Tag) information that is a hash of the content. The server compares this with its own computed ETag to check for modifications.

#### 4xx: Client Error

These codes are used when the server thinks that the client is at fault, either by requesting an invalid resource or making a bad request. The most popular code in this class is 404 Not Found, which I think everyone will identify with. 404 indicates that the resource is invalid and does not exist on the server. The other codes in this class include:
- 404 indicates that the resource is invalid and does not exist on the server.
- 400 Bad Request: the request was malformed.
- 401 Unauthorized: request requires authentication. The client can repeat the request with the Authorization header. If the client already included the Authorization header, then the credentials were wrong.
- 403 Forbidden: server has denied access to the resource.
- 405 Method Not Allowed: invalid HTTP verb used in the request line, or the server does not support that verb.
- 409 Conflict: the server could not complete the request because the client is trying to modify a resource that is newer than the client's timestamp. Conflicts arise mostly for PUT requests during collaborative edits on a resource.

#### 5xx: Server Error

This class of codes are used to indicate a server failure while processing the request. The most commonly used error code is 500 Internal Server Error. The others in this class are:
- 501 Not Implemented: the server does not yet support the requested functionality.
- 503 Service Unavailable: this could happen if an internal system on the server has failed or the server is overloaded. Typically, the server won't even respond and the request will timeout.


### Request and Response Message Formats

Let's now look at the content of these messages. The HTTP specification states that a request or response message has the following generic structure:

```
message = <start-line>
          *(<message-header>)
          CRLF
          [<message-body>]

<start-line> = Request-Line | Status-Line 
<message-header> = Field-Name ':' Field-Value
```

It's mandatory to place a new line between the message headers and body.


### Request Format
The request message has the same generic structure as above, except for the request line which looks like:

```
Request-Line = Method SP URI SP HTTP-Version CRLF
Method = "OPTIONS"
       | "HEAD"  
       | "GET"  
       | "POST"  
       | "PUT"  
       | "DELETE"  
       | "TRACE"
```       
SP is the space separator between the tokens. HTTP-Version is specified as "HTTP/1.1" and then followed by a new line. Thus, a typical request message might look like:

```
GET /articles/http-basics HTTP/1.1
Host: www.articles.com
Connection: keep-alive
Cache-Control: no-cache
Pragma: no-cache
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
```

- Note the request line followed by many request headers. The Host header is mandatory for HTTP/1.1 clients. GET requests do not have a message body, but POST requests can contain the post data in the body.


##### Accept header
The "Accept" header field can be used by user agents to specify response media types that are acceptable.

##### Content-Type 
The "Content-Type" header field indicates the media type of the associated representation

- Accept indicates what kind of response from the server the client can accept. Content-type always is about the content of the current request or response.


### Response Format
The response format is similar to the request message, except for the status line and headers. The status line has the following structure:

```
Status-Line = HTTP-Version SP Status-Code SP Reason-Phrase CRLF
```

- HTTP-Version is sent as HTTP/1.1
- The Status-Code is one of the many statuses discussed earlier.
- The Reason-Phrase is a human-readable version of the status code.

A typical status line for a successful response might look like so:
```
HTTP/1.1 200 OK
```

### What is REST

- R- Representational - Data can be represented in any form (XML, JSON (javascript object notation), even html)
- S- State - We are considered about the state of the resources rather than actions on the data
- T- Transfer - Transfering the data from one application to another (through http)
- ** REST - in short stands for transfering state of a resources from server to client (or vice-versa). **

### Difference between SOAP and REST
0. SOAP and rest cannot be compared directly. SOAP is a protocol but REST is an architectural style.
1. SOAP (RPC) is service oriented but REST is resource oriented
2. SOAP is focused more about actions and verbs.REST is emphasizing more on nouns that describe the application.
3. SOAP is tightly coupled. There is a rigid contract between Client and Server based on WSDL and XML namespaces. anything changes it breaks. REST client is more like a web-browser. 

### REST URLs 
REST URIs - Uniform Resource Identifiers should identify a resource.
Parameters in URLs - http://localhost:9080/Spittles/{id} --> http://localhost:9080/Spittles/123
Here {id} is the parameter. These are supported in Spring by @PathVariable annotation.
```
@RequestMapping(value="/{id}", method = RequestMethod.GET)
public String getSpittle(@PathVariable("id") long id, Model model) {
	return "view";
}
```

#### Resource URIs
```
http://www.example.com/products/{product-id}
```
#### Collection URIs
```
http://www.example.com/products 
```
#### Pluralization
Should URI nodes in your hierarchy be named using singular or plural nouns? For example, should your URI for retrieving a representation of a customer resource look like this:

GET http://www.example.com/customer/33245 or: GET http://www.example.com/customers/33245

There are good arguments on both sides, but the commonly-accepted practice is to always use plurals in node names to keep your API URIs consistent across all HTTP methods. The reasoning is based on the concept that customers are a collection within the service suite and the ID (e.g. 33245) refers to one of those customers in the collection.

### REST Verbs - HTTP Methods
- HTTP Methods are characterized by the two traits:
1. Safe  - A method is safe if it doesnt change the state of resource.
2. Idempotent - repeated requests should have no further side effects after the first resource.

Method | Description | Safe? | Idempotent? 
-------|-------------|-------|-----------
GET | Retrieves resource data from server | yes| yes
POST | Posts data to the server handled by the listening process | no | no
PUT | Puts (updates) the data to the server | no | yes
DELETE | detes the resource at the url | no | yes

### Representing Resources
- Resource can be represented in any form (xml, json, pdf, html).
- Spring provides two ways to transform resource's Java representation into the representation that will be shipped to the client:
  - Negotiated view-based rendenring - ContentNegotiatingViewResovler - similar to MVC 
  - HTTP message converters
  	- bypasses model and view. In this style the object returned from the controller is automatically converted into a represenation appropriate for the client.
	
 
###  HATEOAS

- HATEOAS (Hypermedia as the Engine of Application State) is a constraint of the REST application architecture.
- A hypermedia-driven site provides information to navigate the site's REST interfaces dynamically by including hypermedia links with the responses.
- This capability differs from that of SOA-based systems and WSDL-driven interfaces. With SOA, servers and clients usually must access a fixed specification that might be staged somewhere else on the website, on another website, or perhaps distributed by email.

The following code represents a Customer object.
```
class Customer {
    String name;
}
```
A simple JSON presentation is traditionally rendered as: (The customer data is there, but the data contains nothing about its relevant links.)

```
{ 
    "name" : "Alice"
}
```
- A HATEOAS-based response would look like this:
```
{
    "name": "Alice",
    "links": [ {
        "rel": "self",
        "href": "http://localhost:8080/customer/1"
    } ]
}
```
This response not only has the person's name, but includes the self-linking URL where that person is located.

  - **rel** means relationship. In this case, it's a self-referencing hyperlink. More complex systems might include other relationships. For example, an order might have a "rel":"customer" relationship, linking the order to its customer.
  - **href**  is a complete URL that uniquely defines the resource.

####  The Richardson Maturity Model

- Richardson Maturity Model defines the maturity level of a Restful Web Service. Following are the different levels and their characteristics.

- Level 0 : Expose SOAP web services in REST style. Expose action based services (http://server/getPosts, http://server/deletePosts, http://server/doThis, http://server/doThat etc) using REST.
- Level 1 : Expose Resources with proper URI’s (using nouns). Ex: http://server/accounts, http://server/accounts/10. However, HTTP Methods are not used.
- Level 2 : Resources use proper URI's + HTTP Methods. For example, to update an account, you do a PUT to . The create an account, you do a POST to . Uri’s look like posts/1/comments/5 and accounts/1/friends/1.
- Level 3 : HATEOAS (Hypermedia as the engine of application state). You will tell not only about the information being requested but also about the next possible actions that the service consumer can do. When requesting information about a facebook user, a REST service can return user details along with information about how to get his recent posts, how to get his recent comments and how to retrieve his friend’s list.

### JAX-RS

- JAX-RS: Java API for RESTful Web Services (JAX-RS) is a Java programming language API spec that provides support in creating web services according to the Representational State Transfer (REST) architectural pattern.
- JAX-RS uses annotations, introduced in Java SE 5, to simplify the development and deployment of web service clients and endpoints.

- Implementations
  - Jersey, the reference implementation from Sun (now Oracle)
  - RESTeasy, JBoss's implementation
  - Apache CXF, an open source Web service framework

### Writing REST Clients 
- Using REST Template

```
public class Application {

    private static final Logger log = LoggerFactory.getLogger(Application.class);

    public static void main(String args[]) {
        RestTemplate restTemplate = new RestTemplate();
        Quote quote = restTemplate.getForObject("http://gturnquist-quoters.cfapps.io/api/random", Quote.class);
        log.info(quote.toString());
    }
```
