#### Spring Container
In Spring based application, the objects live within a Container.

- There two types of Spring Containers:
  - Bean Factories: Simplest of containers. defined by org.springframework.beans.factory.BeanFactory interface.
  - Application Context: build on the notion of a bean factory by providing application framework services, such as the ability to
resolve textual messages from a properties file and the ability to publish application
events to interested event listeners.

#### Application Context

- ClassPathXmlApplicationContext - Loads a context definition from an XML file located in the classpath.
	``` 
	ApplicationContext context=new ClassPathXmlApplicationContext("foo.xml"); 
	```
- FileSystemXmlApplicationContext - Loads a context definition from an XML file in the file system.
	``` 
	ApplicationContext context=new FileSystemXmlApplicationContext("c:/foo.xml");
	```
- XmlWebApplicationContext - Loads context definitions from an XML file contained within a web application.

- The difference between using FileSystemXmlApplicationContext and ClassPathXmlApplicationContext is that FileSystemXmlApplicationContext will look for foo.xml in a specific location within the file system, whereas ClassPathXmlApplicationContext will look for foo.xml anywhere in the classpath (including JAR files).

#### Spring Configuration 

- A typical Spring configuration XML file looks like this:
```
<?xml version="1.0"encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

<!-- Beandeclarationsgohere-->
</beans>
```

- Within the <beans> you can place all of your Spring configuration, including <bean> declarations.

- Spring comes with several XML namespaces through which you can configure the Spring container

  - **aop**  -  Provides elements for declaring aspects and for automatically proxying @AspectJannotated classes as Spring aspects.
  - **beans** The core primitive Spring namespace, enabling declaration of beans and how they should be wired.
  - **context** - Comes with elements for configuring the Spring application context, including the ability to autodetect and autowire beans and injection of objects not directly managed by Spring.
  - **jee**  - Offers integration with Java EE APIs such as JNDI and EJB.
  - **jms** - Provides configuration elements for declaring message-driven POJOs.
  - **lang** - Enables declaration of beans that are implemented as Groovy, JRuby, or BeanShell scripts.
  - **mvc** - Enables Spring MVC capabilities such as annotation-oriented controllers, view control-lers, and interceptors.


#### Bean Scope 

- Spring’s bean scopes let you declare the scope under which beans are created without hard-
coding the scoping rules in the bean class itself.

  - **singleton** - Scopes the bean definition to a single instance per Spring container (default).
  - **prototype** - Allows a bean to be instantiated any number of times (once per use).
  - **request** - Scopes a bean definition to an HTTP request. Only valid when used with a web-capable Spring context (such as with Spring MVC).
  - **session** - Scopes a bean definition to an HTTP session. Only valid when used with a web-capable Spring context (such as with Spring MVC).
  - **global-session** - Scopes a bean definition to a global HTTP session. Only valid when used in a portlet context.

#### Spring Bean vs Java Bean vs POJOs

- Java Beans: At a basic level, JavaBeans are simply Java classes which adhere to certain coding conventions. For example, classes that
  - Have a public default (no argument) constructor
  - allows access to properties using accessor (getter and setter) methods
  - Implement java.io.Serializable

- Spring Beans:
  - A Spring bean is basically an object managed by Spring. 
  - More specifically, it is an object that is instantiated, configured and otherwise "managed by a Spring Framework container".
  - Spring beans are defined in a Spring configuration file (or, more recently, by using annotations), instantiated by the Spring container, and then injected into your application.

- Note that Spring Beans need not to be Java Beans always. Spring Beans may not implement java.io.Serializable Interface, can have arguments in constructor etc.

#### Minimalizing XML Configuration

- Autowiring helps reduce or even eliminate the need for <property> and <constructor-arg> elements by letting Spring automatically figure out how to wire bean dependencies.

- The four kinds of autowiring
  - byName — Attempts to match all properties of the autowired bean with beans that have the same name (or ID) as the properties. Properties for which there’s no matching bean will remain unwired. 
  - byType — Attempts to match all properties of the autowired bean with beans whose types are assignable to the properties. Properties for which there’s no matching bean will remain unwired.  	
  - constructor — Tries to match up a constructor of the autowired bean with beans whose types are assignable to the constructor arguments. 
  - autodetect — Attempts to apply constructor autowiring first. If that fails, byType will be tried.
 
Before
```
  <bean id="kenny" class ="com.springaction.springidol.Instrumentalist" >
        <property name="song" value="hey there" />
	<property name="instrument" ref="piano" />
  </bean>
 
  <bean id="instrument" class="com.springaction.springidol.Violin" />
```
After
```
     <bean id="kenny" class ="com.springaction.springidol.Instrumentalist" autowire="byName" >
        <property name="song" value="hey there" />
    </bean>
    
    <bean id="instrument" class="com.springaction.springidol.Violin" />
``` 
  
#### Wiring with Annotation

- Annotation wiring isn’t turned on in the Spring container by default.
- The simplest way to do that is with the ``` <context:annotation-config> ``` element from Spring’s context configuration namespace.

```
<?xml version="1.0"encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
		http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
		http://www.springframework.org/schema/context
		http://www.springframework.org/schema/context/spring-context-3.0.xsd">
<context:annotation-config/>
<!-- beandeclarationsgohere-->
</beans>
```
#### Sticky Sessions

When your website is served by only 1 web server, for each pair, a session object is created and remains in the memory of the web server. All the requests from the client go to this web server and update this session object. If some data needs to be stored in the session object over the period of interaction, it is stored in this session object and stays there as long as the session exists.

However, if your website is served by multiple web servers which sit behind a load balancer, the load balancer decides which actual (physical) web-server should each request go to. For example, if there are 3 webservers A, B and C behind the load balancer, it is possible that www.mywebsite.com/index.jsp is served from server A, www.mywebsite.com/login.jsp is served from server B and www.mywebsite.com/accoutdetails.php are served from server C.

Now, if the requests are being served from (physically) 3 different servers, each server has created a session object for you and because these session objects sit on 3 independent boxes, there's no direct way of one knowing what is there in the session object of the other. In order to synchronize between these server sessions, you may have to write/read the session data into a layer which is common to all - like a DB. Now writing and reading data to/from a db for this use-case may not be a good idea. Now, here comes the role of sticky-session. If the load balancer is instructed to use sticky sessions, all of your interactions will happen with the same physical server, even though other servers are present. Thus, your session object will be the same throughout your entire interaction with this website.

To summarize, In case of Sticky Sessions, all your requests will be directed to the same physical web server while in case of a non-sticky loadbalancer may choose any webserver to serve your requests.

As an example, you may read about Amazon's Elastic Load Balancer and sticky sessions here : http://aws.typepad.com/aws/2010/04/new-elastic-load-balancing-feature-sticky-sessions.html


## REST API

####  HATEOAS

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