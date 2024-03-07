# Recitation 17: Databases

> What failure models are we dealing with in this paper?
> 

Ensure that individual users see consistent states of the database even though operation on behalf of many users may be interleaved by the database system.

Database state is not corrupted as the result of a software, system, or media failure.

> Under what circumstances would you want transaction executions to respect the ACID properties? Are there systems that don’t need to have all four properties?
> 

Transaction together provide the key abstraction which allows application developers to disregard irregular or even malicious effects from concurrency or failures of transaction executions, as the transactional server in charge guarantees the consistency of the underlying data and ultimately the correctness of the application.

NoSQL database not follow all four properties.

> What is an example from the paper that illustrates the trade-off between implementing ACID transaction properties and maintaining good performance?
> 

Isolation levels and hierarchical locking.

> How does that policy or technique trade off performance?
> 

Isolation levels hold long-duration write locks, but may not acquire read lock or hold short-duration to trade concurrency for consistency.

Hierarchical locking at a coarser granularity to reduce lock acquire.

> Why would you use this policy or technique? (In what context, under what circumstances, etc.)
> 

Isolation levels used for some application don’t need strict consistency model.

Hierarchical locking use finer granularity to allow more concurrency at tuple level operation, coarser granularity for page, relation or even database level operation.