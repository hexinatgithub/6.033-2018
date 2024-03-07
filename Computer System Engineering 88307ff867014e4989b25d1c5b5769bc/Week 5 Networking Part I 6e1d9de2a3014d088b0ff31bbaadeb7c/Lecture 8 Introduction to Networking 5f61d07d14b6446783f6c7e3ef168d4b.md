# Lecture 8: Introduction to Networking

## 7.2 Getting Organized: Layers

> The end-to-end argument
*The application knows best.*
> 

    In this case, the basic thrust of the end-to-end argument is that the application knows best what its real communication requirements are, and for a lower network layer to try to implement any feature other than transporting the data risks implementing something that isn’t quite what the application needed. Moreover, if it isn’t exactly what is needed, the application will probably have to reimplement that function on its own. The end-to-end argument can thus be paraphrased as: don’t bury it in a lower layer, let the end points deal with it because they know best what they need.

### 7.2.6 Mapped and Recursive Applications of the Layered Model

    Mapped composition occurs when a network layer is built directly on another network layer by mapping higher-layer network addresses to lower-layer network addresses. A typical application for mapping arises when a better or more popular network technology comes along, yet it is desirable to keep running applications that are designed for the old network.

    Another application for mapped composition is to interconnect several independently designed network layers, a scheme called internetworking.

    Recursive composition occurs when a network layer rests on a link layer that itself is a complete three-layer network. Recursive composition is not a general property of layers, but rather it is a specific property of layered communication systems: The send/receive semantics of an end-to-end connection through a network can be designed to be have the same semantics as a single link, so such an end-to-end connection can be used as a link in a higher-level network. That property facilitates recursive composition, as well as the implementation of various interesting and useful network structures.