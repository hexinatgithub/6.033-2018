# Eraser: A Dynamic Data Race Detector for Multithreaded Programs

# 1. INTRODUCTION

## 1.1 Definitions

    A lock is a simple synchronization object used for mutual exclusion; it is either available, or owned by a thread. The operations on a lock mu are lock(mu) and unlock(mu). Thus it is essentially a binary semaphore used for mutual exclusion, but differs from a semaphore in that only the owner of a lock is allowed to release it.

A data race occurs when two concurrent threads access a shared variable and when

- at least one access is a write and
- the threads use no explicit mechanism to prevent the accesses from being simultaneous

    If a program has a potential data race, then the effect of the conflicting accesses to the shared variable will depend on the interleaving of the thread executions. Although programmers occasionally deliberately allow a data race when the nondeterminism seems harmless, usually a potential data race is a serious error caused by failure to synchronize properly.

# 2. THE LOCKSET ALGORITHM

    For each shared variable v, Eraser maintains the set C(v) of candidate locks for v. This set contains those locks that have protected v for the computation so far. That is, a lock l is in C(v) if, in the computation up to that point, every thread that has accessed v was holding l at the moment of the access. When a new variable v is initialized, its candidate set C(v) is considered to hold all possible locks. When the variable is accessed, Eraser updates C(v) with the intersection of C(v) and the set of locks held by the current thread. This process, called lockset refinement, ensures that any lock that consistently protects v is contained in C(v). If some lock l consistently protects v, it will remain in C(v) as C(v) is refined. If C(v) becomes empty this indicates that there is no lock that consistently protects v.

In summary, here is the first version of the Lockset algorithm:

```
Let locks held(t) be the set of locks held by thread t.
For each v, initialize C(v) to the set of all locks.
On each access to v by thread t,
    set C(v) := C(v) ∩ locks_held(t);
    if C(v) = { }, then issue a warning.
```

## 2.2 Initialization and Read-Sharing

![Untitled](Eraser%20A%20Dynamic%20Data%20Race%20Detector%20for%20Multithrea%209f11a650c87b49afb9952a99bc727757/Untitled.png)

    When a variable is first allocated, it is set to the Virgin state, indicating that the data are new and have not yet been referenced by any thread. Once the data are accessed, it enters the Exclusive state, signifying that it has been accessed, but by one thread only. In this state, subsequent reads and writes by the same thread do not change the variable’s state and do not update C(v). This addresses the initialization issue, since the first thread can initialize the variable without causing C(v) to be refined. When and if another thread accesses the variable, then the state changes. A read access changes the state to Shared. In the Shared state, C(v) is updated, but data races are not reported, even if C(v) becomes empty. This takes care of the read-shared data issue, since multiple  threads can read a variable without causing a race to be reported. A write access from a new thread changes the state from Exclusive or Shared to the Shared-Modified state, in which C(v) is updated and races are reported, just as described in the original, simple version of the algorithm.

## 2.3 Read-Write Locks

```
Let locks_held(t) be the set of locks held in any mode by thread t.
Let write_locks held(t) be the set of locks held in write mode by thread t.
For each v, initialize C(v) to the set of all locks.
On each read of v by thread t,
    set C(v) := C(v) ∩ locks held(t);
    if C(v) := { }, then issue a warning.
On each write of v by thread t,
    set C(v) := C(v) ∩ write locks held(t);
    if C(v) = { }, then issue a warning.
```

    Locks held purely in read mode are removed from the candidate set when a write occurs, as such locks held by a writer do not protect against a data race between the writer and some other reader thread.

# **Eraser Assignment**

> Why can’t the lockset algorithm catch every race condition?
> 

Initialization, Read-Shared Data, Read-Write Locks are free from any data races.

> Would you use Eraser? If so, in what situations?
> 

Dynamically detects data races in multithreaded programs. Help write correctness multithreaded programs.

> What are the goals of Eraser?
> 

Eraser is a tool that dynamically detects data races in multithreaded programs that use lock-based synchronization. The goals of Eraser are to help developers easily find and correct concurrency bugs, and to check that each shared memory access follows a consistent locking discipline.

> How was it designed to meet those goals?
> 

According to the web page context, Eraser was designed to meet the following goals:

- **Dynamic**: Eraser monitors the execution of multithreaded programs and detects data races at runtime, rather than statically analyzing the source code or requiring annotations.
- **Precise**: Eraser uses a lockset algorithm that tracks the set of locks that protect each shared variable and reports a race when a variable is accessed with an empty lockset.
- **Practical**: Eraser is implemented as a software library that can be linked with any program that uses POSIX threads, without requiring any modifications to the program or the compiler. Eraser also has low overhead and few false positives.

> Why do we need a tool like Eraser? (Or why do the authors believe that we need such a tool?)
> 

Take the advantages of enforcing a simple locking discipline instead of checking for races in general parallel programs that employ many different synchronization primitives and has demonstrated that with this technique it is practical to dynamically check production multithreaded programs for data races.