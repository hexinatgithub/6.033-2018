# Enforcing Modularity with Clients and Services

# Overview

One way to limit interactions between software modules is to organize systems as
clients and services. In the client/service organization, modules interact only by sending messages. This organization has three main benefits:

- Messages are the only way for a programmer to request that a module provide
a service. Limiting interactions to messages makes it more difficult for programmers to violate the modularity conventions.
- Messages are the only way for errors to propagate between modules. If clients
and services fail independently and if the client and the service check messages,
they may be able to limit the propagation of errors.
- Messages are the only way for an attacker to penetrate a module. If clients and
services carefully check the messages before they act on them, they can block
attacks.

## 4.1 Client/service organization

A standard way to create modularity in a large program is to divide it up into named procedures that call one another. Although the resulting structure can be called modular, implementation errors can propagate from caller to callee and vice versa, and not just through their specified interfaces. For example, if a programmer makes a mistake and introduces an infinite loop in a called procedure and the procedure never returns, then the callee will never receive control again. Or since the caller and callee are in the same address space and use the same stack, either one can accidentally store something in a space allocated to the other. For this reason, we identify this kind of modularity as soft. Soft modularity limits interactions of correctly implemented modules to their specified interfaces, but implementation errors can cause interactions that go outside the specified interfaces.

Soft modularity limits interactions of correctly implemented modules to their specified interfaces, but implementation errors can cause interactions that go outside the specified interfaces.

The client/service organization has two benefits: first, errors can propagate only with messages. Second, clients can check for certain errors by just considering the messages.

### 4.1.1 From Soft Modularity to Enforced Modularity

What we desire in systems is enforced modularity: modularity that is enforced by some external mechanism. This external mechanism limits the interaction among modules to the ones we desire. Such a limit on interactions reduces the number of opportunities for propagation of errors. It also allows verification that a user uses a module correctly, and it helps prevent an attacker from penetrating the security of a module.

### 4.1.2 Client/Service Organization

One good way to enforce modularity is to limit the interactions among modules to
explicit messages. It is convenient to impose some structure on this organization by
identifying participants in a communication as clients or services.

The disadvantage of this implementation is that it requires one computer per module, which may be costly in equipment. It may also have a performance cost because it may take a substantial amount of time to send a message from one computer to another, in particular if the computers are far away geographically.

The client/service organization not only separates functions (abstraction), it also
enforces that separation (enforced modularity). Compared to modularity using procedure calls, the client/service organization has the following advantages:

- The client and service don’t rely on shared state other than the messages. Therefore, errors can propagate from the client to the service, and vice versa, in only one way. If the services (as in line 15) and the clients check the validity of the request and response messages, then they can control the ways in which errors propagate. Since the client and service don’t rely on global, shared data structures such as a stack, a failure in the client cannot directly corrupt data in the service, and vice versa.
- The transaction between a client and a service is an arm’s-length transaction. Many errors cannot propagate from one to the other. For instance, the client does not have to trust the service to return to the appropriate return address, as it does using procedure calls. As another example, arguments and results are marshaled and unmarshaled, allowing the client and service to check them.
- The client can protect itself even against a service that fails to return because the client can put an upper limit on the time it waits for a response. As a result, if the service gets into an infinite loop, or fails and forgets about the request, the client can detect that something has gone wrong and undertake some recovery procedure, such as trying a different service. On the other hand, setting timers can create new problems because it can be difficult to predict how long a wait is reasonable.
- Client/Service organization encourages explicit, well-defined interfaces. Because the client and service can interact only through messages, the messages that a service is willing to receive provide a well-defined interface for the service. If those messages are well specified and their specification is public, a programmer can implement a new client or service without having to understand the internals of another client or the service. Clear specification allows clients and service to be implemented by different programmers, and can encourage competition for the best implementation.

Separating state and passing well-defined messages reduce the number of potential interactions, which helps contain errors. If the programmer who developed the service introduces an error and the service has a disaster, the client has only a controlled problem. The client’s only concern is that the service didn’t deliver its part of the contract; apart from this wrong or missing value, the client has no concern for its own integrity. The client is less vulnerable from faults in the service, or, in slightly different words, fate sharing can be reduced. Clients can be mostly independent of service
failures, and vice versa.

The client/service organization is an example of a sweeping simplification because the model eliminates all forms of interaction other than messages. By separating the client and the service from each other using message passing, we have created a firewall between them. As with firewalls in buildings, if there is a fire in the service, it will be contained in the service, and, assuming the client can check for flames in the response, it will not propagate to the client. If the client and service are well implemented, then the only way to go from the client to the service and back is through well-defined messages.

For ease of understanding, most of the examples in this chapter exhibit modules consisting of a single procedure. In the real world, designers usually apply the client/service organization between software modules of a larger granularity. The tendency toward larger granularity arises because the procedures within an application typically need to be tightly coupled for some practical reason, such as they all operate on the same shared data structure.

Another factor in whether or not to apply the client/service organization to two modules is the plan for recovery when the service module fails.

## 4.2 Communication between client and service

### 4.2.1 Remote Procedure Call (RPC)

RPCs come in many varieties, adding features to the basic request/response style of interaction. Some RPC systems, for example, simplify the programming of clients and services by hiding many the details of constructing and formatting messages.

Stubs remove this burden from the programmer. A stub is a procedure that hides the marshaling and communication details from the caller and callee.

### 4.2.2 RPCs are not Identical to Procedure Calls

However, RPCs are different from ordinary procedure calls in three important ways: First, RPCs can reduce fate sharing between caller and callee by exposing the failures of the callee to the caller so that the caller can recover. Second, RPCs introduce new failures that don’t appear in procedure calls. These two differences change the semantics of remote procedure calls as compared with ordinary procedure calls, and the changes usually require the programmer to make adjustments to the surrounding code. Third, remote procedure calls take more time than procedure calls; the number of instructions to invoke a procedure is much less than the cost of invoking a stub, marshaling arguments, sending a request over a network, invoking a service stub, unmarshaling arguments, marshaling the response, receiving the response over the network, and unmarshaling the response.

The second difference between ordinary procedure calls and RPCs is that RPCs introduce a new failure mode, the “no response” failure. When there is no response from a service, the client cannot tell which of two things went wrong: (1) some failure occurred before the service had a chance to perform the requested action, or (2) the service performed the action and then a failure occurred, causing just the response to be lost.

Most RPC designs handle the no-response case by choosing one of three implementation strategies:

- At-least-once RPC. If the client stub doesn’t receive a response within some specific time, the stub resends the request as many times as necessary until it receives a response from the service. An at-least-once implementation does not provide the guarantee implied by its name. For example, if the service was located in a building that has been blown away by a hurricane, retrying doesn’t help. To handle such cases, an at-least-once RPC implementation will give up after some number of retries. When that happens, the request may have been executed more than once or not at all.
- At-most-once RPC. If the client stub doesn’t receive a response within some specific time, then the client stub returns an error to the caller, indicating that the service may or may not have processed the request. At-most-once semantics may be more appropriate for requests that do have side effects.
- Exactly-once RPC. These semantics are the ideal, but because the client and service are independent it is in principle impossible to guarantee.

## 4.2.3 Communicating through an Intermediary

Once we have an intermediary, three interesting design opportunities arise. First, the sender and receiver may make different choices of whether to push or pull messages.

Second, the existence of an intermediary opens an opportunity to apply the design principle decouple modules with indirection by having the intermediary, rather than the originator, determine to whom a message is delivered.

Third, when indirection through an intermediary is available, the designer has a choice of when and where to duplicate messages.