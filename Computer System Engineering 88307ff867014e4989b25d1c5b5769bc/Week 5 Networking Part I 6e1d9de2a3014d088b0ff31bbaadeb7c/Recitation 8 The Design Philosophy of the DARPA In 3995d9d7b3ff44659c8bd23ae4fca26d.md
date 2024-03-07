# Recitation 8: The Design Philosophy of the DARPA Internet Protocols

> Why did the Internet architects decide to divide TCP and IP into two separate protocols?
> 

It was thus decided, fairly early in the development of the Internet architecture, that more than one transport service would be required, and the architecture must be prepared to tolerate simultaneously transports which wish to constrain reliability, delay, or bandwidth, at a minimum.

The second goal of the Internet architecture is that it should support, at the transport service level, a variety of types of service. Different types of service are distinguished by differing requirements for such things as speed, latency and reliability.

> How do datagrams help the Internet achieve two of its goals: to connect existing networks and to survive transient failures?
> 

The Internet architecture achieves this flexibility by making a minimum set of assumptions about the function which the net will provide. The basic assumption is that network can transport a packet or datagram. So it be able to  incorporate and utilize a wide variety of network technologies, including military and commercial facilities.

Use the fate-sharing which store state information of the on-going conversation must be protected at the endpoint of the net.

> If you could start from scratch, how would you redesign the Internet today? Would you keep the same principles but change their order? Would you use new principles?
> 

The Internet should be secure by default. This means that all communications should be encrypted and authenticated to prevent eavesdropping and tampering.

Keep the same principles and order. Add security goals is very important.

> What were three of the most important goals of the early Internet?
> 

Survivability in the Face of Failure, support a variety of types of service and incorporate and utilize a wide variety of network technologies.

> How was it designed to meet those goals?
> 

The fundamental architectural feature of the Internet is the use of datagrams as the entity which is transported across the underlying networks. As this paper has suggested, there are several reasons why datagrams are important within the architecture. First, they eliminate the need for connection state within the intermediate switching nodes, which means that the Internet can be reconstituted after a failure without concern about state. Secondly, the datagram provides a basic building block out of which a variety of types of service can be implemented. In contrast to the virtual circuit, which usually implies a fixed type of service, the datagram provides a more elemental service which the endpoints can combine as appropriate to build the type of service needed. Third, the datagram represents the minimum network service assumption, which has permitted a wide variety of networks to be incorporated into various Internet realizations. The decision to use the datagram was an extremely successful one, which allowed the Internet to meet its most important goals very successfully.

> Why were those goals important (or, why does the author believe that those goals were important)?
> 

Network was designed to operate in a military context, which implied the possibility of a hostile environment, survivability was put as a first goal, and accountability as a last goal.

Different types of service are distinguished by differing requirements for such things as speed, latency and reliability. The architecture did not wish to assume that the underlying networks themselves support multiple types of services, because this would violate the goal of using existing networks.

Integrating a number of separately administrated entities into a common utility.