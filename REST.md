
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

This tells the client that the request was successfully processed. The most common code is 200 OK. For a GET request, the server sends the resource in the message body. There are other less frequently used codes:
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

Note the request line followed by many request headers. The Host header is mandatory for HTTP/1.1 clients. GET requests do not have a message body, but POST requests can contain the post data in the body.


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
