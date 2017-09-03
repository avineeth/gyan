
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
