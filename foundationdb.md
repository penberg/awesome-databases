# FoundationDB

This is a summary and review of the SIGMOD'21 paper "FoundationDB: A Distributed Unbundled Transactional Key Value Store" by Jingyu Zhou et al., which is available at https://www.foundationdb.org/files/fdb-paper.pdf

The paper has also been discussed elsewhere:

http://charap.co/reading-group-foundationdb-a-distributed-unbundled-transactional-key-value-store/
https://www.micahlerner.com/2021/06/12/foundationdb-a-distributed-unbundled-transactional-key-value-store.html

## Introduction

FoundationDB is an unbundled, transactional key-value store, which aims to combine the flexibility and scalability of NoSQL database systems with the ACID transactional capabilities of NewSQL database systems. The authors state that the motivation for FoundationDB is the need for a scalable, distributed storage system for persisting application state. The authors also argue out that application developers need "sufficiently strong" semantics and flexible data model for developer velocity. Finally, FoundationDB has to scale to the needs of large scale workloads used by Apple and Snowflake, for exmaple: billions of users (!), up to exabytes of data, and millions of requests per second.

## Design

At its core, FoundationDB is an ordered, transactional key-value store, which supports strictly serializable multi-key transactions. Unlike many other database systems, FoundationDB does not provide structured semantics, query language, data model, schema management, or secondary indexes. Instead, FoundationDB focuses on providing low level building blocks for applications and database layers that implement needed semantics.

FoundationDB uses an _unbundled architecture_ where each component can be scaled separately.
At high level, components are split into control plane, which manages system metadata, and data plane components, which manages data.

  * Control plane components: coordinators, cluster controller, data distributor.
  * Data plane components: transaction system, log system, storage system.

## Transactions

## Replication

* Metadata is replacted with Active Disk Paxos.
* Log replication?
* Storage replication?
* Geo-replication and failover.
  * Performance & consistency trade-off.
  * Synchronous = strong consistency, but high latency.
  * Asynchronous = low latency, but with data loss?.
  * FoundationDB method: use multiple availability zones within the same region.

## Simulator
