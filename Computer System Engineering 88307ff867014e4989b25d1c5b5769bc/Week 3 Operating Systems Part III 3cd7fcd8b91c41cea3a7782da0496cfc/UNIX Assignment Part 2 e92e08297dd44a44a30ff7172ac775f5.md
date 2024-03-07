# UNIX Assignment Part 2

# 5. Processes and Images

An *image* is a computer execution environment. It includes a core image, general register values, status of open files, current directory, and the like. An image is the current state of a pseudo computer.

A *process* is the execution of an image. While the processor is executing on behalf of a process, the image must reside in core; during the execution of other processes it remains in core unless the appearance of an active, higher-priority process forces it to be swapped out to the fixed-head disk.

The user-core part of an image is divided into three logical segments. The program text segment begins at location 0 in the virtual address space. During execution, this segment is write-protected and a single copy of it is shared among all processes executing the same program. At the first 8K byte boundary above the program text segment in the virtual address space begins a non-shared, writable data segment, the size of which may be extended by a system call. Starting at the highest address in the virtual address space is a stack segment, which automatically grows downward as the hardware’s stack pointer fluctuates.

> What 6.033 concepts are exemplified by UNIX?
> 

Modularity: the shell and program use exec() function to enforce modularity.

Abstraction: parent and child process have effective boundaries.

Layering: process primitive used to construct init program, init program used to construct shell program, shell program provide function to user application.

Hierarchy: assemble file system, threads, pipes to shell.

> What design principles, or objectives, do you think the authors were following as they developed UNIX?
> 

Simplicity: the interface is simple. Both in implement and interface.

Consistency: the file write API keep  consistency but not introduce unnecessary complexity.

Correctness: process is isolate and will not interfere with each other. All file concurrent write is allowed but accept.

Completeness: the file system and thread contain necessary interface, complex function is left to user to implement.

> What does the UNIX shell do?
> 

Interpret shell command and create new child process, wait for child process to died and continue serving.

> How does it work?
> 

Associate file descript with child process, call exec and wait for child process to died.

> Why is it useful?
> 

Provide user interface, function and environment, invoke user program.