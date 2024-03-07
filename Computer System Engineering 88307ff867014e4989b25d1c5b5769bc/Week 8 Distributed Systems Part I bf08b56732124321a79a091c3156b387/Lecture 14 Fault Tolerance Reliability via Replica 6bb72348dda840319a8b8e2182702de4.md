# Lecture 14: Fault Tolerance: Reliability via Replication

Construction of reliable systems from unreliable components is one of the most important applications of modularity. There are, in principle, three basic steps to building reliable systems:

1. Error detection: discovering that there is an error in a data value or control signal. Error detection is accomplished with the help of redundancy, extra information that can verify correctness.
2. Error containment: limiting how far the effects of an error propagate. Error containment comes from careful application of modularity. When discussing reliability, a module is usually taken to be the unit that fails independently of other such units. It is also usually the unit of repair and replacement.
3. Error masking: ensuring correct operation despite the error. Error masking is accomplished by providing enough additional redundancy that it is possible to discover correct, or at least acceptably close, values of the erroneous data or control signal. When masking involves changing incorrect values to correct ones, it is usually called error correction.

## 8.1 Faults, Failures, and Fault Tolerant Design

### 8.1.1 Faults, Failures, and Modules

From the point of view of the next higher-level subsystem, the subsystem with the error may contain the error in one of four ways:

1. Mask the error, so the higher-level subsystem does not realize that anything went wrong. One can think of failure as falling off a cliff and masking as a way of providing some separation from the edge.
2. Detect and report the error at its interface, producing what is called a fail-fast design. Fail-fast subsystems simplify the job of detection and masking for the next higher-level subsystem. If a fail-fast module correctly reports that its output is questionable, it has actually met its specification, so it has not failed. (Fail-fast modules can still fail, for example by not noticing their own errors.)
3. Immediately stop dead, thereby hoping to limit propagation of bad values, a technique known as fail-stop. Fail-stop subsystems require that the higher-level subsystem take some additional measure to discover the failure, for example by setting a timer and responding to its expiration. A problem with fail-stop design is that it can be difficult to distinguish a stopped subsystem from one that is merely running more slowly than expected. This problem is particularly acute in asynchronous systems.
4. Do nothing, simply failing without warning. At the interface, the error may have contaminated any or all output values. (Informally called a “crash” or perhaps “fail-thud”.)

### 8.1.2 The Fault-Tolerance Design Process

Here is an overview of the fault-tolerance design process:

1. Begin to develop a fault-tolerance model, as described in Section 8.3:
    - Identify every potential fault.
    - Estimate the risk of each fault, as described in Section 8.2.
    - Where the risk is too high, design methods to detect the resulting errors.
2. Apply modularity to contain the damage from the high-risk errors.
3. Design and implement procedures that can mask the detected errors, using the techniques described in Section 8.4:
    1. Temporal redundancy. Retry the operation, using the same components.
    2. Spatial redundancy. Have different components do the operation.
4. Update the fault-tolerance model to account for those improvements.
5. Iterate the design and the model until the probability of untolerated faults is low enough that it is acceptable.
6. Observe the system in the field:
    - Check logs of how many errors the system is successfully masking. (Always keep track of the distance to the edge of the cliff.)
    - Perform postmortems on failures and identify all of the reasons for each failure.
7. Use the logs of masked faults and the postmortem reports about failures to revise and improve the fault-tolerance model and reiterate the design.

The fault-tolerance design process can be described as a safety-net approach to system design.

> Be explicit
Get all of the assumptions out on the table.
> 

The primary purpose of creating a fault-tolerance model is to expose and document the assumptions and articulate them explicitly. The designer needs to have these assumptions not only for the initial design, but also in order to respond to field reports of unexpected failures. Unexpected failures represent omissions or violations of the assumptions.

> design for iteration
a fault tolerant design must include feedback about actual error rates, evaluation of that feedback, and update of the design as field experience is gained.
> 

> the safety margin principle
> 
> 
> When fault tolerant systems fail, it is usually not because they had inadequate fault tolerance, but because the number of failures grew unnoticed until the fault tolerance of the design was exceeded.
> 

> keep digging
> 
> 
> Complex systems fail for complex reasons. When a failure of a system that is supposed to be reliable does occur, always look beyond the first, obvious cause. It is nearly always the case that there are actually several contributing causes and that there was something about the mind set of the designer that allowed each of those causes to creep in to the design.
> 

> adopt sweeping simplifications
complexity increases the chances of mistakes, so it is an enemy of reliability.
> 

## 8.2 Measures of Reliability and Failure Tolerance

### 8.2.1 Availability and Mean Time to Failure

### 8.2.3 Measuring Fault Tolerance

    It is sometimes useful to have a quantitative measure of the fault tolerance of a system. One common measure, sometimes called the failure tolerance, is the number of failures of its components that a system can tolerate without itself failing.

    When a failure occurs, the remaining failure tolerance of the system goes down. The remaining failure tolerance is an important thing to monitor during operation of the system because it shows how close the system as a whole is to failure. One of the most common system design mistakes is to add fault tolerance but not include any monitoring to see how much of the fault tolerance has been used up, thus ignoring the safety margin principle. When systems that are nominally fault tolerant do fail, later analysis invariably discloses that there were several failures that the system successfully masked but that somehow were never reported and thus were never repaired. Eventually, the total number of failures exceeded the designed failure tolerance of the system.

## 8.3 Tolerating Active Faults

### 8.3.1 Responding to Active Faults

In dealing with active faults, the designer of a module can provide one of several responses:

- Do nothing. The error becomes a failure of the module, and the larger system or subsystem of which it is a component inherits the responsibilities both of discovering and of handling the problem. The designer of the larger subsystem then must choose which of these responses to provide. In a system with several layers of modules, failures may be passed up through more than one layer before being discovered and handled. As the number of do-nothing layers increases, containment generally becomes more and more difficult.
- Be fail-fast. The module reports at its interface that something has gone wrong. This response also turns the problem over to the designer of the next higher-level system, but in a more graceful way. Example: when an Ethernet transceiver detects a collision on a frame it is sending, it stops sending as quickly as possible, broadcasts a brief jamming signal to ensure that all network participants quickly realize that there was a collision, and it reports the collision to the next higher level, usually a hardware module of which the transceiver is a component, so that the higher level can consider resending that frame.
- Be fail-safe. The module transforms any value or values that are incorrect to values that are known to be acceptable, even if not right or optimal. An example is a digital traffic light controller that, when it detects a failure in its sequencer, switches to a blinking red light in all directions. Chapter 11 discusses systems that provide security. In the event of a failure in a secure system, the safest thing to do is usually to block all access. A fail-safe module designed to do that is said to be fail-secure.
- Be fail-soft. The system continues to operate correctly with respect to some predictably degraded subset of its specifications, perhaps with some features missing or with lower performance. For example, an airplane with three engines can continue to fly safely, albeit more slowly and with less maneuverability, if one engine fails. A file system that is partitioned into five parts, stored on five different small hard disks, can continue to provide access to 80% of the data when one of the disks fails, in contrast to a file system that employs a single disk five times as large.
- Mask the error. Any value or values that are incorrect are made right and the module meets it specification as if the error had not occurred.

As a general rule, one can design algorithms and procedures to cope only with specific, anticipated faults. Further, an algorithm or procedure can be expected to cope only with faults that are actually detected. Thus when trying to determine if a system design has adequate fault tolerance, it is helpful to classify errors as follows:

- A detectable error is one that can be detected reliably. If a detection procedure is in place and the error occurs, the system discovers it with near certainty and it becomes a detected error.
- A maskable error is one for which it is possible to devise a procedure to recover correctness. If a masking procedure is in place and the error occurs, is detected, and is masked, the error is said to be tolerated.
- Conversely, an untolerated error is one that is undetectable, undetected, unmaskable, or unmasked.

### 8.3.2 Fault Tolerance Models

1. Analyze the system and categorize possible error events into those that can be reliably detected and those that cannot. At this stage, detectable or not, all errors are untolerated.
2. For each undetectable error, evaluate the probability of its occurrence. If that probability is not negligible, modify the system design in whatever way necessary to make the error reliably detectable.
3. For each detectable error, implement a detection procedure and reclassify the module in which it is detected as fail-fast.
4. For each detectable error try to devise a way of masking it. If there is a way, reclassify this error as a maskable error.
5. For each maskable error, evaluate its probability of occurrence, the cost of failure, and the cost of the masking method devised in the previous step. If the evaluation indicates it is worthwhile, implement the masking method and reclassify this error as a tolerated error.

Some errors, which have negligible probability of occurrence or for which a masking measure would be too expensive, are identified as untolerated. When those errors occur the system fails, leaving its users to cope with the result. Other errors have specified recovery algorithms, and when those occur the system should continue to run correctly. A review of the system recovery strategy can now focus separately on two distinct questions:

- Is the designer’s list of potential error events complete, and is the assessment of the probability of each error realistic?
- Is the designer’s set of algorithms, procedures, and implementations that are supposed to detect and mask the anticipated errors complete and correct?