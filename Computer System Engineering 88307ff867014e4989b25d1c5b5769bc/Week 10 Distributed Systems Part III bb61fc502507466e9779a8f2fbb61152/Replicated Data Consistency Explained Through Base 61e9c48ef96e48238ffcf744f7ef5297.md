# Replicated Data Consistency Explained Through Baseball

# Abstract

    Some cloud storage services, like Windows Azure, replicate data while providing strong consistency to their clients while others, like Amazon, have chosen eventual consistency in order to obtain better performance and availability. A broader class of consistency guarantees can, and perhaps should, be offered to clients that read shared data.

## 1. Introduction

    The reason for exploring different consistency models is that there are fundamental tradeoffs between consistency, performance, and availability. Offering stronger consistency generally results in lower performance and reduced availability for reads or writes or both. Each proposed consistency model occupies some point in the complex space of tradeoffs.

    But are different consistencies useful in practice? Can application developers cope with eventual consistency? Should cloud storage systems offer an even greater choice of consistency than the consistent and eventually consistent reads offered by Amazon’s SimpleDB?

## 2. Read Consistency Guarantees

    The six consistency guarantees that I advocate in this section can be described in a simple, implementation independent way. This not only benefits application developers but also can permit flexibility in the design, operation, and evolution of the underlying storage system.

    These consistency guarantees are based on a simple model in which clients perform read and write operations to a data store. The data is replicated among a set of servers, but the details of the replication protocol are hidden from clients. Writes are serialized and eventually performed in the same order at all servers. This order is consistent with order in which write operations are submitted by clients. Reads return the values of one or more data objects that were previously written, though not necessarily the latest values. Each read operation can request a consistency guarantee, which dictates the set of allowable return values. Each guarantee is defined by the set of previous writes whose results are visible to a read operation. Table 1 summarizes these six consistency guarantees.

![Untitled](Replicated%20Data%20Consistency%20Explained%20Through%20Base%2061e9c48ef96e48238ffcf744f7ef5297/Untitled.png)

> Strong consistency is particularly easy to understand. It guarantees that a read operation returns the value that was last written for a given object. If write operations can modify or extend portions of a data object, such as appending data to a log, then the read returns the result of applying all writes to that object. In other words, a read observes the effects of all previously completed writes.
> 

> Eventual consistency is the weakest of the guarantees, meaning that it allows the greatest set of possible return values. For whole-object writes, an eventually consistent read can return any value for a data object that was written in the past. More generally, such a read can return results from a replica that has received an arbitrary subset of the writes to the data object being read.
> 

> By requesting a consistent prefix, a reader is guaranteed to observe an ordered sequence of writes starting with the first write to a data object. For example, the read may be answered by a replica that receives writes in order from a master replica but has not yet received an unbounded number of recent writes. In other words, the reader sees a version of the data store that existed at the master at some time in the past. This is similar to the “snapshot isolation” consistency offered by many database management systems.
> 

> Bounded staleness ensures that read results are not too out-of-date. Typically, staleness is defined by a time period T, say 5 minutes. The storage system guarantees that a read operation will return any values written more than T minutes ago or more recently written values. Alternative, some systems have defined staleness in terms of the number of missing writes or even the amount of inaccuracy in a data value. I find that time bounded staleness is the most natural concept for application developers.
> 

> Monotonic Reads is a property that applies to a sequence of read operations that are performed by a given storage system client. As such, it is often called a “session guarantee.” With monotonic reads, a client can read arbitrarily stale data, as with eventual consistency, but is guaranteed to observe a data store that is increasingly up-to-date over time. In particular, if the client issues a read operation and then later issues another read to the same object(s), the second read will return the same value(s) or the results of later writes.
> 

> Read My Writes is a property that also applies to a sequence of operations performed by a single client. It guarantees that the effects of all writes that were performed by the client are visible to the client’s subsequent reads. If a client writes a new value for a data object and then reads this object, the read will return the value that was last written by the client (or some other value that was later written by a different client). (Note: In other papers, this has been called “Read Your Writes,” but I have chosen to rename it to more accurately describe the guarantee from the client’s viewpoint.)
> 

These last four read guarantees are all a form of eventual consistency but stronger than the eventual consistency model that is typically provided in systems like Amazon. None of these four guarantees is stronger than any of the others, meaning that each might result in a read operation returning a different value. In some cases, as will be shown later, applications may want to request multiple of these guarantees. For example, a client could request monotonic reads and read my writes so that it observes a data store that is consistent with its own actions.