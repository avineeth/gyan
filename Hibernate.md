
## A JDBC Example

```
package com.citibank.cfx.reports;

import java.sql.Connection;  // <-- ALL CLASSES ARE IN java.sql.*
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;
import java.sql.ResultSet;

public class DBConn {
	
	private static final String URL = "jdbc:oracle:thin:@localhost:13100:INFR";
	private static final String DB_USER = "user";
	private static final String DB_PASSWORD = "pass";
	
	public static void main(String[] args) {
		
		Connection conn = null;
		Statement stmt  = null;
		ResultSet rs  = null;
		try {
			//1. load driver class
			Class.forName("oracle.jdbc.driver.OracleDriver"); // oracle driver in ojdbc.jar
			
			//2. create connection
			conn = DriverManager.getConnection(URL, DB_USER, DB_PASSWORD);
			
			//3. create statement object
			stmt = conn.createStatement();
			
			//4. execute query
			rs = stmt.executeQuery("Select * from EMPLOYEE");
			while(rs.next()){
				System.out.println(rs.getInt(1));
				System.out.println(rs.getString(2));
				System.out.println(rs.getString("firstname"));
				System.out.println("-------------");
			}
		}catch(ClassNotFoundException ce) {
			ce.printStackTrace();
		}catch(SQLException sq){
			sq.printStackTrace();
		}finally {
			try{
				//5. close connection objects
				if(rs !=null)
					rs.close();
				if(stmt !=null) 
					stmt.close();
				if(conn !=null)
					conn.close();
			}catch(SQLException sq) {
				//Do nothing
			}
		}
		
	}
}
```

## What is Hibernate?

- Hibernate is a powerful, high performance object/relational persistence and query service.
- This lets the users to develop persistent classes following object-oriented principles such as association, inheritance, polymorphism, composition, and collections.

## What is ORM?

- ORM stands for Object/Relational mapping. 
- It is the programmed and translucent perseverance of objects in a Java application in to the tables of a relational database using the metadata that describes the mapping between the objects and the database.
- It works by transforming the data from one representation to another.

![Image](https://github.com/avineeth/gyan/blob/master/img/hibernate_data_access_layers.png?raw=true)

## Advantages of Hibernate

1. Opensource and Lightweight: Hibernate framework is opensource under the LGPL license and lightweight. The GNU Lesser General Public License (LGPL) is a free software license published by the Free Software Foundation (FSF).
2. Fast performance: The performance of hibernate framework is fast because cache is internally used in hibernate framework. There are two types of cache in hibernate framework first level cache and second level cache. First level cache is enabled bydefault.
3. Database Independent query: HQL (Hibernate Query Language) is the object-oriented version of SQL. It generates the database independent queries. So you don't need to write database specific queries. Before Hibernate, If database is changed for the project, we need to change the SQL query as well that leads to the maintenance problem.
4. Automatic table creation: Hibernate framework provides the facility to create the tables of the database automatically. So there is no need to create tables in the database manually.
5. Simplifies complex join: To fetch data form multiple tables is easy in hibernate framework.
6. Provides query statistics and database status: Hibernate supports Query cache and provide statistics about query and database status.

## Relation with JPA
- JPA (Java Persistence API) is an interface for persistence providers to implement.
- Hibernate is one such implementation of JPA.
- You can annotate your classes as much as you would like with JPA annotations, however without an implementation nothing will happen. -- Think of JPA as the guidelines that must be followed or an interface, while Hibernates JPA implementation is code that meets the API as defined by JPA and provides the under the hood functionality.
- When you use hibernate with JPA you are actually using the Hibernate JPA implementation. The benefit of this is that you can swap out hibernates implementation of JPA for another implementation of the JPA specification. When you use straight hibernate your locking into the implementation because other ORMs may use different methods/configurations and annotations, therefore you cannot just switch over to another ORM.

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

### Extension interfaces

- Much of the functionality that Hibernate provides is configurable, allowing you to choose between certain built-in strategies.
- When the built-in strategies are insufficient, Hibernate will usually let you plug in your own custom implementation by implementing an interface.
- Extension points include:
  1. Primary key generation (IdentifierGenerator interface)
  2. SQL dialect support (Dialect abstract class)
  3. Caching strategies (Cache and CacheProvider interfaces)
  4. JDBC connection management (ConnectionProvider interface)
  5. Transaction management (TransactionFactory, Transaction, and TransactionManagerLookup interfaces)
  6. ORM strategies (ClassPersister interface hierarchy)
  7. Property access strategies (PropertyAccessor interface)
  8. Proxy creation (ProxyFactory interface)

### Hibernate Configuration

It's important to understand the difference in configuring Hibernate for managed and nonmanaged environments:

##### Managed environment
- Pools resources such as database connections and allows transaction boundaries and security to be specified declaratively (that is, in metadata).
- A J2EE application server such as JBoss, BEA WebLogic, or IBM WebSphere implements the standard (J2EE-specific) managed environment for Java.
##### Nonmanaged environment
- Provides basic concurrency management via thread pooling. A servlet container like Jetty or Tomcat provides a nonmanaged server environment for Java Web applications.
- A standalone desktop or command line application is also considered nonmanaged.
- Nonmanaged environments don't provide automatic transaction or resource management or security infrastructure. The application itself manages database connections and demarcates transaction boundaries.


### Hibernate/JPA2 Annotations
- The JPA 2 standard annotations are contained in the ** javax.persistence ** package. 

#### Entity
- The @Entity annotation marks this class as an entity bean.
- It must have a no-argument constructor that is visible with at least protected scope (JPA specific). 
- POJO class must not be final; and it must not be abstract as well.
```
import javax.persistence.Entity;
 
@Entity
public class EmployeeEntity implements Serializable
{
    public EmployeeEntity(){
 
    }
    //Other code
}
```

#### Primary Keys with @Id and @GeneratedValue
- Each entity bean has to have a primary key, which you annotate on the class with the @Id annotation. 
- By default, the @Id annotation will not create a primary key generation strategy, which means that you, as the code’s author, need to determine what valid primary keys are, by setting them explicitly calling setter methods. OR you can use @GeneratedValue annotation.

```
@Id
@GeneratedValue (strategy = GenerationType.SEQUENCE)
private Integer employeeId;
```

- There are four different types of primary key generators on GeneratorType, as follows:
  1. AUTO: Hibernate decides which generator type to use, based on the database’s support for primary key generation.
  2. IDENTITY: The database is responsible for determining and assigning the next primary key.
  3. SEQUENCE: Some databases support a SEQUENCE column type. It uses @SequenceGenerator.
  4. TABLE: This type keeps a separate table with the primary key values. It uses @TableGenerator.

#### @Table annotation.
- This annotation allows you to specify many of the details of the table that will be used to persist the entity in the database. As already pointed out, if you omit the annotation, Hibernate will default to using the class name for the table name, so you need only provide this annotation if you want to override that behavior. 
- The @Table annotation provides four attributes, allowing you to override the name of the table, its catalog, and its schema, and to enforce unique constraints on columns in the table. 
 
#### The @Column annotation
- is used to specify the details of the column to which a field or property will be mapped. 
- The following attributes commonly being overridden:
  - name : permits the name of the column to be explicitly specified—by default, this would be the name of the property.
  - length : permits the size of the column used to map a value (particularly a String value) to be explicitly defined. The column size defaults to 255, which might otherwise result in truncated String data, for example.
  - nullable : permits the column to be marked NOT NULL when the schema is generated. The default is that fields should be permitted to be null; however, it is common to override this when a field is, or ought to be, mandatory.
  - unique : permits the column to be marked as containing only unique values. This defaults to false, but commonly would be set for a value that might not be a primary key but would still cause problems if duplicated (such as username).
```
@Column(name="FNAME",length=100,nullable=false)
private String  firstName;
```


### Entity States
Given an instance of an object that is mapped to Hibernate, it can be in any one of four different states: transient, persistent, detached, or removed.

- Newly created POJO object will be in the **transient state**. Transient object doesn’t represent any row of the database i.e. not associated with any session object. It’s plain simple java object.
- **Persistent object** represent one row of the database and always associated with some unique hibernate session. Changes to persistent objects are tracked by hibernate and are saved into database when commit call happen.
- **Detached objects** are those who were once persistent in past, and now they are no longer persistent. To persist changes done in detached objects, you must reattach them to hibernate session.
- **Removed objects** are persistent objects that have been passed to the session’s remove() method and soon will be deleted as soon as changes held in the session will be committed to database.


refresh() Method


#### Understanding Entities and Their Associations

- Entities can contain references to other entities, either directly as an embedded property or field, or indirectly via a collection of some sort (arrays, sets, lists, etc.).
- These associations are represented using ** foreign key** relationships in the underlying tables. These foreign keys will rely on the identifiers used by participating tables.
- When only one of the pair of entities contains a reference to the other, the association is unidirectional.
- If the association is mutual, then it is referred to as bidirectional.
- In hibernate mapping associations, one (and only one) of the participating classes is referred to as “managing the relationship” and other one is called “managed by” using ‘mappedBy’ property.
= We should not make both ends of association “managing the relationship”. Never do it.
- Note that “mappedBy” is purely about how the foreign key relationships between entities are saved. It has nothing to do with saving the entities themselves using cascade functionality.

### TYPE OF ASSOCIATION
- One-to-one	Either end can be made the owner, but one (and only one) of them should be; if you don’t specify this, you will end up with a circular dependency.
- One-to-many	The many end must be made the owner of the association.
- Many-to-one	This is the same as the one-to-many relationship viewed from the opposite perspective, so the same rule applies: the many end must be made the owner of the association.
- Many-to-many	Either end of the association can be made the owner.

### @JoinColumn and mappedBy
- The annotation @JoinColumn indicates that this entity is the owner of the relationship (that is: the corresponding table has a column with a foreign key to the referenced table), whereas the attribute mappedBy indicates that the entity in this side is the inverse of the relationship, and the owner resides in the "other" entity.
- This also means that you can access the other table from the class which you've annotated with "mappedBy" (fully bidirectional relationship).
- In particular, for the code in the question the correct annotations would look like this:
```
@Entity
public class Company {
    @OneToMany(fetch = FetchType.LAZY, mappedBy = "company")
    private List<Branch> branches;
}

@Entity
public class Branch {
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "companyId")
    private Company company;
}
```

### JPA Cascade Types

The cascade types supported by the Java Persistence Architecture are as below:

1. CascadeType.PERSIST : means that save() or persist() operations cascade to related entities.
2. CascadeType.MERGE : means that related entities are merged when the owning entity is merged.
3. CascadeType.REFRESH : does the same thing for the refresh() operation.
4. CascadeType.REMOVE : removes all related entities association with this setting when the owning entity is deleted.
5. CascadeType.DETACH : detaches all related entities if a “manual detach” occurs.
6. CascadeType.ALL : is shorthand for all of the above cascade operations.

### Criteira (where clause)
- The simplest example of a criteria query is one with no optional parameters or restrictions—the criteria query will simply return every object that corresponds to the class.

```
Criteria crit = session.createCriteria(Product.class);
List<Product> results = crit.list();
```

- The Criteria API makes it easy to use restrictions in your queries to selectively retrieve objects; for instance, your application could retrieve only products with a price over $30. You may add these restrictions to a Criteria object with the add() method. The add() method takes an org.hibernate.criterion.Criterion object that represents an individual restriction. You can have more than one restriction for a criteria query.

- Restrictions.eq() Example
To retrieve objects that have a property value that “equals” your restriction, use the eq() method on Restrictions, as follows:
```
Criteria crit = session.createCriteria(Product.class);
crit.add(Restrictions.eq("description","Mouse"));
List<Product> results = crit.list()
```
Above query will search all products having description as “Mouse”.


### Lazy Loading

- The default behavior is to load ‘property values eagerly’ and to load ‘collections lazily’. 
- Also note that @OneToMany and @ManyToMany associations are defaulted to LAZY loading; and @OneToOne and @ManyToOne are defaulted to EAGER loading. This is important to remember to avoid any pitfall in future.

#### How Lazy Loading Works in Hibernate

The simplest way that Hibernate can apply lazy load behavior upon your entities and associations is by providing a proxy implementation of them. Hibernate intercepts calls to the entity by substituting a proxy for it derived from the entity’s class. Where the requested information is missing, it will be loaded from the database before control is ceded to the parent entity’s implementation.


#### Annotation Type Temporal
- This annotation must be specified for persistent fields or properties of type java.util.Date and java.util.Calendar. It may only be specified for fields or properties of these types.
- The Temporal annotation may be used in conjunction with the Basic annotation, the Id annotation, or the ElementCollection annotation (when the element collection value is of such a temporal type.
g. java.util.Date it's not obvious if one wants to map to DATE or TIMESTAMP database type. Only exception is java.sql.Date/Time.

```
@Temporal(DATE)
protected java.util.Date endDate;
```
### Removing cache objects from first level cache example

- Though we can not disable the first level cache in hibernate, but we can certainly remove some of objects from it when needed. This is done using two methods :
1. evict()
2. clear()
- Here evict() is used to remove a particular object from cache associated with session, and clear() method is used to remove all cached objects associated with session. So they are essentially like remove one and remove all.

http://www.byteslounge.com/tutorials/jpa-entity-versioning-version-and-optimistic-locking

### Second level Cache
- First level cache: This is enabled by default and works in session scope. Read more about hibernate first level cache.
- Second level cache: This is apart from first level cache which is available to be used globally in session factory scope.
- Above statement means, second level cache is created in session factory scope and is available to be used in all sessions which are created using that particular session factory.
- It also means that once session factory is closed, all cache associated with it die and cache manager also closed down.

#### How second level cache works

1. Whenever hibernate session try to load an entity, the very first place it look for cached copy of entity in first level cache (associated with particular hibernate session).
2. If cached copy of entity is present in first level cache, it is returned as result of load method.
3. If there is no cached entity in first level cache, then second level cache is looked up for cached entity.
4. If second level cache has cached entity, it is returned as result of load method. But, before returning the entity, it is stored in first level cache also so that next invocation to load method for entity will return the entity from first level cache itself, and there will not be need to go to second level cache again.
5. If entity is not found in first level cache and second level cache also, then database query is executed and entity is stored in both cache levels, before returning as response of load() method.
6. Second level cache validate itself for modified entities, if modification has been done through hibernate session APIs.
7. If some user or process make changes directly in database, the there is no way that second level cache update itself until “timeToLiveSeconds” duration has passed for that cache region. In this case, it is good idea to invalidate whole cache and let hibernate build its cache once again. You can use below code snippet to invalidate whole hibernate second level cache.


#### Configuring EhCache
http://howtodoinjava.com/hibernate/hibernate-ehcache-configuration-tutorial/

To configure ehcache, you need to do two steps:
1. configure Hibernate for second level caching
2. specify the second level cache provider

```
Example 6.2. Configuring cache providers using annotations

@Entity 
@Cacheable
@Cache(usage = CacheConcurrencyStrategy.NONSTRICT_READ_WRITE)
public class Forest { ... }
```

- You can cache the content of a collection or the identifiers, if the collection contains other entities. Use the @Cache annotation on the Collection property.

- @Cache can take several attributes.
Attributes of @Cache annotation, 

  - usage 
      The given cache concurrency strategy, which may be:
     1. NONE
     2. READ_ONLY
     3. NONSTRICT_READ_WRITE
     4. READ_WRITE
     5. TRANSACTIONAL
  - region
The cache region. This attribute is optional, and defaults to the fully-qualified class name of the class, or the qually-qualified role name of the collection.

  - include
Whether or not to include all properties.. Optional, and can take one of two possible values.

A value of all includes all properties. This is the default.
A value of non-lazy only includes non-lazy properties.

- ENABLE_SELECTIVE	Entities are not cached unless you explicitly mark them as cachable. This is the default and recommended value.
- DISABLE_SELECTIVE	Entities are cached unless you explicitly mark them as not cacheable.
- ALL	All entities are always cached even if you mark them as not cacheable.
- NONE	No entities are cached even if you mark them as cacheable. This option basically disables second-level caching.

### The persistence.xml file
- The EntityManager API is great, but how does the server know which database it is supposed to save / update / query the entity objects? How do we configure the underlying object-relational-mapping engine and cache for better performance and trouble shooting? 
- The persistence.xml file gives you complete flexibility to configure the EntityManager.
- The persistence.xml file is a standard configuration file in JPA. 
- It has to be included in the META-INF directory inside the JAR file that contains the entity beans.
- For Hibernate, hibernate.cfg.xml is the configuration file. 
- The persistence.xml file must define a persistence-unit with a unique name in the current scoped classloader. 

References:
https://vladmihalcea.com/tutorials/hibernate/
