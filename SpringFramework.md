#### Spring Container
In Spring based application, the objects live within a Container.

- There two types of Spring Containers:
  - Bean Factories: Simplest of containers. defined by org.springframework.beans.factory.BeanFactory interface.
  - Application Context: build on the notion of a bean factory by providing application framework services, such as the ability to
resolve textual messages from a properties file and the ability to publish application
events to interested event listeners.

#### Application Context

- ClassPathXmlApplicationContext - Loads a context definition from an XML file located in the classpath.
	``` ApplicationContext context=new ClassPathXmlApplicationContext("foo.xml"); ```
- FileSystemXmlApplicationContext - Loads a context definition from an XML file in the file system.
	``` ApplicationContext context=new FileSystemXmlApplicationContext("c:/foo.xml"); ```
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

Within the <beans> you can place all of your Spring configuration, including <bean> declarations.

- Spring comes with several XML namespaces through which you can configure the Spring container

  - aop  -  Provides elements for declaring aspects and for automatically proxying @AspectJannotated classes as Spring aspects.
  - beans The core primitive Spring namespace, enabling declaration of beans and how they should be wired.
  - context - Comes with elements for configuring the Spring application context, including the ability to autodetect and autowire beans and injection of objects not directly managed by Spring.
  - jee  - Offers integration with Java EE APIs such as JNDI and EJB.
  - jms - Provides configuration elements for declaring message-driven POJOs.
  - lang - Enables declaration of beans that are implemented as Groovy, JRuby, or BeanShell scripts.
  - mvc - Enables Spring MVC capabilities such as annotation-oriented controllers, view control-lers, and interceptors.
