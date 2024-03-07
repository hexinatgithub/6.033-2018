# Recitation 18: Consistency Guarantees

> What are other systems where some of the weaker consistency guarantees are applicable?
> 

shopping cart data.

> What type of app-specific knowledge matters when determining the “right” consistency guarantee?
> 

Knowledge of who writes data or when data was last written.

> What is a consistency guarantee? What aspects of a system does it affect?
> 

By having the storage system perform write operations in a strict order, application developers can avoid the complication of dealing with update conflicts from concurrent writes.  This leaves developers with the job of choosing their desired read consistency.

System will read some stale data object.

> How does a system designer choose an appropriate consistency guarantee?
> 

Knowledge of who writes data or when data was last written can sometimes allow clients to perform a relaxed consistency read, and obtain the associated benefits, while reading up-to-date data.

> Why does the choice of consistency guarantee matter?
> 

Trade-offs between consistency, performance, and availability are tangible and may become more pronounced with the proliferation of geo replicated services.