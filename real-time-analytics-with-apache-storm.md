Sharding
Sharding is a type of partitioning that separates very large datasets the into smaller, faster, more easily managed parts called data shards.
The word shard means a small part of a whole.

Spout 
Sources of data for the topology (e.g) Posgres/My SQL/Kafka/Kestral

Bolts
Units of computation on data. eg Filtering/Aggregation/Transformations

Tuples
Spouts emit tuples - Immutable ordered list of elements.
Bolts recieve a tuple. Do internal calculations and emit a tuple.
since Tuples are immutable, a new tuple is emitted instead of a modified tuple.

Topology
Spouts and Bolts are linked together in an DAG(Directed Acyclic Graph) called Topology.
Vertices - Spouts/Bolts
Edges - Streams of data


Stream Grouping (Edges)
Shuffle Grouping - Randomly distributes tuples to next stage of bolt instances.
Fields Grouping - Groups Tuples by single/multiple column values
All Grouping - Replicates all Tuples to next stage of bolt instances. This is a rare grouping since its a fan-out/data replicated.
Global Grouping - Sends all tuples to a single next stage bolt instance. (Fan-In).




 

