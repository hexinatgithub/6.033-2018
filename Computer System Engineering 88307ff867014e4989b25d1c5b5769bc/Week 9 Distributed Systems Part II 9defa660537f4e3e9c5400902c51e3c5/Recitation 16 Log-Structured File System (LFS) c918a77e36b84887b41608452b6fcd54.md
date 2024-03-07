# Recitation 16: Log-Structured File System (LFS)

> The “infinite log” is actually on a finite disk; how does that constraint affect the designers’ goals?
> 

Need to scatter inode and inode-map all throughout the disk. Need to keep a checkpoint-region(CR).

Old versions of file structures scattered throughout the disk. Need garbage collection or cleaner.

> Are there certain file access patterns by applications might make it hard for a log-structured file system to avoid seeks?
> 

Random read.

> What is one technique that the log-structure filesystem uses to achieve higher performance? *(There is more than one technique.)*
> 

There are several techniques that log-structured file systems use to achieve higher performance.

1. Write buffers
2. never overwrites existing data

> How does the log-structured file system implement this technique?
> 
- Write buffers: The file system keeps track of file updates in memory and writes all changes sequentially to the disk in a free space when the buffer is sufficient . This way, the system performs only one write operation, which helps improve disk writing performance.
- transform all updates to file-system state into a series of sequential writes to disk.

> Why does this technique, along with minimizing seeks, lead to good performance?
> 

The write buffer technique, along with minimizing seeks, leads to good performance because it reduces the number of disk writes and seeks. By buffering file updates in memory and writing all changes sequentially to the disk in a free space when the buffer is sufficient, the system performs only one write operation, which helps improve disk writing performance. This way, the file system can achieve high write throughput and low latency.

Minimizing seeks also helps improve performance by reducing the time spent on seeking the disk head to the right location.