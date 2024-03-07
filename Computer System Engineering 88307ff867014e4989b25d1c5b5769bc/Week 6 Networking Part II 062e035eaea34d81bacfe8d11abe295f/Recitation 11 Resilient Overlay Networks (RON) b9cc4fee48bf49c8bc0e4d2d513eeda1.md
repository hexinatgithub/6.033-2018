# Recitation 11: Resilient Overlay Networks (RON)

> Why is RON able to overcome failures that BGP can’t?
> 

RON nodes exchange information about the quality of the paths among themselves via a routing protocol and build forwarding tables based on a variety of path metrics, including latency, packet loss rate, and available throughput. Each RON node obtains the path metrics using a combination of active probing experiments and passive observations of on-going data transfers. This  allows RON to recover from problems in the underlying Internet in several seconds rather than several minutes.

> Why does RON collect different application metrics?
> 

Second goal of RON is to integrate routing and path selection with distributed applications more tightly than is traditionally done. This integration includes the ability to consult application-specific metrics in selecting paths, and the ability to incorporate application-specific notions of what network conditions constitute a “fault.”

> How far does RON scale?
> 

Two and fifty nodes.

> Routing is normally done at the network layer, but RON (and BGP) operate at the application layer. What are the benefits and drawbacks of this change?
> 

In summary, operating at the application layer provides RON with more flexibility and the ability to optimize routing based on application-specific requirements. However, this comes at the cost of increased complexity and potential security vulnerabilities

> What is the goal of RON?
> 

The main goal of RON is to enable a group of nodes to communicate with each other in the face of problems with the underlying Internet paths connecting them. RON detects problems by aggressively probing and monitoring the paths connecting its nodes. If the underlying Internet path is the best one, that path is used and no other RON node is involved in the forwarding path. If the Internet path is not the best one, the RON will forward the packet by way of other RON nodes.

The second goal of RON is to integrate routing and path selection with distributed applications more tightly than is traditionally done.

The third goal of RON is to provide a framework for the implementation of expressive routing policies, which govern the choice of paths in the network.

> How was it designed to meet this goal?
> 

The default RON router uses a link-state routing protocol to disseminate topology information between routers, which in turn is used to build the forwarding tables.

Store probe collected metrics to performance database and to compute forwarding tables.

Use the data classifier module would determine which policy to use and set the corresponding tag on the packet.

Policy tag that is interpreted by the forwarders to decide which network routing policies apply to the packet.

> Why do we need RON? (Or why do the authors believe that we need RON?)
> 

This paper showed that a Resilient Overlay Network (RON) can greatly improve the reliability of Internet packet delivery by detecting and recovering from outages and path failures more quickly than current inter-domain routing protocols. A RON works by deploying nodes in different Internet routing domains, which cooperatively route packets for each other. Each RON is an application-layer overlay network; nodes in a RON monitor the quality of the underlying Internet between themselves and use this information to route packets according to application-specified routing metrics either via a direct Internet path or by way of other RON nodes.