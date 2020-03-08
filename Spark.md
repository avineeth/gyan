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
 
 Second, since Spark as a compiler understands your Dataset type JVM object, it maps your type-specific JVM object to Tungsten’s internal memory representation using Encoders. As a result, Tungsten Encoders can efficiently serialize/deserialize JVM objects as well as generate compact bytecode that can execute at superior speeds.
 
 
