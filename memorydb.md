# What is MemoryDB

Cloud offering from AWS built on top of OSS Redis to provide enhanced
durability, allowing for use-cases beyond just caching.

# Use case

Redis is good as a data store because it provides features to store complex
data-structures in memory (not just a basic Key-Value store).

Customers would like to use it as a in-memory database, but to do so currently,
they need to build complex pipelines to make sure data in Redis remains
consistent and up-to-date.

```quote
Redis employs asynchronous replication for high availability and read scaling
and an on-disk transaction log for local durability. Redis does not offer a
replication solution that can tolerate the loss of nodes without data loss, or
can offer scalable strongly-consistent reads. This limits its ability to be
leveraged for use cases beyond caching.
```

```quote
For example, a catalog microservice in an e-commerce shopping
application may want to fetch item details from Redis to serve
millions of page views per second. In an optimal setup, the service
stores all data in Redis, but instead must use a data pipeline to ingest
catalog data into a separate database, like Amazon DynamoDB [ 24 ],
before triggering writes to Redis through a DynamoDB stream.
When the service detects that an item is missing in Redis—a sign of
data loss—a separate job must reconcile Redis against DynamoDB.
Our customers find this approach complex, and asked for a way
to simplify their architectures, reduce costs, and achieve better
performance. We built MemoryDB for Redis to meet these needs.
```

```quote
Our customers asked us for a solution to allow Redis to be used as a primary
database with multi-Availability Zone (AZ) 1 durability.
```

Amazon MemoryDB provides:

- 11 9s of durability (similar to S3)
- single-digit millisecond writes
- microsecond read latencies
- fully compatible with Redis

# Vanilla Redis working

- Failover

# How does it work

```quote
We use Redis as an in-memory execution and storage engine but redi-
rect its existing replication stream into the transaction log, which is
responsible for propagation of writes to replicas and leader election
```

```quote
Many MemoryDB customers operate shards with either a
primary only or a primary with a single replica, but still receive
durability across three AZs, which would not be possible if compute
and storage were coupled.
```

```quote
After a client sends a mutation, the
reply from the mutation operation is stored in a tracker until the
transaction log acknowledges persistence and only then sent to the
client.
```

# Presentation flow

- explain problems with redis
- explain how durability is achieved in memorydb

# Things to note

1. Leader election

   - MemoryDB ensures that only fully caught up replicas become eligible for
     promotion to primaries, therefore maintaining strong consistency across
     failures.
   - MemoryDB always ensures leader singularity, by leveraging a lease system
     that demotes failed primaries
   - MemoryDB does not require any cluster quorum for liveness, therefore
     improving availability over Redis cluster bus leadership mechanism.

2. Snapshotting
