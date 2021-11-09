# FoundationDB

This is a summary and review of the SIGMOD'21 paper "FoundationDB: A Distributed Unbundled Transactional Key Value Store" by Jingyu Zhou et al., which is available at https://www.foundationdb.org/files/fdb-paper.pdf

The paper has also been discussed elsewhere:

* http://charap.co/reading-group-foundationdb-a-distributed-unbundled-transactional-key-value-store/
* https://www.micahlerner.com/2021/06/12/foundationdb-a-distributed-unbundled-transactional-key-value-store.html

## Introduction

FoundationDB is an unbundled, transactional key-value store, which aims to combine the flexibility and scalability of NoSQL database systems with the ACID transactional capabilities of NewSQL database systems.
One of the main motivations stated for FoundationDB is the need for cloud services to persist their application state, which the authos argue to require the following properties from a data store:

* Fault tolerance
* High availability
* Strict serializability
* Flexible data model (for rapid application development)
* Millions of requests per second
* Petabyte/exabyte range of data

The scalability, high availability, and fault tolerance properties have been addressed by NoSQL systems such as Apache Cassandra, MongoDB, and CouchBase already a decade ago, but with _eventual consistency_ model, which the authors argue to pushe complexity to application developers who have to manage concurrent updates.
FoundationDB attempts to bridge this gap by providing serializable transactions that scale for _read-mostly_ workloads.
The authors also note that existing NoSQL systems such as Apache Cassandra, MongoDB, and CouchBase have also recently added some support for ACID, and even SQL.

## Design

At its core, FoundationDB is an ordered, transactional key-value store, which supports strictly serializable multi-key transactions.
Unlike many other database systems, FoundationDB does not provide structured semantics, query language, data model, schema management, or secondary indexes.
Instead, FoundationDB focuses on providing low level building blocks for applications and database layers that implemente needed semantics:

> For example, the FoundationDB Record Layer adds back much of what users expect from a relational database, and JanusGraph, a graph database, provides an implementation as a FoundationDB layer. In its newest release, CouchDB (arguably the first NoSQL system) is being re-built as a layer on top of FoundationDB.

FoundationDB has the following design goals:

* Decouple write path (transactions) from read path, and scale them separately.
* Use recovery for failure handling.
* Combine fail-fast with fast recovery
* Test using simulation

### System interface

The basic system interface to FoundationDB is a combination of `get` and `set` operations for individual key-value pairs, `getRange` for range queries, and `clear` for range deletions:

> FDB exposes operations to read and modify single keys as well as ranges of keys. The get() and set() operations read and write a single key-value pair, respectively. For ranges, getRange() returns a sorted list of keys and their values within the given range; and clear() deletes all keys-value pairs whithin a range or starting with a certain key prefix.

FoundationDB client buffers writes until the client commits:

> An FDB transaction observes and modifies a snapshot of the database at a certain version and changes are applied to the underlying database only when the transaction commits. A transaction’s writes (i.e., set() and clear() calls) are buffered by the FDB client until the final commit() call, and read-your-write semantics are preserved by combining results from database look-ups with uncommitted writes of the transaction.

TODO: How does this work in practice :point_up:?

FoundationDB has limits for key, value, and transactions sizes:

> Key and value sizes are limited to 10 KB and 100 KB respectively for better performance. Transaction size is limited to 10 MB, including the size of all written keys and values as well as the size of all keys in read or write conflict ranges that are explicitly specified.

### Architecture

FoundationDB takes a modular approach with control plane and data plane that can be separtely scaled.
The control plane has the following components:

> The control plane is responsible for persisting critical system metadata, i.e., the configuration of transaction systems, on Coordinators. These Coordinators form a disk Paxos group and select a singleton ClusterController. The ClusterController monitors all servers in the cluster and recruits three singleton processes, Sequencer, DataDistributor, and Ratekeeper, which are re-recruited if they fail or crash. The Sequencer assigns read and commit versions to transactions. The DataDistributor is responsible for monitoring failures and balancing data among StorageServers. Ratekeeper provides overload protection for the cluster.

For data plane, FoundationDB targets read-mostly workloads:

> FDB targets OLTP workloads that are read-mostly, read and write a small set of keys, have low contention, and require scalability.

* TODO: What does :point_up: mean for other types of workloads? In particular, how slow are _writes_?

The data plane is split into following independent components: transaction management system (TS), log system (LS), storage system (SS):

> FDB chooses an unbundled architecture: a distributed transaction management system (TS) performs in-memory transaction processing, a log system (LS) stores Write-Ahead-Log (WAL) for TS, and a separate distributed storage system (SS) is used for storing data and servicing reads.

The transaction management system is further split into the following components:

> The TS provides transaction processing and consists of a Sequencer, Proxies, and Resolvers, all of which are stateless processes. The LS contains a set of Log-Servers and the SS has a number of StorageServers. This scales well to Apple’s largest transactional workloads.
The Sequencer assigns a read version and a commit version to each transaction and, for historical reasons, also recruits Proxies, Resolvers, and LogServers. Proxies offer MVCC read versions to clients and orchestrate transaction commits. Resolvers check for conflicts between transactions.

Log system looks as follows:

> LogServers act as replicated, sharded, distributed persistent queues, where each queue stores WAL data for a StorageServer.

Storage system looks as follows:

> The SS consists of a number of StorageServers for serving client reads, where each StorageServer stores a set of data shards, i.e., contiguous key ranges.

The vast majority of processes in a system are storage servers, which form a distributed B-tree. Individual processes use modified SQLite for storage.

> StorageServers are the majority of processes in the system, and together they form a distributed B-tree. Currently, the storage engine on each StorageServer is a modified version of SQLite, with enhancements that make range clears faster, defer deletion to a background task, and add support for asynchronous programming.

* TODO: Work to use RocksDB instead of SQLite.

### Scaling

In FoundationDB, processes have specific roles: coordinator, storage server, or sequencer.
FoundationDB cluster is scaled by increasing the number of processes.
As read and write paths are decoupled, read and write capacity can be scaled independently.

> FDB’s design is decoupled; processes are assigned different roles (e.g., Coordinators, StorageServers, Sequencer), and the database scales by expanding the number of processes for each role. This separates the scaling of client reads from client writes (i.e., transaction commits).

The authors claim that reads scale linearly with the number of storage servers because clients issue read requests directly to the shard that owns a piece of data.

> Because clients directly issue reads to sharded StorageServers, reads scale linearly with the number of StorageServers.

* TODO: Does read have to contact a transaction manager, though?

The authors mention that writes are also scaled by adding more processes, but don't discuss _how_ FoundationDB writes scale:

> Similarly, writes are scaled by adding more processes to Proxies, Resolvers, and Log Servers in TS and LS. 

### Bootstrapping and reconfiguration

Most of the data in a FoundationDB cluster is stored in a storage server. Storage server metadata is persisted in log servers and log server configuration is persisted in coordinators.

> All user data and most of the system metadata (keys that start with 0xFF prefix) are stored in StorageServers. The metadata about StorageServers is persisted in LogServers, and the configuration of LS (i.e., information about LogServers) is stored in all Coordinators.

One of the coordinators is elected as a cluster controller:

> Using Coordinators as a disk Paxos group, servers attempt to become the ClusterController if one does not exist. 

FoundationDB uses the same reconfiguration process for transaction server, log server, and database configuration changes:

> Whenever there is a failure in the TS or LS, or a database configuration change, a reconfiguration process brings the transaction management system to a new configuration, i.e., a clean state. Specifically, the Sequencer process monitors the health of Proxies, Resolvers, and LogServers. If any one of the monitored processes fails or the database configuration changes, the Sequencer process terminates. The ClusterController will detect the Sequencer failure event, then recruit a new Sequencer, which follows the above bootstrapping process to spawn the new TS and LS instance. In this way, transaction processing is divided into epochs, where each epoch represents a generation of the transaction management system with its unique Sequencer process.

## Transactions

## Testing and debugging

> The development of FDB took a radical approach — before building the database itself, we built a deterministic database simulation framework that can simulate a network of interacting processes and a variety of disk, process, network, and request-level failures and recoveries, all within a single physical process. This rigorous testing in simulation makes FDB extremely stable, and allows its developers to introduce new features and releases in a rapid cadence. This is unusual not only for distributed databases, but even for centralized systems.

* TODO: Interesting contrast to other systems using formal verification (e.g. TLA+).
