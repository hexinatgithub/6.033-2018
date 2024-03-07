# Lecture 10: Networking: Routing (BGP)

# 1 Autonomous Systems

    Network attachment points for Internet hosts are identified using IP addresses, which are 32-bit (in IPv4) numbers. The fundamental reason for the scalability of the Internet’s network layer is its use of topological addressing. Unlike an Ethernet address that is location-independent and always identifies a host network interface independent of where it is connected in the network, an IP address of a connected network interface depends on its location in the network topology. Topological addressing allows routes to Internet destinations to be aggregated in the forwarding tables of the routers (as a simple example of aggregation, any IP address of the form 18.* in the Internet is in MIT’s network, so external routers need only maintain a routing entry for 18.* to correctly forward packets to MIT’s network), and allows routes to be summarized and exchanged by the routers participating in the Internet’s routing protocols. In the absence of aggressive aggregation, there would be no hope for scaling the routing system to hundreds of millions of hosts connected over tens of millions of networks located in tens of thousands of ISPs and organizations.

    Route, which we define as a mapping from an IP prefix (a range of addresses) to a link, with the property that packets for any destination within that prefix may be sent along the corresponding link.

    A key difference between BGP and IGPs is that the former is concerned with exchanging reachability information between ASes in a scalable manner while allowing each AS to implement autonomous routing policies, whereas the latter are typically concerned with optimizing a path metric. In general, IGPs don’t scale as well as BGP does with respect to the number of participants involved.

# 2 Inter-AS Relationships: Transit and Peering

There are two prevalent forms of AS-AS interconnection.

Provider-customer transit (aka “transit”), wherein one ISP (the “provider” P in Figure 3) provides access to all (or most) destinations in its routing tables. Transit almost always is meaningful in an inter-AS relationship where financial settlement is involved; the provider charges its customers for Internet access, in return for forwarding packets on behalf of customers to destinations (and in the opposite direction in many cases).

Peering: two ASes (typically ISPs) provide mutual access to a subset of each other’s routing tables. The subset of interest here is their own transit customers (and the ISPs own internal addresses). Like transit, peering is a business deal, but it may not involve financial settlement. While paid peering is common in some parts of the world, in many cases they are reciprocal agreements.

## 2.2 Exporting Routes to Make or Save Money

Transit customer routes: To an ISP, its customer routes are likely the most important, because the view it provides to its customers is the sense that all potential senders in the Internet can reach them. It is in the ISP’s best interest to advertise routes to its transit customers to as many other connected ASes as possible. The more traffic that an ISP carries on behalf of a customer, the “fatter” the pipe that the customer would need, implying higher revenue for the ISP. Hence, if a destination were advertised from multiple neighbors, an ISP should prefer the advertisement made from a customer over all other choices (in particular, over peers and transit providers).

Transit provider routes: Does an ISP want to provide transit to the routes exported by its provider to it? Most likely not, because the ISP isn’t making any money on providing such transit facilities.

Peer routes: It usually makes sense for an ISP to export only selected routes from its routing tables to other peering ISPs. It obviously makes sense to export routes to all of ones transit customers. It also makes sense to export routes to addresses within an ISP. However, it does not make sense to export an ISP’s transit provider routes to other peering ISPs, because that may cause a peering ISP to use the advertising ISP to reach a destination advertised by a transit provider. Doing so would expend ISP resources but not lead to revenue.

## 2.3 Importing Routes to Make or Save Money

Typically routes are imported in the following priority order:

<aside>
💡 customer > peer > provider

</aside>

## 2.4 Routing Policy = Ranking + Filtering

Network operators express a wide range of routing policies, but to first approximation, most of them can be boiled down to ranking decisions and export filters.

# 3 BGP

## 3.1 Design Goals

The design of BGP was motivated by three important needs:

1. Scalability. The division of the Internet into ASes under independent administration was done while the backbone of the then Internet was under the administration of the NSFNet. When the NSFNet was “turned off” in the early 1990s and the Internet routing infrastructure opened up to competition in the US, a number of ISPs providing different sizes sprang up. The growth in the number of networks (and hosts) has continued to date. To support this growth, routers must be able to handle an increasing number of prefixes, BGP must ensure that the amount of advertisement traffic scales well with “churn” in the network (parts of the network going down and coming up), and BGP must converge to correct loop-free paths within a reasonable amount of time after any change. Achieving these goals is not easy.
2. Policy. The ability for each AS to implement and enforce various forms of routing policy was an important design goal. One of the consequences of this was the development of the BGP attribute structure for route announcements, allowing route filtering, and allowing each AS to rank its available routes arbitrarily.
3. Cooperation under competitive circumstances. BGP was designed in large part to handle the transition from the NSFNet to a situation where the “backbone” Internet infrastructure would no longer be run by a single administrative entity. This structure implies that the routing protocol should allow ASes to make purely local decisions on how to route packets, from among any set of choices. Moreover, BGP was designed to allow each AS to keep its ranking and filtering policies confidential from other ASes.

## 3.2 Protocol Details

    BGP runs over TCP on a well-known port (179). To start participating in a BGP session with another router, a router sends an OPEN message after establishing a TCP connection to it on the BGP port. After the OPEN is completed, both routers exchange their tables of all active routes (of course, applying all applicable route filtering rules). Each router then integrates the information obtained from its neighbor into its routing table. The entire process may take many seconds to a few minutes to complete, especially on sessions that have a large number of active routes.

    After this initialization, there are two main types of messages on the BGP session. First, BGP routers send route UPDATE messages sent on the session. These updates only send any routing entries that have changed since the last update (or transmission of all active routes). There are two kinds of updates: announcements, which are changes to existing routes or new routes, and withdrawals, which are messages that inform the receiver that the named routes no longer exist. A withdrawal usually happens when some previously announced route can no longer be used (e.g., because of a failure or a change in policy). Because BGP uses TCP, which provides reliable and in-order delivery, routes do not need to be periodically announced, unless they change.

## 3.3 eBGP and iBGP

    There are two types of BGP sessions: eBGP sessions are between BGP-speaking routers in different ASes, while iBGP sessions are between BGP routers in the same AS. They serve different purposes, but use the same protocol.

    In general, each AS will have more than one router that participates in eBGP sessions with neighboring ASes. During this process, each router will obtain information about some subset of all the prefixes that the entire AS knows about. Each such eBGP router must disseminate routes to the external prefix to all the other routers in the AS. This dissemination must be done with care to meet two important goals:

1. Loop-free forwarding. After the dissemination of eBGP learned routes, the resulting routes
(and the subsequent forwarding paths of packets sent along those routes) picked by all routers
should be free of deflections and forwarding loops.
2. Complete visibility. One of the goals of BGP is to allow each AS to be treated as a single monolithic entity. This means that the several eBGP-speaking routes in the AS must exchange external route information so that they have a complete view of all external routes. By “complete visibility”, we mean the following: for every external destination, each router picks the same route that it would have picked had it seen the best routes from each eBGP router in the AS.

## 3.4 Key BGP Attributes

### 3.4.1 Exchanging Reachability: NEXT HOP Attribute

    A BGP route announcement has a set of attributes associated with each announced prefix. One of them is the NEXT HOP attribute, which gives the IP address of the router to send the packet to. As the announcement propagates across an AS boundary, the NEXT HOP field is changed; typically, it gets changed to the IP address of the border router of the AS the announcement came from.

    The above behavior is for eBGP speakers. For iBGP speakers, the first router that introduces the route into iBGP sets the NEXT HOP attribute to its so-called loopback address (the address that all other routers within the AS can use to reach the first router). All the other iBGP routers within the AS preserve this setting, and use the ASes IGP to route any packets destined for the route (in the reverse direction of the announcement) toward the NEXT HOP IP address. In general, packets destined for a prefix flow in the opposite direction to the route announcements for the prefix.

### 3.4.2 Length of AS Paths: ASPATH Attribute

    Another attribute that changes as a route annoucement traverses different ASes is the ASPATH attribute, which is a vector that lists all the ASes (in reverse order) that this route announcement has been through. Upon crossing an AS boundary, the first router prepends the unique identifier of its own AS and propagates the announcement on (subject to its route filtering rules). This use of a “path vector”—a list of ASes per route—is the reason BGP is classified as a path vector protocol.

    A path vector serves two purposes. The first is loop avoidance. Upon crossing an AS boundary, the router checks to see if its own AS identifier is already in the vector. If it is, then it discards the route announcement, since importing this route would simply cause a routing loop when packets are forwarded.

    The second purpose of the path vector is to help pick a suitable path from among multiple choices. If no LOCAL PREF is present for a route, then the ASPATH length is used to decide on the route. Shorter ASPATH lengths are preferred to longer ones. However, it is important to remember that BGP isn’t a strict shortest-ASPATH protocol (classical path vector protocols would pick shortest vectors), since it pays attention to routing policies. The LOCAL PREF attribute is always given priority over ASPATH. Many routes in practice, though, end up being picked according to shortest-ASPATH.