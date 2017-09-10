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

- Within the `<beans>` you can place all of your Spring configuration, including `<bean>` declarations.

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

- Autowiring helps reduce or even eliminate the need for `<property> and <constructor-arg> ` elements by letting Spring automatically figure out how to wire bean dependencies.

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

## Spring AOP
- In software development, functions that span multiple points of an application are called cross-cutting concerns.
- Separating these cross-cutting concerns from the business logic is where aspect-oriented programming (AOP) goes to work.
- AOP helps you decouple cross-cutting concerns from the objects that they affect.
- Practical applications of aspects, includes ** logging, declarative transactions, security, and caching.**

- Cross-cutting concerns can now be modularized into special classes called aspects. 
- This has two benefits. First, the logic for each concern is now in one place, as opposed to being scattered all over the code base.
- Second, our service modules are now cleaner since they only contain code for their primary concern (or core functionality) and second-ary concerns have been moved to aspects.

### AOP Concepts

#### Advice
- Advice defines both the what and the when of an aspect. In addition to describing the job that an aspect will perform, advice addresses the question of when to perform the job.
- Spring aspects can work with five kinds of advice: before, after, after-returning, after-throwing, around.
 
#### Join Points
- A join point is a point in the execution of the application where an aspect can be plugged in. This point is always a method being called in Spring

#### Pointcuts
- Pointcuts help narrow down the join points advised by an aspect.
- If advice defines the what and when of aspects, then pointcuts define the where. 
- A pointcut definition matches one or more join points at which advice should be woven.
- Often you specify these pointcuts using explicit class and method names or through regular expressions that define matching class and method name patterns.

### Aspects
- An aspect is the merger of advice and pointcuts. 
- Taken together, advice and pointcuts define everything there is to know about an aspect—what it does and where and when it does it.

### SPRING ADVISES OBJECTS AT RUNTIME
- In Spring, aspects are woven into Spring-managed beans at runtime by wrapping them with a proxy class. As illustrated in figure 4.3, the proxy class poses as the target bean, intercepting advised method calls and forwarding those calls to the target bean.
- Between the time when the proxy intercepts the method call and the time when it invokes the target bean’s method, the proxy performs the aspect logic.
- Spring doesn’t create a proxied object until that proxied bean is needed by the application. If you’re using an ApplicationContext, the proxied objects will be created when it loads all of the beans from the BeanFactory. Because Spring creates proxies at runtime, you don’t need a special compiler to weave aspects in Spring’s AOP.

### SPRING ONLY SUPPORTS METHOD JOIN POINTS
- As mentioned earlier, multiple join point models are available through various AOP implementations. Because it’s based on dynamic proxies, **Spring only supports method join points.**
- This is in contrast to some other AOP frameworks, such as AspectJ and JBoss, which provide field and constructor join points in addition to method pointcuts.
- Spring’s lack of field pointcuts prevents you from creating very fine-grained advice, such as intercepting updates to an object’s field. And without constructor pointcuts, there’s no way to apply advice when a bean is instantiated.

![Image](https://github.com/avineeth/gyan/blob/master/img/spring_aop.png?raw=true)

#### Wiring Pointcuts

![Image](https://github.com/avineeth/gyan/blob/master/img/pointcut.PNG?raw=true)


#### AOP Configuration


 AOP Config | Purpose
 ---------- | ------------------------------------------------------------
 `<aop:config>` | The top-level AOP element. all AOP elements must be contained within this element.
`<aop:aspect>` | Defines an aspect.
`<aop:pointcut>` | Defines a pointcut.
 `<aop:before>` | The advice functionality takes place before the advised method is invoked.
 `<aop:after>` | The advice functionality takes place after the advised method completes,regardless of the outcome.
 `<aop:after-returning>` |The advice functionality takes place after the advised method successfully completes.
 `<aop:after-throwing>` | The advice functionality takes place after the advised method throws an exception.
 `<aop:around>` |The advice wraps the advised method, providing some functionality before and after the advised method is invoked.
 
#### Example

**Aspect Class**
```
package com.citibank.cfx.trial;
public class Audience {

	public void takeSeats() {
		System.out.println("Take Seats...");
	}
	
	public void clap() {
		System.out.println("Clap...Clap..Clap");
	}
	
}
```
**Spring Config**
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" 
	   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	   xmlns:aop="http://www.springframework.org/schema/aop"
	   xsi:schemaLocation= "http://www.springframework.org/schema/beans
 		http://www.springframework.org/schema/beans/spring-beans.xsd
 		http://www.springframework.org/schema/aop
 		http://www.springframework.org/schema/aop/spring-aop.xsd" >
 
	<bean id="piano" class="com.citibank.cfx.trial.Piano" />
	
	<bean id="audience" class="com.citibank.cfx.trial.Audience" />
	
	 <aop:config>
	 	<aop:aspect ref="audience">
	 		<aop:pointcut id="performance" expression="execution(* com.citibank.cfx.trial.Piano.play(..))" />
	 		<aop:before pointcut-ref="performance" method="takeSeats" />
	 		<aop:after pointcut-ref="performance" method="clap" />
	 	</aop:aspect>
	 </aop:config>
</beans>

```

## Transaction Management

### Explaining transactions in only four words

#### Atomic
Transactions are made up of one or more activities bundled together as a single unit of work. Atomicity ensures that all the operations in the transaction happen or that none of them happen. If all the activities succeed, the transaction is a success. If any of the activities fails, the entire transaction fails and is rolled back.

#### Consistent
Once a transaction ends (whether successful or not), the system is left in a state consistent with the business that it models. The data shouldn’t be corrupted with respect to reality.

#### Isolated
Transactions should allow multiple users to work with the same data, without each user’s work getting tangled up with the others. Therefore, transactions should be isolated from each other, preventing concurrent reads and writes to the same data from occurring. (Note that isolation typically involves locking rows and/or tables in a database.)

#### Durable
Once the transaction has completed, the results of the transaction should be made permanent so that they’ll survive any sort of system crash. This typically involves storing the results in a database or some other form of persistent storage.

### Important Points Regarding Spring's  Transaction Support
- Spring supports for both programmatic and declarative transaction management.
- For programmatic transaction management Spring employs a callback mechanism that abstracts away the actual transaction implementation from the transactional code.
- Choosing between programmatic and declarative transaction management is largely a decision of fine-grained control versus convenience. Typically that is not required.
- If your application uses only a single persistent resource, Spring can use the transactional support offered by the persistence mechanism. This includes JDBC, Hibernate, and the Java Persistence API (JPA).
- But if your application has transaction requirements that span multiple resources, Spring can support distributed (XA) transactions using a thirdparty JTA implementation.


#### Sticky Sessions

When your website is served by only 1 web server, for each pair, a session object is created and remains in the memory of the web server. All the requests from the client go to this web server and update this session object. If some data needs to be stored in the session object over the period of interaction, it is stored in this session object and stays there as long as the session exists.

However, if your website is served by multiple web servers which sit behind a load balancer, the load balancer decides which actual (physical) web-server should each request go to. For example, if there are 3 webservers A, B and C behind the load balancer, it is possible that www.mywebsite.com/index.jsp is served from server A, www.mywebsite.com/login.jsp is served from server B and www.mywebsite.com/accoutdetails.php are served from server C.

Now, if the requests are being served from (physically) 3 different servers, each server has created a session object for you and because these session objects sit on 3 independent boxes, there's no direct way of one knowing what is there in the session object of the other. In order to synchronize between these server sessions, you may have to write/read the session data into a layer which is common to all - like a DB. Now writing and reading data to/from a db for this use-case may not be a good idea. Now, here comes the role of sticky-session. If the load balancer is instructed to use sticky sessions, all of your interactions will happen with the same physical server, even though other servers are present. Thus, your session object will be the same throughout your entire interaction with this website.

To summarize, In case of Sticky Sessions, all your requests will be directed to the same physical web server while in case of a non-sticky loadbalancer may choose any webserver to serve your requests.

As an example, you may read about Amazon's Elastic Load Balancer and sticky sessions here : http://aws.typepad.com/aws/2010/04/new-elastic-load-balancing-feature-sticky-sessions.html




### What is Spring Boot?
Over the years spring has become more and more complex as new functionalities have been added. Just visit the page-https://spring.io/projects and we will see all the spring projects we can use in our application for different functionalities. If one has to start a new spring project we have to add build path or add maven dependencies, configure application server, add spring configuration . So a lot of effort is required to start a new spring project as we have to currently do everything from scratch.
Spring Boot is the solution to this problem. Spring boot has been built on top of existing spring framework. **Using spring boot we avoid all the boilerplate code and configurations that we had to do previously.** Spring boot thus helps us use the existing Spring functionalities more robustly and with minimum efforts.
