---
Title: Geo-Distributed Active-Active Redis Applications with Conflict-free Replicated Databases (CRDB)
description: $description
weight: $weight
alwaysopen: false
---
Developing globally distributed applications can be challenging, as
developers have to think about race conditions and complex combinations
of events under geo-failovers and cross-region write conflicts. CRDBs
simplify developing such applications by directly using built-in smarts
for handling conflicting writes based on the data type in use. Instead
of depending on just simplistic "last-writer-wins" type conflict
resolution, geo-distributed CRDBs combines techniques defined in CRDT
(conflict-free replicated data types) research with Redis types to
provide smart and automatic conflict resolution based on the data types
intent.

CRDB is a globally distributed database that spans multiple Redis
Enterprise Software clusters. Each CRDB can have many CRDB Instances
(Instances is a CRDB in each cluster that is participating in a global
CRDBs) come with added smarts for handling globally distributed writes
using the proven
[CRDT](https://en.wikipedia.org/wiki/Conflict-free_replicated_data_type)
approach.
[CRDT](https://en.wikipedia.org/wiki/Conflict-free_replicated_data_type)
research describes a set of techniques for creating systems that can
handle conflicting writes. CRDBs are powered by Multi-Master Replication
(MMR) provides a straightforward and effective way to replicate your
data between regions and simplify development of complex applications
that can maintain correctness under geo-failovers and concurrent
cross-region writes to the same data.

![geo replication world
map](/wp-content/uploads/2017/10/crdbs.png){.alignnone .size-full
.wp-image-30163 width="800" height="569"}

CRDBs replicate data between multiple Redis Enterprise Software
clusters. Common uses for CRDBs include disaster recovery,
geographically redundant applications, and keeping data closers to your
user's locations. MMR is always multi-directional amongst the clusters
configured in the CRDB. For unidirectional replication, please see the
ReplicaOf capabilities in Redis Enterprise Software.

Example of synchronization
--------------------------

In the example below, database writes are concurrent at the point in
times t1 and t2 and happen before a sync can communicate the changes.
However, writes at times t4 and t6 are not concurrent as a sync happened
in between.

**Time**

**CRDB Instance1**

**CRDB Instance2**

t1

[SET key1 "a"]{style="font-family: courier;"}

t2

[SET key1 "b"]{style="font-family: courier;"}

t3

[--- Sync ---]{style="font-family: courier;"}

 t4

[SET key1 "c"]{style="font-family: courier;"}

t5

[--- Sync ---]{style="font-family: courier;"}

t6

[SET key1 "d"]{style="font-family: courier;"}

[Learn more about
synchronization](/redis-enterprise-documentation/developing/crdbs/) for
each supported data type and how to develop with them on RES.

Terminology
-----------

1.  **Global conflict-free replicated database (CRDB):** refers to a new
    type of Redis Enterprise Software database that spans clusters.
    There can be one or more member databases across many clusters that
    form a conflict-free replicated database (CRDB)s. Each local
    database can have different shard count, replica count, and other
    database options but contain identical information in steady-state.
2.  **CRDB Instance:** is a "member database" instance of a global CRDB
    which is made up of its own master and slave shards spanning a
    single cluster. An CRDB Instance can participate in only one (CRDB)
    scheme.
3.  **Multi-master Replication (MMR):** is the multi-directional
    replication that power the efficient replication required to achieve
    active-active concurrent writes in CRDBs.
4.  **Conflict-free Replicated Data Types (CRDT):** is the underlying
    research that describes techniques used by Redis data types in CRDBs
    that smartly handle conflicting concurrent writes across member
    CRDBs.
5.  **Participating Clusters:** refers to clusters participating in the
    multi-master replication of a conflict-free replicated database
    (CRDB).
6.  **Concurrent Writes or Concurrent Updates:** Concurrency or updates
    and writes refer to more than events that happen at the same wall
    clock time across member CRDBs. Concurrent updates refer to the fact
    that updates happen in between sync events that catch up member
    CRDBs with updates that happened on other member CRDBs.