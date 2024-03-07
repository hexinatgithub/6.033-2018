# Lecture 20: Introduction to Security

# Overview

To achieve security, the system must obtain trustworthy answers to the following three questions before performing the requested action:

1. Authenticity: Is the agent’s claimed identity authentic? (Or, is someone masquerading as the agent?)
2. Integrity: Is this request actually the one the agent made? (Or, did someone tamper with it?)
3. Authorization: Has a proper authority granted permission to this agent to perform this action?

## 11.1 Introduction to Secure Systems

### 11.1.1 Threat Classification

There are three broad categories of threats:

1. Unauthorized information release: an unauthorized person can read and take advantage of information stored in the computer or being transmitted over networks. This category of concern sometimes extends to “traffic analysis,” in which the adversary observes only the patterns of information use and from those patterns can infer some information content.
2. Unauthorized information modification: an unauthorized person can make changes in stored information or modify messages that cross a network—an adversary might engage in this behavior to sabotage the system or to trick the receiver of a message to divulge useful information or take unintended action. This kind of violation does not necessarily require that the adversary be able to see the information it has changed.
3. Unauthorized denial of use: an adversary can prevent an authorized user from reading or modifying information, even though the adversary may not be able to read or modify the information. Causing a system “crash,” flooding a service with messages, or firing a bullet into a computer are examples of denial of use. This attack is another form of sabotage.

### 11.1.3 The Safety Net Approach

In the context of security, the two safety net principles be explicit and design for iteration reinforce this paranoid attitude:

1. Be explicit: Make all assumptions explicit so that they can be reviewed. It may require only one hole in the security of the system to penetrate it. The designer must therefore consider any threat that has security implications and make explicit the assumption on which the security design relies. Furthermore, make sure that all assumptions on which the security of the system is based are apparent at all times to all participants. For example, in the context of protocols, the meaning of each message should depend only on the content of the message itself, and should not be dependent on the context of the conversation. If the content of a message depends on its context, an adversary might be able to break the security of a protocol by tricking a receiver into interpreting the message in a different context.
2. Design for iteration: Assume you will make errors. Because the designer must assume that the design itself will contain flaws, the designer must be prepared to iterate the design. When a security hole is discovered, the designer must review the assumptions, if necessary adjust them, and repair the design. When a designer discovers an error in the system, the designer must reiterate the whole design and implementation process.

### 11.1.4 Design Principles

> Open design principle
Let anyone comment on the design. You need all the help you can get.
> 

> Minimize secrets
Because they probably won’t remain secret for long.
> 

> Economy of mechanism
The less there is, the more likely you will get it right.
> 

> Minimize common mechanism
Shared mechanisms provide unwanted communication paths.
> 

> Fail-safe defaults
Most users won’t change them, so make sure that defaults do something safe.
> 

> Least privilege principle
Don’t store lunch in the safe with the jewels.
> 

### 11.1.6 Security Model

    Although there are many ways to compromise the security of a system, the conceptual model to secure a system is surprisingly simple. To be secure, a system requires complete mediation: the system must mediate every action requested, including ones to configure and manage the system. The basic security plan then is that for each requested action the agent requesting the operation proves its identity to the system and then the system decides if the agent is allowed to perform that operation.

    Agents perform on behalf of some entity that corresponds to a person outside the computer system; we call the representation of such an entity inside the computer system a principal. The principal is the unit of authorization in a computer system, and therefore also the unit of accountability and responsibility. Using these terms, mediating an action is asking the question, “Is the principal who requested the action authorized to perform the action?”

    The basic approach to mediating every requested action is to ensure that there is really only one way to request an action. Conceptually, we want to build a wall around the system with one small  opening through which all requested actions pass. Then, for every requested action, the system must answer “Should I perform the action?”. To do so a system is typically decomposed in two parts: one part, called a guard, that specializes in deciding the answer to the question and a second part that performs the action. (In the literature, a guard that provides complete mediation is usually called a reference monitor.) The guard can clarify the question, “Is the principal who originated the requested action allowed to perform the action?” by obtaining answers to the three subquestions of complete mediation (see Figure 11.2). The guard verifies that the message containing the request is authentic (i.e., the request hasn’t been modified and that the principal is indeed the source of the request), and that the principal is permitted to perform the requested action on the object (authorization). If so, the guard allows the action; otherwise, it denies the request. The guard also logs all decisions for later audits.

    The first two (has the request been modified and what is the source of the request) of the three mediation questions fall in the province of authentication of the request. Using an authentication service the guard verifies the identity of the principal. Using additional information, sometimes part of the request but sometimes communicated separately, the guard verifies the integrity of the request. After answering the authenticity questions, the guard knows who the principal associated with the request is and that no adversary has modified the request.

    The third, and final, question falls in the province of authorization. An authorization service allows principals to specify which objects they share with whom. Once the guard has securely established the identity of the principal associated with the request using the authentication service, the guard verifies with the authorization service that the principal has the appropriate authorization, and, if so, allows the requested service to perform the requested action.

    The guard approach of complete mediation applies broadly to computer systems. Whether the messages are Web requests for an Internet store, LOAD and STORE operations to memory, or supervisor calls for the kernel, in all cases the same three questions must be answered by the Web service, virtual memory manager, or kernel, respectively. The implementation of the mechanisms for mediation, however, might be quite different for each case.

### 11.1.7 Trusted Computing Base

    When designing a secure system, we organize the system into two kinds of modules: untrusted modules and trusted modules. The correctness of the untrusted modules does not affect the security of the whole system. The trusted modules are the part that must work correctly to make the system secure. Ideally, we want the trusted modules to be usable by other untrusted modules, so that the designer of a new module doesn’t have to worry about getting the trusted modules right. The collection of trusted modules is usually called the trusted computing base (TCB).

The following method shows how to build such a TCB:

- Specify security requirements for the TCB (e.g., secure communication over untrusted networks). The main reason for this step is to explicitly specify assumptions so that we can decide if the assumptions are credible. As part of the requirements, one also specifies the attacks against which the TCB is protected so that the security risks are assessable. By specifying what the TCB does and does not do, we know against which kinds of attacks we are protected and to which kinds we are vulnerable.
- Design a minimal TCB. Use good tools to express the design.
- Implement the TCB. It is again important to use good tools. For example, buffer-overrun attacks can be avoided by using a language that checks array bounds.
- Run the TCB and try to break the security.

The hard part in this multistep design method is verifying that the steps are consistent: verifying that the design meets the specification, verifying that the design is resistant to the specified attacks, verifying that the implementation matches the design, and verifying that the system running in the computer is the one that was actually implemented.