# Week 1: Operating Systems Part I

## System design style

### The right thing:

- Simplicity -- the design must be simple, both in implementation and interface. It is more important for the interface to be simple than the implementation.
- Correctness -- the design must be correct in all observable aspects. Incorrectness is simply not allowed.
- Consistency -- the design must not be inconsistent. A design is allowed to be slightly less simple and less complete to avoid inconsistency. Consistency is as important as correctness.
- Completeness -- the design must cover as many important situations as is practical. All reasonably expected cases must be covered. Simplicity is not allowed to overly reduce completeness.

### The worse-is-better philosophy or the new jersey approach is only slightly different:

- Simplicity -- the design must be simple, both in implementation and interface. It is more important for the implementation to be simple than the interface. Simplicity is the most important consideration in a design.
- Correctness -- the design must be correct in all observable aspects. It is slightly better to be simple than correct.
- Consistency -- the design must not be overly inconsistent. Consistency can be sacrificed for simplicity in some cases, but it is better to drop those parts of the design that deal with less common circumstances than to introduce either implementational complexity or inconsistency.
- Completeness -- the design must cover as many important situations as is practical. All reasonably expected cases should be covered. Completeness can be sacrificed in favor of any other quality. In fact, completeness must be sacrificed whenever implementation simplicity is jeopardized. Consistency can be sacrificed to achieve completeness if simplicity is retained; especially worthless is consistency of interface.

The worse-is-better software first will gain acceptance, second will condition its users to expect less, and third will be improved to a point that is almost the right thing.

Many interesting applications of computers require

- fault tolerance
- coordination of concurrent activities
- geographically separated but linked data
- vast quantities of stored information
- protection from mistakes and intentional attacks
- interactions with many people

To develop applications that have these requirements, the designer must look beyond the software and hardware and view the computer system as a whole. In doing so, the designer encounters many new problems—so many that the limit on the scope of computer systems generally arises neither from laws of physics nor from theoretical impossibility, but rather from limitations of human understanding.

Some of these same problems have counterparts, or at least analogs, in other systems that have, at most, only incidental involvement of computers. The study of systems is one place where computer engineering can take advantage of knowledge from other engineering areas: civil engineering (bridges and skyscrapers), urban planning (the design of cities), mechanical engineering (automobiles and air conditioning), aviation and space flight, electrical engineering, and even ecology and political science.

## 1.1 Systems and complexity

### 1.1.1 Common problems of systems in many fields

The problems one encounters in these many kinds of systems can usefully be divided into four categories: emergent properties, propagation of effects, incommensurate scaling, and trade-offs.

Emergent properties, propagation of effects, incommensurate scaling, and tradeoffs are issues that the designer must deal with in every system. The question is how to build useful computer systems in the face of such problems.

## 1.1.2 Systems, Components, Interfaces, and Environments

A system is a set of interconnected components that has an expected behavior observed at the interface with its environment.

The underlying idea of the concept of system is to divide all the things in the world into two groups: those under discussion and those not under discussion. Those things under discussion are part of the system---those that are not are part of the environment. There are always interactions between a system and its environment; these interactions are the interface between the system and the environment.

One studies a system to predict its overall behavior, based on information about its components, their interconnections, and their individual behaviors. Identifying the components, however, depends on one’s point of view, which has two aspects, purpose and granularity. One may, with different purposes in mind, look at a system quite differently. One may also choose any of several different granularities. These choices affect one’s identification of the components of the system in important ways.

When a system in one context is a component in another, it is usually called a subsystem. The composition of systems from subsystems or decomposition of systems into subsystems can be carried on to as many levels as is useful. In summary, then, to analyze a system one must establish a point of view to determine which things to consider as components, what the granularity of those components should be, where the boundary of the system lies, and which interfaces between the system and its environment are of interest.

## 1.1.3 Complexity

Describe a set of signs of complexity that can help confirm a diagnosis:

1. Large number of components. Sheer size certainly affects our view of whether or not a system rates the description “complex”.
2. Large number of interconnections. Even a few components may be interconnected in an unmanageably large number of ways. For example, the Sun and the known planets comprise only a few components, but every one has gravitational attraction for every other, which leads to a set of equations that are unsolvable (in closed form) with present mathematical techniques. Worse, a small disturbance can, after a while, lead to dramatically different orbits. Because of this sensitivity to disturbance, the solar system is technically chaotic. Although there is no formal definition of chaos for computer systems, that term is often informally applied.
3. Many irregularities. By themselves, a large number of components and interconnections may still represent a simple system, if the components are repetitive and the interconnections are regular. However, a lack of regularity, as shown by the number of exceptions or by non-repetitive interconnection arrangements, strongly suggests complexity. Put another way, exceptions complicate understanding.
4. A long description. Looking at the best available description of the system one finds that it consists of a long laundry list of properties rather than a short, systematic specification that explains every aspect. Theoreticians formalize this idea by measuring what they call the “Kolmogorov complexity” of a computational object as the length of its shortest specification. To a certain extent, this sign may be merely a reflection of the previous three, although it emphasizes an important aspect of complexity: it is relative to understanding. On the other hand, lack of a methodical description may also indicate that the system is constructed of ill-fitting components, is poorly organized, or may have unpredictable behavior, any of which add complexity to both design and use.
5. A team of designers, implementers, or maintainers. Several people are required to understand, construct, or maintain the system. A fundamental issue in any system is whether or not it is simple enough for a single person to understand all of it. If not, it is a complex system because its description, construction, or maintenance will require not just technical expertise but also coordination and communication across a team.

## 1.2 Sources of complexity

### 1.2.1 Cascading and Interacting Requirements

A primary source of complexity is just the list of requirements for a system. Each requirement, viewed by itself, may seem straightforward. Any particular requirement may even appear to add only easily tolerable complexity to an existing list of requirements. The problem is that the accumulation of many requirements adds not only their individual complexities but also complexities from their interactions. This interaction complexity arises from pressure for generality and exceptions that add complications, and it is made worse by change in individual requirements over time.

### 1.2.2 Maintaining High Utilization

One requirement by itself is frequently a specific source of complexity. It starts with a desire for high performance or high efficiency. Whenever a scarce resource is involved, an effort arises to keep its utilization high.

## 1.3 Coping with complexity I

### 1.3.1 Modularity

The simplest, most important tool for reducing complexity is the divide-and-conquer technique: analyze or design the system as a collection of interacting subsystems, called modules. The power of this technique lies primarily in being able to consider interactions among the components within a module without simultaneously thinking about the components that are inside other module.

### 1.3.2 Abstraction

An important assumption in the numerical example of the effect of modularity on debugging time may not hold up in practice: that discovery of a bug should usually lead to examining just one module. For that assumption to hold true, there is a further requirement: there must be little or no propagation of effects from one module to another.

Thus the best divisions usually follow natural or effective boundaries. They are characterized by fewer interactions among modules and by less propagation of effects from one module to another. More generally, they are characterized by the ability of any module to treat all the others entirely on the basis of their external specifications, without need for knowledge about what goes on inside. This additional requirement on modularity is called abstraction. Abstraction is separation of interface from internals, of specification from implementation.

<aside>
💡 The robustness principle.

</aside>

<aside>
💡 Be tolerant of inputs and strict on outputs.

</aside>

This principle means that a module should be designed to be liberal in its interpretation of its input values, accepting them even if they are not within specified ranges, if it is still apparent how to sensibly interpret them. On the other hand, the module should construct its outputs conservatively in accordance with its specification—if possible making them even more accurate or more constrained than the specification requires. The effect of the robustness principle is to tend to suppress, rather than propagate or even amplify, noise or errors that show up in the interfaces between modules.

<aside>
💡 The safety margin principle

</aside>

<aside>
💡 Keep track of the distance to the cliff, or you may fall over the edge.

</aside>

When inputs are not close to their specified values, that is usually an indication that something is starting to go wrong. The sooner that something going wrong can be noticed, the sooner it can be fixed. For this reason, it is important to track and report out-of-tolerance inputs, even if the robustness principle would allow them to be interpreted successfully.

### 1.3.3 Layering

Systems that are designed using good abstractions tend to minimize the number of interconnections among their component modules. One powerful way to reduce module interconnections is to employ a particular method of module organization known as layering. In designing with layers, one builds on a set of mechanisms that is already complete (a lower layer) and uses them to create a different complete set of mechanisms (an upper layer). A layer may itself be implemented as several modules, but as a general rule, a module of a given layer interacts only with its peers in the same layer and with the modules of the next higher and next lower layers. That restriction can significantly reduce the number of potential inter-module interactions in a big system.

### 1.3.4 Hierarchy

The final major technique for coping with complexity also reduces interconnections
among modules but in a different, specialized way. Start with a small group of modules, and assemble them into a stable, self-contained subsystem that has a well-defined interface. Next, assemble a small group of subsystems to produce a larger subsystem. This process continues until the final system has been constructed from a small number of relatively large subsystems. The result is a tree-like structure known as a hierarchy.

The reason is that hierarchy constrains interactions by permitting them only among the components of a subsystem. Hierarchy constrains a system of N components, which in the worst
case might exhibit N*(N-1) interactions, so that each component can interact only with members of its own subsystem, except for an interface component that also interacts with other members of the subsystem at the next higher level of hierarchy.

### 1.3.5 Putting it Back Together: Names Make Connections

The four techniques for coping with complexity—modularity, abstraction, layering, and hierarchy—provide ways of dividing things up and placing the resulting modules in suitable relation one to another. However, we still need a way of connecting those modules. In digital systems, the primary connection method is that one module names another module that it intends to use. Names allow postponing of decisions, easy replacement of one module with a better one, and sharing of modules.

In a modular system, one can usually find several ways to combine modules to implement a desired feature. The designer must at some point choose a specific implementation from among many that are available. Making this choice is called binding. Recalling that the power of modularity comes from the ability to replace an implementation with a better one, the designer usually tries to maintain maximum flexibility by delaying binding until the last possible instant, perhaps even until the first instant that the feature is actually needed.

> Decouple modules with indirection
> 

> Indirection supports replaceability.
> 

## 1.4 Computer systems are the same but different

The techniques that have been devised for coping with complexity are universal. Modularity, abstraction, layering, and hierarchy are used as tools in most fields that deal with complex systems. It is therefore useful for the computer system designer to investigate systems from other fields, both to gain additional perspective on how system problems arise and to discover specific techniques from other fields that may also apply to computer systems. Stated briefly, we conclude that computer systems are the same as all other systems.

But there is one problem with that conclusion: it is wrong. There are at least two significant ways in which computer systems differ from every other kind of system with which designers have experience:

- The complexity of a computer system is not limited by physical laws.
- The rate of change of computer system technology is unprecedented.

### 1.4.1 Computer Systems have no Nearby Bounds on Composition

Computer systems not accumulate noise as it goes through a string of devices, then noise does not limit the number of devices one can string together.

In contrast with other systems, computer systems allow composition to a depth whose first limit is the designer’s ability to understand. Unfortunately, this lack of nearby natural, physical bounds on depth of composition tempts designers to build more complex systems. If nature does not impose a nearby limit on composition, the designer must self-impose a limit. Since it can be hard to say no to a reasonable-sounding feature, features keep getting added. Therein lies the fate of too many computer system designs.

### 1.4.2 d(technology)/dt is unprecedented

> The incommensurate scaling rule
> 

> Changing any system parameter by a factor of 10 usually requires a new design.
> 

## 1.5 Coping with complexity II

### 1.5.1 Why modularity, abstraction, layering, and hierarchy aren’t enough

> Design for iteration
> 

> You won’t get it right the first time, so make it easy to change.
> 

Document the assumptions behind the design so that when the time comes to change the design you can more easily figure out what else has to change. Expect not only to modify and replace modules, but also to remodularize as the system and its requirements become better understood.

- Take small steps. The purpose is to allow discovery of both design mistakes and bad ideas quickly, so that they can be changed or removed with small effort and before other parts of the system in later iterations start to depend on them and they effectively become unchangeable. Systems under active development may be subjected to a complete system rebuild every day because the rebuilding process invokes a large number of checks and tests that can reveal implementation mistakes, while the changes that caused the mistakes are fresh in the minds of the implementers.
- Don’t rush. Even though individual steps may be small, they must still be well planned. In most projects, the temptation is to rush to implementation. With iterative design, that temptation can be stronger, and the designer must make sure that the design is ready for the next step.
- Plan for feedback. Include as part of the design both feedback paths and positive incentives to provide feedback. Testers, installers, maintainers, and users of the system can provide much of the information needed to refine it. Alpha testing (“we’re not at all sure this even works”) and beta testing (“seems to work, use at your own risk”) are common examples, and many vendors encourage users to report details of problems and transcripts of failures by e-mail. A well designed system will provide many such feedback schemes at all levels.
- Study failures. An important goal is to learn from failures rather than assign blame for them. Incentives must be carefully designed to ensure that feedback about failures is not ignored or even suppressed by people fearful of being blamed. Then, having found the apparent cause of a failure, continue looking for other contributing or more basic causes. Working systems often work for reasons that aren’t well understood. It is common to find that a new release of a system reveals a bug that has actually been in the system for a long time but has never mattered until now. Much can be learned by figuring out why it never mattered. It can also be useful to explore the mindset of the designers to understand what allowed them to design a system that could fail in this way.* Similarly, don’t ignore unexplained behavior. If the feedback reports something that now seems not to be a problem or to have gone away, it is probably a
sign that something is wrong rather than that the system magically fixed itself.
    
    > Keep digging
    > 
    
    > Complex systems fail for complex reasons.
    > 

### 1.5.3 Keep it simple

> Adopt sweeping simplifications
> 

> So you can see what you are doing.
> 

[Enforcing Modularity with Clients and Services](Week%201%20Operating%20Systems%20Part%20I%209f5785e6604e49abb7f340f4d8b8b783/Enforcing%20Modularity%20with%20Clients%20and%20Services%202cc0825dfce64235a804550700a9ef6c.md)