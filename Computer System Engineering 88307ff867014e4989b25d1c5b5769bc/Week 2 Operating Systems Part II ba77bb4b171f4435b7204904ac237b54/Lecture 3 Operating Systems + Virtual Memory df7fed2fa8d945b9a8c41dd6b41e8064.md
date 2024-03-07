# Lecture 3: Operating Systems + Virtual Memory

# Chapter 5 Enforcing Modularity with Virtualization

## 5.1 Client/server organization within a computer
using virtualization

### 5.1.1.1 Threads

A thread is an abstraction that encapsulates the execution state of an active computation. The state of a thread consists of the variables internal to the interpreter (e.g., processor registers), which include

1. A reference to the next program step (e.g., a program counter)
2. References to the environment (e.g., a stack, a heap, and other current objects)

Exception handlers run in the context of the interrupted thread, but interrupt handlers run in the context of the operating system, there are different restrictions on what the two kinds of handlers can safely do.

### 5.1.1.2 Virtual Memory

Virtual memory can provide each module with its own virtual address space, which has its own virtual addresses. That is, the arguments to jmp, load, and store instructions are all virtual addresses, which a new hardware gadget (called a virtual memory manager) translates to physical addresses. If each module has its own virtual address space, then a module can name only its own physical memory and cannot store to the memory of another module. If a thread of a module by accident calculates an incorrect virtual address and stores to that virtual address, it will affect only that module.

With threads and virtual memory, we can create a virtual computer for each module. Each module has one or more threads that execute the code of the module. The threads of one module share a single virtual address memory that threads of other modules by default cannot touch.

### 5.1.1.3 Bounded Buffer

To allow client and service modules on virtual computers to communicate, we introduce send and receive with a bounded buffer of messages. A thread can invoke send, which attempts to insert the supplied message into a bounded buffer of messages. If the bounded buffer is full, the sending thread waits until there is space in the bounded buffer. A thread invokes receive to retrieve a message from the buffer; if there are no messages, the calling thread waits.

## 5.3 Enforcing modularity in memory

### 5.3.3 More Enforced Modularity with Kernel and User Mode

To complete the enforcement of domains, we modify the processor to prevent threads from overwriting the content of domain registers as follows:

- Add one bit to the processor indicating whether the processor is in kernel mode or user mode, and modify the processor as follows. Instructions can change the value of the processor’s domain registers only in kernel mode, and instructions that attempt to change the domain register in user mode generate an illegal instruction exception. Similarly, instructions can change the mode bit only in kernel mode.
- Extend the set of permissions for a domain to include kernel-only, and modify the processor to make it illegal for threads in user mode to reference addresses in a domain with kernel-only permission. A thread in user mode that attempts to read or write memory with kernel-only permission causes a permission error exception.
- Switch to kernel mode on an interrupt and on an exception so that the handler can process the interrupt (or exception) and invoke privileged instructions.

### 5.3.4 Gates and Changing Modes

Adding one more special instruction to the processor, the supervisor call instruction (svc), which specifies in a register the name for the intended gate. Upon executing the svc instruction, the processor performs two operations as one action:

1. Change the processor mode from user to kernel.
2. Set the pc to an address predefined by the hardware, the entry point of the gate manager.

The gate manager now has control in kernel mode; it can call the appropriate procedures to serve the thread’s request. Typically, gate names are numbers, and the gate manager has a table that records for each gate number the corresponding procedure.

When the thread wants to return to user mode, it executes the following instructions:

1. Change mode from kernel to user.
2. Load the program counter from the top of the stack into the processor’s pc.

Gates can also be used to handle interrupts and exceptions. If the processor encounters an interrupt, the processor enters a special gate for interrupts and the gate manager dispatches the interrupt to the appropriate handler, based on the source of the interrupt.

### 5.3.5 Enforcing Modularity for Bounded Buffers

We can protect the shared bounded buffer, too, by putting the buffer in a shared kernel domain. Now the threads cannot directly write the shared buffer in user mode. The threads must transition to kernel mode to copy messages into the shared buffer. In this design, send and receive are supervisor calls. When a thread invokes send, it changes to kernel mode and copies the message from the sending thread’s domain into the shared buffer. When the receiving thread invokes receive, it changes to kernel mode and copies a message from the shared buffer into the receiving’s domain. As long as the program that is running in kernel mode is written carefully, this design provides stronger enforced modularity because threads in user mode have no direct access to a bounded buffer’s messages.

## 5.4 Virtualizing memory

To reduce the programming burden of managing memory, most modern computer systems virtualize memory, a step that provides two features:

1. Virtual addresses. If programs address memory using virtual addresses and the memory manager translates the virtual addresses to physical addresses on the fly, then the memory manager can grow and move domains in memory behind the program’s back.
2. Virtual address spaces. A single address space may not be large enough to hold all addresses of all applications at the same time. For example, a single large database program by itself may need all the address space available in the hardware. If we can create virtual address spaces, then we can give each program its own address space. This extension also allows a thread to have its program loaded at an address of its choosing (e.g., address 0).

### 5.4.1 Virtualizing Addresses

Virtualizing addresses exploits the design principle decouple modules with indirection. The virtual memory manager provides a layer of indirection between the processor and the memory system by translating the virtual addresses of program instructions into physical addresses, instead of having the program directly issue physical memory addresses. Because it controls the translation from the addresses issued by the program to the addresses understood by the memory system, the virtual memory manager can translate any particular virtual address to different physical memory addresses at different times. Thanks to the translation, the virtual memory manager can rearrange the data in the memory system without having to modify any application program.

### 5.4.2 Translating Addresses Using a Page Map

A virtual address then is a name overloaded with structure consisting of two parts: a page number and a byte offset within that page. The page number uniquely identifies an entry in the page map, and thus a page, and the byte offset identifies a byte within that page. (If the processor provides word addressing instead of byte addressing, offset would specify the word within a page.) The size of a page, in bytes, is equal to the maximum number of different values that can be stored in the byte offset field of the virtual address. If the offset field is 12 bits wide, then a page contains 4,096 ($2^{12}$) bytes.

With this arrangement, the virtual memory manager records in the page map, for each page, the block of physical memory that contains that page. The page map simplifies memory management because the memory manager can allocate a block anywhere in physical memory and insert the appropriate mapping into the page map, without having to copy domains in physical memory to coalesce free space.

A physical address can also be viewed as having two parts: a block number that uniquely identifies a block of memory and an offset that identifies a byte within that block. Translating a virtual address to a physical address is now a two-step process:

1. The virtual memory manager translates the page number of the virtual address to a block number that holds that page by means of some mapping from page numbers to block numbers.
2. The virtual memory manager computes the physical address by concatenating the block number with the byte offset from the original virtual address.

### 5.4.3 Virtual Address Spaces

    A virtual address space provides each application with the illusion that it has a complete address space to itself. The virtual memory manager can implement a virtual address space by giving each virtual address space its own page map.

    There are two options for setting up page maps for the kernel program. The first option is to have each address space include a mapping of the kernel into its address space. For example, the top half of the address space might contain the kernel, in which case the bottom half contains the user program. With this setup, switching from the user program to the kernel (and vice versa) doesn’t require changing the processor’s page-map address register; only the user-mode bit must be changed. To protect the kernel, the kernel sets the permissions for kernel pages to kernel-only; in user mode, performing a store to kernel pages is an illegal instruction. An additional advantage of this design is that in kernel mode, it is easy for the kernel to read data structures of the user program because the user program and kernel share the same address space. A disadvantage of this design is that it reduces the available address space for user programs, which could be a problem in a legacy architecture that has small address spaces.

    The second option is for the memory manager to give the kernel its own separate address space, which is inaccessible to user-level threads. To implement this option, we must extend the svc instruction to switch the page-map address register to the kernel’s page map when entering kernel mode. Similarly, when returning from kernel mode to user mode, the kernel must change the page-map address register to the page map of the thread that entered the gate.

### 5.4.4 Hardware versus Software and the Translation Look-Aside Buffer

    The processor typically maintains a cache of entries of the page map in a smaller fast memory within the processor itself. The hope is that when the processor executes the next instruction, it will discover a previously cached entry that can translate the address, without making a bus reference to the larger memory. The cache memory of translations is usually referred to as the translation look-aside buffer (TLB).

    When a translation is not in the TLB, the processor generates a TLB miss exception. The handler for this interrupt looks up the mapping in a data structure implemented in software, inserts the translation in the TLB, and returns from the interrupt.  

[**UNIX Assignment Part 1**](Lecture%203%20Operating%20Systems%20+%20Virtual%20Memory%20df7fed2fa8d945b9a8c41dd6b41e8064/UNIX%20Assignment%20Part%201%20018fb8da845448029aed799e633c744a.md)