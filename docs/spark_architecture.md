# Spark Jobs:
## Typically Jobs involve:
* Loading data from a data source
* Transforming or manipulating the data using Spark's API
* Storing the processed data back to a data store

A Job is created when an Action is called on an RDD (Resilient Distributed Dataset) or a DataFrame.

An Action calls for data processing & computation. The result is sent back to the Driver program, or saved to an external storage system.

Actions are not immediately completed, first a DAG is created to describe how the computation will actually be run -- **Lazy Evaluation**.

Spark examines the DAG and determines what needs to be done in what order -- this is what makes up the **Job**.

So -- **Action on RDD** -> **DAG** -> **Job**. **1 Action == 1 Job**.

**Transformations** do not execute until an action is actually called on an RDD.

# RDDs vs. DataSets
RDD:
- Immutable, partitioned collection of data
- No built-in schema or data type information
- Supports any data type (e.g., strings, integers, custom objects)
- More flexible, but less optimized for performance

DataSet:
- Similar to RDD, but with strong typing and schema information
- Built on top of DataFrame API, providing better performance and optimization
- Supports structured and semi-structured data (e.g., JSON, CSV)
- Provides additional features like encoding and decoding

In general, use RDDs when working with unstructured or custom data, and DataSets when working with structured or semi-structured data.

# map() and filter()
`map()`:

- Applies a function to each element in the RDD/DataFrame/DataSet
- Returns a new RDD/DataFrame/DataSet with the transformed elements
- Function can be a lambda expression or a named function
- Example: rdd.map(lambda x: x * 2) doubles each element in the RDD

`map()` is useful for:

- Data transformation (e.g., converting data types)
- Data normalization (e.g., scaling values)
- Feature extraction (e.g., extracting specific columns)

`filter()`:

- Returns a new RDD/DataFrame/DataSet containing only elements that satisfy a condition
- Condition can be a lambda expression or a named function
- Example: rdd.filter(lambda x: x % 2 == 0) returns only even elements

`filter()` is useful for:
- Data filtering (e.g., removing outliers)
- Data selection (e.g., selecting specific rows)
- Data partitioning (e.g., splitting data into training and testing sets)

# Controlling parallelization
How to control parallelization:

1. Repartitioning: Use repartition() or coalesce() to adjust partition count.
    - repartition(numPartitions): Creates new partitions by hashing keys.
    - coalesce(numPartitions): Reduces number of partitions by merging adjacent partitions.
2. Caching: Use cache() or persist() to store intermediate results.
    - cache(): Stores data in memory.
    - persist(storageLevel): Stores data in memory, disk, or both.
3. Partitioning: Use partitionBy() to specify partitioning scheme.
    - partitionBy(numPartitions, partitionFunc): Partitions data using a custom function.

Best practices:

1. Monitor Spark's execution plans and adjust parallelization accordingly.
2. Balance parallelization with data locality to minimize data shuffling.
3. Use repartition() or coalesce() sparingly, as they can incur additional overhead.

Example:

## Read data from storage with 10 partitions
data = spark.read.parquet("data.parquet", numPartitions=10)

## Filter data and adjust partition count to 5
filtered_data = data.filter(lambda x: x > 10).repartition(5)

## Perform aggregation and specify partitioning scheme
aggregated_data = filtered_data.groupBy("key").agg({"value": "sum"}).partitionBy(3, "key")

## Write data to storage with 2 partitions
aggregated_data.write.parquet("output.parquet", partitionBy=["key"], numPartitions=2)
