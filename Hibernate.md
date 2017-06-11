
## What is Hibernate?

- Hibernate is a powerful, high performance object/relational persistence and query service.
- This lets the users to develop persistent classes following object-oriented principles such as association, inheritance, polymorphism, composition, and collections.

## What is ORM?

- ORM stands for Object/Relational mapping. 
- It is the programmed and translucent perseverance of objects in a Java application in to the tables of a relational database using the metadata that describes the mapping between the objects and the database.
- It works by transforming the data from one representation to another.

![Image](https://github.com/avineeth/gyan/blob/master/img/hibernate_data_access_layers.svg?raw=true)

## Advantages of Hibernate

1. Opensource and Lightweight: Hibernate framework is opensource under the LGPL license and lightweight. The GNU Lesser General Public License (LGPL) is a free software license published by the Free Software Foundation (FSF).
2. Fast performance: The performance of hibernate framework is fast because cache is internally used in hibernate framework. There are two types of cache in hibernate framework first level cache and second level cache. First level cache is enabled bydefault.
3. Database Independent query: HQL (Hibernate Query Language) is the object-oriented version of SQL. It generates the database independent queries. So you don't need to write database specific queries. Before Hibernate, If database is changed for the project, we need to change the SQL query as well that leads to the maintenance problem.
4. Automatic table creation: Hibernate framework provides the facility to create the tables of the database automatically. So there is no need to create tables in the database manually.
5. Simplifies complex join: To fetch data form multiple tables is easy in hibernate framework.
6. Provides query statistics and database status: Hibernate supports Query cache and provide statistics about query and database status.


## Hibernate Architecture

![Image](https://github.com/avineeth/gyan/blob/master/img/hibernate-architecture.jpg?raw=true)

### Session Interface (org.hibernate.Session)
- The Session interface is the primary interface used by Hibernate applications.
- An instance of Session is lightweight and is inexpensive to create and destroy. This is important because your application will need to create and destroy sessions all the time, perhaps on every request.
- Hibernate sessions are not thread-safe (Single Threaded) and should by design be used by only one thread at a time.
- The Hibernate notion of a session is something between connection and transaction. It may be easier to think of a session as a cache or collection of loaded objects relating to a single unit of work. Hibernate can detect changes to the objects in this unit of work. We sometimes call the Session a persistence manager because it's also the interface for persistence-related operations such as storing and retrieving objects.


### SessionFactory Interface  (org.hibernate.SessionFactory)
- The application obtains Session instances from a SessionFactory. Compared to the Session interface, this object is much less exciting.
- The SessionFactory is certainly not lightweight! It's intended to be shared among many application threads. 
- There is typically a single SessionFactory for the whole application—created during application initialization, for example. 
- However, if your application accesses multiple databases using Hibernate, you'll need a SessionFactory for each database.
- The SessionFactory caches generate SQL statements and other mapping metadata that Hibernate uses at runtime.
- It also holds cached data that has been read in one unit of work and may be reused in a future unit of work (only if class and collection mappings specify that this second-level cache is desirable).

### Configuration interface
- The Configuration object is used to configure and bootstrap Hibernate.
- The application uses a Configuration instance to specify the location of mapping documents and Hibernate-specific properties and then create the SessionFactory.
- Even though the Configuration interface plays a relatively small part in the total scope of a Hibernate application, it's the first object you'll meet when you begin using Hibernate.


### Transaction interface
- The Transaction interface is an optional API.
- Hibernate applications may choose not to use this interface, instead managing transactions in their own infrastructure code.
- A Transaction abstracts application code from the underlying transaction implementation—which might be a JDBC transaction, a JTA UserTransaction, or even a Common Object Request Broker Architecture (CORBA) transaction—allowing the application to control transaction boundaries via a consistent API. 
- This helps to keep Hibernate applications portable between different kinds of execution environments and containers.


### Query and Criteria interfaces
- The Query interface allows you to perform queries against the database and control how the query is executed. 
- Queries are written in HQL or in the native SQL dialect of your database.
- A Query instance is used to bind query parameters, limit the number of results returned by the query, and finally to execute the query.
- The Criteria interface is very similar; it allows you to create and execute object-oriented criteria queries.
- A Query instance is lightweight and can't be used outside the Session that created it.

### Callback interfaces
- Callback interfaces allow the application to receive a notification when something interesting happens to an object—for example, when an object is loaded, saved, or deleted.
- Hibernate applications don't need to implement these callbacks, but they're useful for implementing certain kinds of generic functionality, such as creating audit records.
- The Interceptor interface was introduced to allow the application to process callbacks without forcing the persistent classes to implement Hibernate-specific APIs. Implementations of the Interceptor interface are passed to the persistent instances as parameters.

### Types
- A fundamental and very powerful element of the architecture is Hibernate's notion of a Type.
- A Hibernate Type object maps a Java type to a database column type (actually, the type may span multiple columns). 
- All persistent properties of persistent classes, including associations, have a corresponding Hibernate type. This design makes Hibernate extremely flexible and extensible.
- There is a rich range of built-in types, covering all Java primitives and many JDK classes, including types for java.util.Currency, java.util.Calendar, byte[], and java.io.Serializable.
- Even better, Hibernate supports user-defined custom types. The interfaces UserType and CompositeUserType are provided to allow you to add your own types. You can use this feature to allow commonly used application classes such as Address, Name, or MonetaryAmount to be handled conveniently and elegantly. 



References:
https://vladmihalcea.com/tutorials/hibernate/
