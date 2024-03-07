# Recitation 9: Landmark Routing

> How does the landmark hierarchy differ from the area hierarchy? Are there places where they perform similarly, or make similar tradeoffs?
> 

Landmark hierarchy contrasted with the area hierarchy where a router on the border of an area may have full local information in the direction within the border, but virtually no local information in the direction across the border.

Both group network router into hierarchy. Saving memory overhead and in link overhead.

Area and Landmark hierarchy both produced not the shortest possible path.

> The landmark hierarchy was not used on the Internet. Why do you think that is?
> 

There are several possible reasons why the landmark hierarchy was not used on the Internet. Here are some of them:

- **Complexity**: The landmark hierarchy requires several algorithms to dynamically configure and maintain the landmarks, bind names to addresses, and accommodate administrative boundaries. These algorithms may be more complex and costly than the existing routing protocols used on the Internet, such as link-state and distance-vector.
- **Scalability**: The landmark hierarchy may not scale well to very large networks with diverse topologies and policies. The performance of the landmark hierarchy depends on the ratio of the landmark radius to the distance to the nearest landmark, which may vary widely across different regions and domains. The landmark hierarchy may also suffer from frequent changes in the network topology and address space, which may cause instability and inconsistency in the routing tables and name bindings.
- **Compatibility**: The landmark hierarchy may not be compatible with the existing addressing and routing schemes used on the Internet, such as IP addresses and autonomous systems. The landmark hierarchy uses a hierarchical address structure based on the proximity to landmarks, which may not reflect the actual network topology or administrative boundaries. The landmark hierarchy may also require changes in the network infrastructure and protocols to support the discovery and advertisement of landmarks, as well as the resolution of names to addresses.

> What problem(s) is the landmark hierarchy addressing?
> 

The landmark hierarchy is addressing the problem of routing in large and dynamic networks. Some of the challenges that the landmark hierarchy aims to solve are:

- **Efficient and automatic operation**: The landmark hierarchy can be dynamically configured using a distributed algorithm, thus allowing for scalability and adaptability to changing network conditions.
- **Name-based addressing and mobility**: The landmark hierarchy provides a mechanism for binding permanent names or IDs to changing addresses, thus accommodating any percentage of mobile nodes and easing network administration.
- **Administrative boundaries and control**: The landmark hierarchy allows for defining administrative boundaries, and providing control of routing paths, protection, and autonomy for different network domains.

> How does it work?
> 

Use hierarchy management algorithm to divide network into hierarchy. Each router is at least an $LM_0$ upon birth, chooses an $r_0$, and advertises itself that distance through the routing algorithm. As such, each $LM_0$ hears of peer $LM_0$’s, and possibly higher level Landmarks. If it hears an $LM_1$, it can graft itself onto the hierarchy under that $LM_1$, and shrink its radius accordingly. If it doesn’t, it can run an election with peers to build up the hierarchy.

Use distance-vector routing algorithm to establish paths to Landmarks it must be known. The modification needed to distance-vector protocol is that advertisement message not passed to any node beyond the landmark’s radius.

Use Assured Destination Binding (ADB) to bind names or IDs to changing addresses.

Allow recursive construct Landmark hierarchy and accommodate administrative control.

> Why do you think the landmark hierarchy wasn’t used on the Internet? (There are many reasons, but it is not because the ideas presented in the paper are inherently bad)
> 

There are several possible reasons why the landmark hierarchy was not used on the Internet. Here are some of them:

- **Complexity**: The landmark hierarchy requires several algorithms to dynamically configure and maintain the landmarks, bind names to addresses, and accommodate administrative boundaries. These algorithms may be more complex and costly than the existing routing protocols used on the Internet, such as link-state and distance-vector.
- **Scalability**: The landmark hierarchy may not scale well to very large networks with diverse topologies and policies. The performance of the landmark hierarchy depends on the ratio of the landmark radius to the distance to the nearest landmark, which may vary widely across different regions and domains. The landmark hierarchy may also suffer from frequent changes in the network topology and address space, which may cause instability and inconsistency in the routing tables and name bindings.
- **Compatibility**: The landmark hierarchy may not be compatible with the existing addressing and routing schemes used on the Internet, such as IP addresses and autonomous systems. The landmark hierarchy uses a hierarchical address structure based on the proximity to landmarks, which may not reflect the actual network topology or administrative boundaries. The landmark hierarchy may also require changes in the network infrastructure and protocols to support the discovery and advertisement of landmarks, as well as the resolution of names to addresses.