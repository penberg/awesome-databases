# Awesome Databases

Databases are awesome!

## Table of Contents

* [Database systems](#database-systems)
  * [Distributed relational databases](#distributed-relational-databases)
  * [Document stores](#document-stores)
  * [Embedded databases](#embedded-databases)
  * [Graph databases](#graph-databases)
  * [Key-value stores](#key-value-stores)
  * [Multi-model databases](#multi-model-databases)
  * [Time series databases](#time-series-databases)
  * [Wide column stores](#wide-column-stores)
* [Research](#research)
  * [Foundations](#foundations)
  * [Distributed databases](#distributed-databases)
  * [Consistency](#consistency)
  * [Query optimization](#query-optimization)
  * [Others](#others)
* [Resources](#resources)

## Database systems

### Distributed relational databases

* [Cloud Spanner](https://cloud.google.com/spanner)
* [CockroachDB](https://www.cockroachlabs.com)
* [SingleStore](https://www.singlestore.com)
* [YugabyteDB](https://www.yugabyte.com)

### Document stores

* [MongoDB](https://www.mongodb.com)

### Embedded databases

* [SQLite](https://www.sqlite.org/)

### Graph databases

* [Dgraph](https://dgraph.io)
* [Neo4j](https://neo4j.com)

### Key-value stores

* [Aerospike](https://aerospike.com)
* [FoundationDB](https://www.foundationdb.org)

### Multi-model databases

* [CosmosDB](https://azure.microsoft.com/en-us/services/cosmos-db/)
* [Fauna](https://fauna.com)

### Time series databases

* [InfluxDB](https://www.influxdata.com)
* [Timescale](https://www.timescale.com)

### Wide column stores

* [Apache Cassandra](https://cassandra.apache.org/)
* [DynamoDB](https://aws.amazon.com/dynamodb/)
* [ScyllaDB](https://www.scylladb.com)

## Research

### Foundations

* [A Relational Model of Data for Large Shared Data Banks](https://www.seas.upenn.edu/~zives/03f/cis550/codd.pdf) by E. F. Codd. CAMC, June 1970.
* [Volcano - An Extensible and Parallel Query Evaluation System](https://paperhub.s3.amazonaws.com/dace52a42c07f7f8348b08dc2b186061.pdf) by Goetz Graefe. IEEE TKDE, Volume 6, Number, 1994.
* [The Cascades Framework for Query Optimization](https://www.cse.iitb.ac.in/infolab/Data/Courses/CS632/Papers/Cascades-graefe.pdf) by Goetz Graefe. IEEE Data Engineering Bulletin, Volume 18, Number 3, September 1995.

### Distributed databases

* [FoundationDB: A Distributed Unbundled Transactional Key Value Store](https://www.foundationdb.org/files/fdb-paper.pdf) by Jingyu Zhou et al. SIGMOD'21.
* [CockroachDB: The Resilient Geo-Distributed SQL Database](https://dl.acm.org/doi/pdf/10.1145/3318464.3386134) by Rebecca Taft et al. SIGMOD'20.

### Consistency

* [Tunable Consistency in MongoDB](http://www.vldb.org/pvldb/vol12/p2071-schultz.pdf) by William Schultz et al. VLDB'18.

### Query optimization

* [The MemSQL query optimizer: a modern optimizer for real-time analytics in a distributed database](https://15721.courses.cs.cmu.edu/spring2020/papers/20-optimizer2/chen-vldb2016.pdf) by Jack Chen et al. VLDB'16.

### Others

* [Fast Scans on Key-Value Stores](http://www.vldb.org/pvldb/vol10/p1526-bocksrocker.pdf) by Markus Pilman et al. VLDB'17.
* [The Snowflake Elastic Data Warehouse](https://dl.acm.org/doi/pdf/10.1145/2882903.2903741) by Benoit Dageville et al. SIGMOD'16.
* [The Cosmos Big Data Platform at Microsoft: Over a Decade of Progress and a Decade to Look Forward](http://vldb.org/pvldb/vol14/p3148-jindal.pdf) by Conor Power et al. VLDB'21.
* [Data Blocks: Hybrid OLTP and OLAP on Compressed Storage using both Vectorization and Compilation](https://db.in.tum.de/downloads/publications/datablocks.pdf) by Harald Lang et al. SIGMOD'16.

## Blogs

* "Writing a SQL database from scratch in Go" by Phil Eaton
  * [1. SELECT, INSERT, CREATE and a REPL](https://notes.eatonphil.com/database-basics.html)
  * [2. Binary expressions and WHERE filters](https://notes.eatonphil.com/database-basics-expressions-and-where.html)
  * [3. Indexes](https://notes.eatonphil.com/database-basics-indexes.html)
  * [4. A database/sql driver](https://notes.eatonphil.com/database-basics-a-database-sql-driver.html)
 
## Resources

* [Awesome Database Learning](https://github.com/pingcap/awesome-database-learning)
