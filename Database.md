
### What are the differences between DDL, DML and DCL in SQL?
- DDL stands for Data Definition Language. SQL queries like CREATE, ALTER, DROP and RENAME come under this.
- DML stands for Data Manipulation Language. SQL queries like SELECT, INSERT and UPDATE come under this.
- DCL stands for Data Control Language. SQL queries like GRANT and REVOKE come under this.

### Having vs Where Clause?
- The difference between the having and where clause in SQL is that the where clause cannot be used with aggregates, but the having clause can.
- The where clause works on row’s data, not on aggregated data.  
- It is not a predefined rule but  in a good number of the SQL queries, we use WHERE prior to GROUP BY and HAVING after GROUP BY. 
- The Where clause acts as a pre filter where as Having as a post filter.

```
SELECT Student, Score FROM Marks WHERE Score >=40

SELECT Student, SUM(score) AS total FROM Marks GROUP BY Student
HAVING total > 70
```

### What is a GUID?

GUID (or UUID) is an acronym for 'Globally Unique Identifier' (or 'Universally Unique Identifier'). It is a 128-bit integer number used to identify resources. The term GUID is generally used by developers working with Microsoft technologies, while UUID is used everywhere else.

##### How unique is a GUID?

128-bits is big enough and the generation algorithm is unique enough that if 1,000,000,000 GUIDs per second were generated for 1 year the probability of a duplicate would be only 50%. Or if every human on Earth generated 600,000,000 GUIDs there would only be a 50% probability of a duplicate.

##### How are GUIDs used?

GUIDs are used in enterprise software development in C#, Java, and C++ as database keys, component identifiers, or just about anywhere else a truly unique identifier is required. GUIDs are also used to identify all interfaces and objects in COM programming.

### What is a Trigger?
A Trigger is a code that associated with insert, update or delete operations. The code is executed automatically whenever the associated query is executed on a table. Triggers can be useful to maintain integrity in database.

### What is an Index?
- Database indexes are auxiliary data structures that allow for quicker retrieval of data.
- The most common type of index is a B-tree index because it has very good general performance characteristics and allows a wide range of comparisons, including both equality and inequalities.
- The penalty for having a database index is the cost required to update the index, which must happen any time the table is altered.
- There is also a certain about of space overhead, although indexes will be smaller than the table they index.
http://20bits.com/article/interview-questions-database-indexes

#### What is the difference between Trigger and Stored Procedure?
Unlike Stored Procedures, Triggers cannot be called directly. They can only be associated with queries.

### Views vs Materialized Views

- Views evaluate the data in the tables underlying the view definition at the time the view is queried.
- It is a logical view of your tables, with no data stored anywhere else. 
- The upside of a view is that it will always return the latest data to you.
- The downside of a view is that its performance depends on how good a select statement the view is based on. If the select statement used by the view joins many tables, or uses joins based on non-indexed columns, the view could perform poorly.

- Materialized views are similar to regular views, in that they are a logical view of your data (based on a select statement), however, the underlying query resultset has been saved to a table.
- The upside of this is that when you query a materialized view, you are querying a table, which may also be indexed. In addition, because all the joins have been resolved at materialized view refresh time, you pay the price of the join once (or as often as you refresh your materialized view), rather than each time you select from the materialized view. In addition, with query rewrite enabled, Oracle can optimize a query that selects from the source of your materialized view in such a way that it instead reads from your materialized view.
- In situations where you create materialized views as forms of aggregate tables, or as copies of frequently executed queries, this can greatly speed up the response time of your end user application. The downside though is that the data you get back from the materialized view is only as up to date as the last time the materialized view has been refreshed.
- Materialized views can be set to refresh manually, on a set schedule, or based on the database detecting a change in data from one of the underlying tables. Materialized views can be incrementally updated by combining them with materialized view logs, which act as change data capture sources on the underlying tables.
- Materialized views are most often used in data warehousing / business intelligence applications where querying large fact tables with thousands of millions of rows would result in query response times that resulted in an unusable application.


### Delete duplicate rows from Oracle tables

The most effective way to detect duplicate rows is to join the table against itself as shown below.
```
select 
   book_unique_id, 
   page_seq_nbr, 
   image_key 
from 
   page_image a 
where 
   rowid > 
     (select min(rowid) from page_image b 
      where 
         b.key1 = a.key1 
      and 
         b.key2 = a.key2 
      and 
         b.key3 = a.key3 
      );
```
