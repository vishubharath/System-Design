**Database and Caching**
==============================================================================

## **Relational database management system (RDBMS)**
A relational database like SQL is a collection of data items organized in tables.
There are many techniques to scale a relational database: master-slave replication, master-master replication, federation, sharding, denormalization, and SQL tuning.

- ### **Master-slave replication**
The master serves reads and writes, replicating writes to one or more slaves, which serve only reads. Slaves can also replicate to additional slaves in a tree-like fashion. If the master goes offline, the system can continue to operate in read-only mode until a slave is promoted to a master or a new master is provisioned.

#### **Disadvantage(s): master-slave replication**
Additional logic is needed to promote a slave to a master.
See Disadvantage(s): replication for points related to both master-slave and master-master.

- ### **Master-master replication**
Both masters serve reads and writes and coordinate with each other on writes. If either master goes down, the system can continue to operate with both reads and writes.

#### **Disadvantage(s): master-master replication**
You'll need a load balancer or you'll need to make changes to your application logic to determine where to write.
Most master-master systems are either loosely consistent (violating ACID) or have increased write latency due to synchronization.
Conflict resolution comes more into play as more write nodes are added and as latency increases.

#### **Disadvantage(s): replication**
There is a potential for loss of data if the master fails before any newly written data can be replicated to other nodes.
Writes are replayed to the read replicas. If there are a lot of writes, the read replicas can get bogged down with replaying writes and can't do as many reads.
The more read slaves, the more you have to replicate, which leads to greater replication lag.
On some systems, writing to the master can spawn multiple threads to write in parallel, whereas read replicas only support writing sequentially with a single thread.
Replication adds more hardware and additional complexity.

- ### **Sharding**
Sharding distributes data across different databases such that each database can only manage a subset of the data. Taking a users database as an example, as the number of users increases, more shards are added to the cluster. Similar to the advantages of federation, sharding results in less read and write traffic, less replication, and more cache hits.

#### **Disadvantage(s): sharding**
You'll need to update your application logic to work with shards, which could result in complex SQL queries.
Data distribution can become lopsided in a shard. For example, a set of power users on a shard could result in increased load to that shard compared to others.
Rebalancing adds additional complexity. A sharding function based on consistent hashing can reduce the amount of transferred data.
Joining data from multiple shards is more complex.
Sharding adds more hardware and additional complexity.


## **NoSQL**
NoSQL is a collection of data items represented in a key-value store, document store, wide column store, or a graph database. Data is denormalized, and joins are generally done in the application code. Most NoSQL stores lack true ACID transactions and favor **eventual consistency.**

BASE is often used to describe the properties of NoSQL databases. In comparison with the CAP Theorem, BASE chooses **availability** over consistency.
- Basically available - the system guarantees availability.
- Soft state - the state of the system may change over time, even without input.
- Eventual consistency - the system will become consistent over a period of time, given that the system doesn't receive input during that period.

#### **Sample data well-suited for NoSQL:**

- Rapid ingest of clickstream and log data
- Leaderboard or scoring data
- Temporary data, such as a shopping cart
- Frequently accessed ('hot') tables
- Metadata/lookup tables

## **Caching**

### **Web server caching**
Reverse proxies and caches such as Varnish can serve static and dynamic content directly. Web servers can also cache requests, returning responses without having to contact application servers.

### **Application caching**
In-memory caches such as Memcached and Redis are key-value stores between your application and your data storage. Since the data is held in RAM, it is much faster than typical databases where data is stored on disk. RAM is more limited than disk, so cache invalidation algorithms such as least recently used (LRU) can help invalidate 'cold' entries and keep 'hot' data in RAM

#### **Disadvantage(s): cache**
Need to maintain consistency between caches and the source of truth such as the database through cache invalidation.
Cache invalidation is a difficult problem, there is additional complexity associated with when to update the cache.
Need to make application changes such as adding Redis or memcached.