
### Difference between Web Server and Application Server

- Most of the times these terms Web Server and Application server are used interchangeably.
- Following are some of the key differences in features of Web Server and Application Server:
1. Web Server is designed to serve HTTP Content. App Server can also serve HTTP Content but is not limited to just HTTP. It can be provided other protocol support such as RMI/RPC
2. Web Server is mostly designed to serve static content, though most Web Servers have plugins to support scripting languages like Perl, PHP, ASP, JSP etc. through which these servers can generate dynamic HTTP content.
3. Most of the application servers have Web Server as integral part of them, that means App Server can do whatever Web Server is capable of. Additionally App Server have components and features to support Application level services such as Connection Pooling, Object Pooling, Transaction Support, Messaging services etc.
4. As web servers are well suited for static content and app servers for dynamic content, most of the production environments have web server acting as reverse proxy to app server. That means while servicing a page request, static contents (such as images/Static HTML) are served by web server that interprets the request. Using some kind of filtering technique (mostly extension of requested resource) web server identifies dynamic content request and transparently forwards to app server
5. Example of such configuration is Apache Tomcat HTTP Server and Oracle (formerly BEA) WebLogic Server. Apache Tomcat HTTP Server is Web Server and Oracle WebLogic is Application Server.
6. In some cases the servers are tightly integrated such as IIS and .NET Runtime. IIS is web server. When equipped with .NET runtime environment, IIS is capable of providing application services.



### Web Profile
![Image](https://github.com/avineeth/gyan/blob/master/img/webprofile.jpg?raw=true)

### WEB-INF
A special directory exists within the application hierarchy named  WEB-INF. This directory contains all things related to the application that aren’t in the document root of the application. The  WEB-INF node is not part of the public document tree of the application. **No file contained in the WEB-INF directory may be served directly to a client by the container**. However, the contents of the  WEB-INF directory are visible to servlet code using the getResource and getResourceAsStream method calls on the ServletContext, and may be exposed using the RequestDispatcher calls.


### JAX WS
The Java API for XML Web Services (JAX-WS) is a Java programming language API for creating web services, particularly SOAP services. JAX-WS is one of the Java XML programming APIs. It is part of the Java EE platform.

Implementations
1. Apache CXF
2. Apache Axis2
3. JAX-WS Reference Implementation (RI) Project. Metro Project - Glassfish

The starting point for developing a JAX-WS web service is a Java class annotated with the javax.jws.WebService annotation. The @WebService annotation defines the class as a web service endpoint.



### Structure of a WSDL Document

Web Services Description Language (WSDL)

![Image](https://github.com/avineeth/gyan/blob/master/img/wsdl.gif?raw=true)


#### Service:

```
<service name="ProductService">
  <port name="ProductPort" binding="tns:ProductPortBinding">
    <soap:address location="http://vineeth-pc:8080/TestMart/ProductService"/>
  </port>
</service>
```


#### Binding
```
<binding type="tns:Product" name="ProductPortBinding">
  <soap:binding style="document" transport="http://schemas.xmlsoap.org/soap/http"/>
  <operation name="getProductList">
    <soap:operation soapAction="getProductList"/>  <!-- SoapAction Set by @WebMethod(action="") -->
    <input>
      <soap:body use="literal"/>
    </input>
    <output>
      <soap:body use="literal"/>
    </output>
  </operation>
</binding>
```

#### PortType
```
<portType name="Product">
  <operation name="getProductList">
    <input message="tns:getProductList" wsam:Action="http://testmart.com/Product/getProductListRequest"/>
    <output message="tns:getProductListResponse" wsam:Action="http://testmart.com/Product/getProductListResponse"/>
  </operation>
  
  <operation name="getProduct">
    <input message="tns:getProduct" wsam:Action="getProduct"/>
    <output message="tns:getProductResponse" wsam:Action="http://testmart.com/Product/getProductResponse"/>
  </operation>
  
  <operation name="addProduct">
    <input message="tns:addProduct" wsam:Action="addProduct"/>
    <output message="tns:addProductResponse" wsam:Action="http://testmart.com/Product/addProductResponse"/>
  </operation>

</portType>
```

### SOAP Binding

- Two styles - RPC and Document
- Document - (default) provides a xsd to validat
- RPC


## Stateless

Web Services are stateless by default because of the underlying HTTP protocol. The server processes each web service request as a new interaction even though it is from the same client

Normally, a JAX-WS Web service is stateless: that is, none of the local variables and object values that you set in the Web service object are saved from one invocation to the next. Even sequential requests from a single client are treated each as independent, stateless method invocations.

There are Web service use cases where a client may want to save data on the service during one invocation and then use that data during a subsequent invocation. For example, a shopping cart object may be added to by repeated calls to the addToCart web method and then fetched by the getCart web method. In a stateless Web service, the shopping cart object would always be empty, no matter how many addToCart methods were called. But by using HTTP Sessions to maintain state across Web service invocations, the cart may be built up incrementally, and then returned to the client.

Enabling stateful support in a JAX-WS Web service requires a minimal amount of coding on both the client and server.



### Sessions, a Pitfall.

http://www.beabetterdeveloper.com/2013/11/sessions-pitfall.htm

I got asked quite often, why one shouldn't use sessions and whats so bad about them. In this post i want to sum up my thoughts and experiences.

##### Sessions produce unnecessary complexity
The term bugfoot is a combination of bug and bigfoot. It's used for a bug that appears only once and is not reproducable. A bugfoot tells you that something is wrong, and that it will probably come up again. But you cannot fix it, because you don't know the cause.

If you want bugfoots in your software, just use sessions. Sooner or later you'll happen to see one.

##### Sessions don't scale
Lets say you are a professional chess player, and you'd like to play multiple people at the same time. If you'd try to remember every game and your strategy on it, you'll hit your capacity rather quick. Now imagine you were not remembering anything of those games, and you were just rereading the chessboard on every move. You could literally play 1.000.000 people at the same time, and it wouldn't make any difference to you. 

Now draw an analogy to your server. If your application gets a lot of load, you might have to distribute it to different servers. If you were using sessions, you'd suddenly had to replicate all sessions to all servers. The system would become even more complex and error prone.

##### Sessionstate cannot be bookmarkable nor cachable
Did you try to bookmark your shopping cart? Nah, of course you can't because it's empty as soon as the session runs out. Imagine a shop that actually allowed you to bookmark your cart. Like
http://example.com/carts/1337 or
http://example.com/cart?products=[{id:1, amount: 1}]
How wonderful that would be.

##### Conclusion
You want a simple system that is easy to test and whose bugs are easy to reproduce. State leads to the opposite. HTTP was never ever ment to be stateful and it should have stayed this way. 

##### If you want some rest, follow these two rules
Keep all application state (not resource state) on the client
Keep all shared state (not session state) on the server


### Things you should know about POJOs annotated with @WebService

1. A POJO annotated with @WebService deployed in a WAR is served by the Web Container (this is significant)
2. Life cycle – a single instance of the POJO serves requests from the client. Pretty much like Servlets.
3. Concurrency aspect (thread safety) – they are not thread safe. The same instance of the bean can be concurrently accessed by multiple threads. Although this is not a problem if your service is stateless i.e. does not use instance variables to store state – you might still face scalability issues though (after all, its just one instance !). If your POJO uses instance variables to store state, then you are in big soup and will definitely face issues w.r.t inconsistent behavior resulting from concurrent threads accessing a single instance of your web service class.
