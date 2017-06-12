
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

References:
https://vladmihalcea.com/tutorials/hibernate/
