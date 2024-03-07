# Recitation 7: MapReduce

> MapReduce has a constrained programming model. Are the benefits of using MapReduce worth that constraint?
> 

Code is simpler, smaller, and easier to understand, because the code that deals with fault tolerance, distribution and parallelization is hidden within the MapReduce library.

The performance of the MapReduce library is good enough that we can keep conceptually unrelated computations separate, instead of mixing them together to avoid extra passes over the data.

The program process has become much easier to operate, because most of the problems caused by machine failures, slow machines, and networking hiccups are dealt with automatically by the MapReduce library without operator intervention

> What types of failures does MapReduce handle, and how does it handle them?
> 

Handle worker machine failures. Completed map tasks are re-executed on a failure because their output is stored on the local disk(s) of the failed machine and is therefore inaccessible.

Master machine usually abort operation.

> What are the performance goals of MapReduce (both the programming model + its implementation)?
> 

MapReduce is a framework for processing large-scale data sets in a distributed and parallel manner. The performance goals of MapReduce can be summarized as follows:

- **Scalability**: MapReduce should be able to handle data sets that are too large to fit in a single machine’s memory or disk, and to scale up the computation as the data size and the number of machines increase.
- **Data-locality**: MapReduce should be able to exploit the locality of data and minimize the network traffic and the disk I/O by assigning tasks to machines that are close to the data they need to process.

> How was MapReduce implemented at Google to meet those goals?
> 

Here is a summary of how MapReduce was implemented at Google to meet the performance goals:

- **Distributed and parallel processing**: MapReduce divides the input data into many small chunks and assigns them to different machines (workers) to process in parallel. The intermediate results are then shuffled and sorted to be consumed by another set of workers (reducers) that produce the final output.
- **Fault tolerance and reliability**: MapReduce handles failures of workers by re-executing the failed tasks on other available machines. It also uses a master node (coordinator) to monitor the progress and status of the workers and assign new tasks as needed.
- **Scalability and efficiency**: MapReduce can scale to handle very large data sets (terabytes or petabytes) by adding more machines to the cluster. It also optimizes the network bandwidth and disk I/O by using a distributed file system (GFS) that stores the data close to the workers and supports replication and caching.

> Why was MapReduce implemented in this way?
> 

Automatic parallelization and distribution of large-scale computations achieves high performance on large clusters of commodity PCs. MapReduce can process large dataset.