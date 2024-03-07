# Recitation 12: Data Center Transmission Control Protocol (DCTP)

> Would DCTCP work on the Internet?
> 

No. 

Round trip times (RTTs) can be less than 250µs, in absence of queuing. Applications simultaneously need extremely high bandwidths and very low latencies. Often, there is little statistical multiplexing: a single flow can dominate a particular path. At the same time, the data center environment offers certain luxuries. The network is largely homogeneous and under a single administrative control. Thus, backward compatibility, incremental deployment and fairness to legacy protocols are not major concerns. Connectivity to the external Internet is typically managed through load balancers and application proxies that effectively separate internal traffic from external, so issues of fairness with conventional TCP are irrelevant.

> Is there a trade-off between the generality of a protocol and its performance?
> 

DCTCP trades off convergence time; the time required for a new flow to grab its share of the bandwidth from an existing flow with a large window size.

> What is the goal of DCTCP?
> 

Low latency for short flows, high burst tolerance, and high utilization for long flows.

> How does DCTCP differ from TCP?
> 

DCTCP achieves these goals primarily by reacting to congestion in proportion to the extent of congestion. DCTCP uses a simple marking scheme at switches that sets the Congestion Experienced (CE) codepoint of packets as soon as the buffer occupancy exceeds a fixed small threshold. The DCTCP source reacts by reducing the window by a factor that depends on the fraction of marked packets: the larger the fraction, the bigger the decrease factor.

> Why does DCTCP differ from TCP?
> 

Incast: arises naturally from use of the Partition/Aggregate design pattern, as the request for data synchronizes the workers’ responses and creates incast at the queue of the switch port connected to the aggregator.

Queue buildup: the short flows experience increased latency as they are in queue behind packets from the large flows.

Buffer pressure: The long, greedy TCP flows build up queues on their interfaces. Since buffer space is a shared resource, the queue build up reduces the amount of buffer space available to absorb bursts of traffic from Partition/Aggregate traffic.