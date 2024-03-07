# Lecture 4: Bounded Buffers + Locks

## 5.2 Virtual links using send, receive, and a bounded buffer

An operating system might provide the following interface for send and receive with bounded buffers:

- buffer ← allocate_bounded_buffer (n): allocate a bounded buffer that can hold n messages.
- deallocate_bounded_buffer (buffer): free the bounded buffer buffer.
- send (buffer, message): if there is room in the bounded buffer buffer, insert message in the buffer. If not, stop the calling thread and wait until there is room.
- message ← receive (buffer): if there is a message in the bounded buffer buffer, return the message to the calling thread. If there is no message in the bounded buffer, stop the calling thread and wait until another thread sends a message to buffer buffer.

## 5.5 Virtualizing processors using threads

The thread manager might support this simple version of a thread_allocate procedure:

> thread_id ← allocate_thread (starting_procedure, address_space_id): allocate a new thread in address space address_space_id. The new thread is to begin with a call to the procedure specified in the argument starting_procedure. allocate_thread returns an identifier that names the just-created thread. If the thread manager cannot allocate a new thread (e.g., it doesn’t have enough free memory to allocate a new stack), allocate_thread returns an error.
> 

    The thread manager implements allocate_thread as follows: it allocates a range of memory in address_space_id to be used as the stack for procedure calls, selects a processor, and sets the  processor’s pc to the address starting_procedure in address_space_id and the processor’s sp to the bottom of the allocated stack.

    The basic idea for virtualizing the processor: when a thread is waiting for an event, its processor can switch from that thread to another one by saving the state of the waiting thread and loading the state of a different thread. Since in most system designs many threads spend much of their life waiting for a condition to become true, this idea is general.

    The job of yield is to switch a processor from one thread to another. In its essence, yield is a simple three-step operation:

1. Save this thread’s state so that it can resume later.
2. Schedule another thread to run on this processor.
3. Dispatch this processor to that thread.

    The concrete problem that yield solves is multiplexing many threads over a potentially smaller number of processors. Each processor typically has an identifier (id), a stack pointer (sp), a program counter (pc), and a page-map address register (pmar), pointing to the page map that defines the thread’s address space. Processors may have additional state, such as floating point registers. Each thread has virtual versions of id, sp, pc, and pmar, and any additional state. yield must multiplex perhaps many threads in the thread layer over a limited number of processors in the processor layer.

    This difference places restrictions on what code can run in an interrupt handler: in general, an interrupt handler shouldn’t invoke procedures (e.g., yield) of the thread layer that assume that the thread is running on the current processor because the interrupt may have nothing to do with the currently running thread. An interrupt handler can invoke an exception handler in the thread layer if the handler determines that this interrupt pertains to the thread running on the interrupted processor. The exception handler can then adjust the thread’s environment.

### 5.5.2 Implementing yield

```
1 shared structure processor_table[7]
2     integer thread_id // identity of thread running on a processor
3 shared structure thread_table[7]
4     integer topstack // value of this thread’s stack pointer
5     integer state // runnable or running
6 shared lock instance thread_table_lock
7 procedure get_thread_id() return processor_table[cpuid].thread_id
8 procedure yield ()
9      acquire (thread_table_lock)
10     enter_processor_layer (get_thread_id()) 
11     release (thread_table_lock)
12     return
13 procedure enter_processor_layer (this_thread)
14     thread_table[this_thread].state ← runnable // switch state to runnable
15     thread_table[this_thread].topstack ← sp // store yielding’s thread sp
16     scheduler()
17     return
18 procedure scheduler()
19     j = get_thread_id()
20     do // schedule a runnable thread
21         j ← (j + 1) modulo 7
22     while thread_table[j].state ≠ runnable // skip running threads
23     thread_table[j].state ← running // set state to running
24     processor_table[cpuid].thread_id ← j // record that processor runs thread j
25     exit_processor_layer(j) // dispatch this processor to thread j
26     return
27 // exit_processor_layer returns from the new thread’s invocation of 
28 // enter_processor_layer and returns control to the new thread’s invocation of yield.
29 procedure exit_processor_layer (new)
30     sp ← thread_table[new].topstack // dispatch: load sp of new thread
31     return
```

    From this example we can see that a thread always releases its processor by calling enter_processor_layer and that the thread always resumes right after the call to enter_processor_layer. This stylized flow of control in which a thread always releases its processor at the same point and resumes at that point is an example of what is sometimes called co-routine.
    To ensure that the thread switch is atomic, the thread that invokes enter_processor_layer acquires thread_table_lock and the thread that resumes using exit_processor_layer releases thread_table_lock (line 11). Because the scheduler is likely to choose a different thread to run from the one that called yield, the thread that releases the lock is most likely a different thread from the one that acquired the lock. In essence, the thread that releases the processor passes the lock along to the thread that next receives the processor.

### 5.5.3 Creating and Terminating Threads

- exit_thread (): destroy and clean up the calling thread. When a thread is done with its job, it invokes exit_thread to release its state.
- destroy_thread (id): destroy the thread identified by id. In some cases, one thread may need to terminate another thread. For example, a user may have started a thread that turns out to have a programming error such as an endless loop, and thus the user wants to terminate it. For these cases, we might want to provide a procedure to destroy a thread.

```
1 shared structure processor_table[7] // each processor maintains the following information:
2     integer topstack // value of stack pointer
3     byte reference stack // preallocated stack for this processor
4     integer thread_id // identity of thread currently running on this processor
5 shared structure thread_table[7] // each thread maintains the following information:
6     integer topstack // value of the stack pointer
7     integer state // runnable, running, or free
8     boolean kill_or_continue // terminate this thread? initialized to continue
9     byte reference stack // stack for this thread
10 procedure yield ()
11    acquire (thread_table_lock)
12    enter_processor_layer (get_thread_id(), cpuid) // See caption below!
13    release (thread_table_lock)
14    return
15
16 procedure scheduler ()
17     while shutdown 5 false do
18         acquire (thread_table_lock)
19         for i from 0 until 7 do
20             if thread_table[i].state 5 runnable then
21                 thread_table[i].state ← running
22                 processor_table[cpuid].thread_id ← i
23                 exit_processor_layer (cpuid, i)
24                 if thread_table[i].kill_or_continue 5 kill then
25                     thread_table[i].state ← free
26                     deallocate(thread_table[i].stack)
27                     thread_table[i].kill_or_continue 5 continue
28                 release (thread_table_lock)
29     return // Go shut down this processor
30 procedure enter_processor_layer (tid, processor)
31     thread_table[tid].state ← runnable
32     thread_table[tid].topstack ← sp // save state: store yielding’s thread sp
33     sp ← processor_table[processor].topstack // dispatch: load sp of processor thread
34     return
35 procedure exit_processor_layer (processor, tid) // transfers control to after line 14
36     processor_table[processor].topstack ← sp // save state: store processor thread’s sp
37     sp ← thread_table[tid].topstack // dispatch: load sp of thread
38     return
```

Using a separate processor thread, we find that switching a processor from one thread-layer thread to another actually requires two thread switches: one from the thread that is releasing its processor to the processor thread and one from the processor thread to the thread that is to receive the processor.

Now we can implement allocate_thread as follows:

1. Allocate space in memory for a new stack.
2. Place on the new stack an empty frame containing just a return address and initialize that return address with the address of exit_thread.
3. Place on the stack a second empty frame containing just a return address and initialize this return address with the address of starting_procedure.
4. Place on the stack a second empty frame containing just a return address and initialize this return address with the address of starting_procedure.
5. Set the state of newly created thread to runnable.

### 5.5.4 Enforcing Modularity with Threads: Preemptive Scheduling

    Supporting preemptive scheduling requires some changes to the thread manager because in the implementation described so far an interrupt handler shouldn’t invoke procedures in the thread layer at all, not even when the interrupt pertains to the currently running thread.

    The problem is that we have concurrent activity within the processor layer: the thread manager (i.e., yield) and the interrupt handler. The concurrent execution within the thread layer is coordinated with locks, but the processor needs its own mechanism. The processor may stop processing instructions of a thread at any time and switch to processing interrupt instructions.

    One solution to prevent the interrupt instruction stream from interfering with the processor instruction stream is to enable/disable interrupts. Disabling interrupts for a region greater than the region in which the thread_table_lock is set ensures that both streams are separate before-or-after actions. When a thread is about to acquire the thread_table_lock, it also disables interrupts on its processor. Now the processor will not switch to an interrupt handler when an interrupt arrives; interrupts are delayed until they are enabled again. After the thread has released the thread_table_lock, it is safe to reenable interrupts. Any pending interrupts will then execute immediately, but it is now safe since no thread on this processor can be inside the thread manager. This solution avoids the deadlock problem.

### 5.5.5 Enforcing Modularity with Threads and Address Spaces

    Thread manager, when it switches a processor from one thread to another, also switch the address space. That is, enter_processor_layer saves the contents of the processor’s pmar in the thread_table entry of the thread that is releasing the processor, and exit_processor_layer loads the processor’s pmar with the value in the thread_table entry of the new thread.

    Loading the pmar adds one significant complication to the thread manager: starting at the instant that the processor loads a new value into the pmar, the processor will translate virtual addresses using the new page table, so that it will take its next instruction from some location in the new virtual address space.

    One way to deal with this complication is to map both the instructions and the data of the thread manager into the same set of virtual addresses in every virtual address space.

### 5.5.6 Layering Threads

![Untitled](Lecture%204%20Bounded%20Buffers%20+%20Locks%20bff4dbc6249d405d9313da7645eff73b/Untitled.png)

    To support interrupts, we can think of a processor as a hard-wired thread manager with two threads: (1) a processor thread and (2) an interrupt thread that runs interrupt handlers in kernel mode. On an interrupt, a processor’s hard-wired thread manager switches from a processor thread to an interrupt thread that runs an interrupt handler in kernel mode, which may invoke a thread-layer exception handler that calls yield.

    The operating system thread layer uses the processor threads of the processor layer to implement a second layer of threads and gives each application module one or more preemptively scheduled virtual processors. When the operating system thread manager switches to another thread, it may also have to load the chosen thread’s pagemap address into the page-map address register to switch to the address space of the chosen thread. The operating system thread manager runs in kernel mode.
    Each application module in turn may implement, if it desires, its own, user-mode, third-layer thread manager using one or more virtual processors provided by the operating system layer. For example, some Web servers have an embedded Java interpreter to run Java programs, which may use several Java threads. To support threads at the Java level, the Java interpreter has its own thread manager. Typically, a third-layer thread manager uses non-preemptive scheduling because all threads belong to the same application module and don’t have to be protected from each other.

## 5.6 Thread primitives

## yield()

Command to tell kernel that thread is waiting for an event.

> Implementation does three things: Suspends running thread, chooses new thread to run, resumes new thread.
All of this happens as an atomic action.
> 

## Condition Variables

Allow kernel to notify threads instead of having threads constantly make checks.

> "Lost notify" problem
> 
> 
> Solution: API is wait(cv, lock)
> 

### Preemption

> If a thread never calls yield or wait, it’s okay; special hardware will periodically generate an interrupt and forcibly call yield.
> 

> But what if this interrupt occurs while the CPU is running yield()? Deadlock.
> 
> 
> Solution: Hardware mechanism to disable interrupts.
>