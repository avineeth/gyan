#### What is RDD

RDD was the primary user-facing API in Spark since its inception.
At the core, an **RDD is an immutable distributed collection of elements of your data**,
**partitioned across nodes** in your cluster that can be **operated in parallel** with a low-level API that offers transformations and actions.

##### When to use RDDs?
Consider these scenarios or common use cases for using RDDs when:

1. you want low-level transformation and actions and control on your dataset;
2. your data is unstructured, such as media streams or streams of text;
3. you want to manipulate your data with functional programming constructs than domain specific expressions;
4. you don’t care about imposing a schema, such as columnar format, while processing or accessing data attributes by name or column; and
5. you can forgo some optimization and performance benefits available with DataFrames and Datasets for structured and semi-structured data.

#### What is DataFrame
  * Like an RDD, a DataFrame is an immutable distributed collection of data.
  * Unlike an RDD, data is organized into named columns, like a table in a relational database. 
  * Designed to make large data sets processing even easier, DataFrame allows developers to impose a structure onto a distributed collection of data, allowing higher-level abstraction;
  * it provides a domain specific language API to manipulate your distributed data; and makes Spark accessible to a wider audience, beyond specialized data engineers.
  
#### What is Dataset
 * Dataset is aa strongly-typed API and DataFrame is an untyped API.
 * Conceptually, consider DataFrame as an alias for a collection of generic objects Dataset[Row], where a Row is a generic untyped JVM object.
 * Dataset, by contrast, is a collection of strongly-typed JVM objects, dictated by a **case class you define in Scala** or **a class in Java**.

```
// read the json file and create the dataset from the 
// case class DeviceIoTData
// ds is now a collection of JVM Scala objects DeviceIoTData
val ds = spark.read.json(“/databricks-public-datasets/data/iot/iot_devices.json”).as[DeviceIoTData]
```
Three things happen here under the hood in the code above:

  1. Spark reads the JSON, infers the schema, and creates a collection of DataFrames.
  2. At this point, Spark converts your data into DataFrame = Dataset[Row], a collection of generic Row object, since it does not know the exact type.
  3. Now, Spark converts the Dataset[Row] -> Dataset[DeviceIoTData] type-specific Scala JVM object, as dictated by the class DeviceIoTData.

##### Advantages of Dataset
 1. Static Typing and Compile type safety
 2. High-level abstraction and custom view into structured and semi-structured data
 3. Ease-of-use of APIs with structure
 4. Second, since Spark as a compiler understands your Dataset type JVM object, it maps your type-specific JVM object to Tungsten’s internal memory representation using Encoders. As a result, Tungsten Encoders can efficiently serialize/deserialize JVM objects as well as generate compact bytecode that can execute at superior speeds.
 
 #### Is spark purely in memory?
 
 
 
 
 
### RDD Persistence
- One of the most important capabilities in Spark is persisting (or caching) a dataset in memory across operations.
- When you persist an RDD, each node stores any partitions of it that it computes in memory and reuses them in other actions on that dataset (or datasets derived from it). This allows future actions to be much faster (often by more than 10x).
- Caching is a key tool for iterative algorithms and fast interactive use.
- **You can mark an RDD to be persisted using the persist() or cache() methods on it.**
- **The first time it is computed in an action, it will be kept in memory on the nodes. Spark’s cache is fault-tolerant – if any partition of an RDD is lost, it will automatically be recomputed using the transformations that originally created it.**
- In addition, each persisted RDD can be stored using a different storage level, allowing you, for example, to persist the dataset on disk, persist it in memory but as serialized Java objects (to save space), replicate it across nodes. These levels are set by passing a StorageLevel object (Scala, Java, Python) to persist(). 
- **The cache() method is a shorthand for using the default storage level, which is StorageLevel.MEMORY_ONLY (store deserialized objects in memory).**

The full set of storage levels is:

   - MEMORY_ONLY	Store RDD as deserialized Java objects in the JVM. If the RDD does not fit in memory, some partitions will not be cached and will be recomputed on the fly each time they're needed. This is the default level.
   - MEMORY_AND_DISK	Store RDD as deserialized Java objects in the JVM. If the RDD does not fit in memory, store the partitions that don't fit on disk, and read them from there when they're needed.
   - MEMORY_ONLY_SER (Java and Scala)	Store RDD as serialized Java objects (one byte array per partition). This is generally more space-efficient than deserialized objects, especially when using a fast serializer, but more CPU-intensive to read.
   - MEMORY_AND_DISK_SER (Java and Scala)	Similar to MEMORY_ONLY_SER, but spill partitions that don't fit in memory to disk instead of recomputing them on the fly each time they're needed.
   - DISK_ONLY	Store the RDD partitions only on disk.
   - MEMORY_ONLY_2, MEMORY_AND_DISK_2, etc.	Same as the levels above, but replicate each partition on two cluster nodes.
   - OFF_HEAP (experimental)	Similar to MEMORY_ONLY_SER, but store the data in off-heap memory. This requires off-heap memory to be enabled.
   
   
