# Lecture 2: Naming in Systems

## 2.2 Naming in computer systems

In a direct analogy with two ways in which procedures can pass arguments, there are two ways to arrange for one object to use another as a component:

- create a copy of the component object and include the copy in the using object (use by value), or
- choose a name for the component object and include just that name in the using object (use by reference). The component object is said to export the name.

One purpose of names, then, is to allow use by reference and thus simplify the sharing of changeable objects.

A second fundamental purpose for a name is to allow a system designer to defer to a later time an important decision: to which object should this name refer? A name also makes it easy to change that decision later.

Decoupling one object from another by using a name as an intermediary is known as indirection. Deciding on the correspondence between a name and an object is an example of binding.

### 2.2.1 The Naming Model

It is helpful to have a model of how names are associated with specific objects. A system designer creates a naming scheme, which consists of three elements. The first element is a name space, which comprises an alphabet of symbols together with syntax rules that specify which names are acceptable.  The second element is a name-mapping algorithm, which associates some (not necessarily all) names of the name space with some (again, not necessarily all) values in a universe of values, which is the third and final element of the naming scheme.  A value may be an object, or it may be another name from either the original name space or from a different name space.  A name-to-value mapping is an example of a binding, and when such a mapping exists, the name is said to be bound to the value.

> Unique identifier name space, which provides a set of names that will never be reused for the lifetime of the name space and, once bound, will always remain bound to the same value. Such a name is said to have a stable binding.
> 

The interpreter that encounters the name runs the name-mapping algorithm of the appropriate naming scheme.  The name-mapping algorithm resolves the name, which means that it discovers and returns the associated value (for this reason, the name-mapping algorithm is also called a resolver).  The name-mapping algorithm is usually controlled by an additional parameter, known as a context.

### 2.2.2 default and explicit context references

Context references for names found in an object

- Default: supplied by the resolver
    - Constant built in to the resolver
    - Variable from the current environment
- Explicit: supplied by the object
    - Per object
    - Per name (qualified name)

### 2.2.3 path names, naming networks, and recursive name resolution

The recursive aspect of this description is that the explicit context reference is itself a path name that must be resolved. So we repeat the analysis as many times as needed until what was originally the most significant component of the path name is also the least significant component, at which point the resolver can do an ordinary table lookup using some context.

In a typical design, the resolver uses one of two default context references:

- A special context reference, known as the root, that is built in to the resolver. The root is an example of a universal name space.  A path name that the resolver can resolve with recursion that ends at the root context is known as an absolute path name.
- The path name of yet another default context.  To avoid circularity, this path name must be an absolute path name.  A path name that is resolved by looking up its most significant component in yet another context is known as a relative path name. Thus in the UNIX file system, for example, if the working directory is /usr/Alice, the relative path name plans/Monday would resolve to the same file as the absolute path name /usr/Alice/plans/Monday.

Path names can also be thought of as identifying objects that are organized in what is called a naming network. In a naming network, contexts are treated as objects, and any context may contain a name-to-object binding for any other object, including another context. The name resolver somehow chooses one context to use as the root (perhaps by having a lower-level name for that context wired into the resolver), and it then resolves all absolute path names by tracing a path from the chosen root to the first named context in the path name, then the next, continuing until it reaches the object that was named by the original path name. It similarly resolves relative path names starting with a default context found in a variable in its environment. That variable contains the absolute path name of the default context.

### 2.2.4 multiple lookup: searching through layered contexts

Multiple lookup is to abandon the notion of a single, default context and instead resolve the name by systematically trying several different contexts. Since a name may be bound in more than one context, multiple lookup can produce multiple resolutions, so some scheme is needed to decide which resolution to use.

### 2.2.5 comparing names

The meaning of name comparison requires some thought because the invoker might have one of three different questions in mind:

1. Are the two names the same?
2. Are the two names bound to the same value?
3. If the value or values are actually the identifiers of storage containers, such as memory cells or disk sectors, are the contents of the storage containers the same?

## 3.1 Considerations in the design of naming schemes

### 3.1.1 modular sharing

Modular sharing means that one can use a shared module by name without knowing the names of the modules it uses.

Lack of modular sharing shows up in the form of name conflict, in which for some reason two or more different values compete for the binding of the same name in the same context. Name conflict can arise when integrating two (or more) independently conceived sets of programs, sets of documents, file systems, databases, or indeed any collection of components that use the same naming scheme for internal interconnection as for integration.

### 3.1.2 metadata and name overloading

Metadata—information that is useful to know about an object but that cannot be found inside the object itself (or if it is inside may not be easy to find). A common, though not universal, property of metadata is that it is information about an object that may be changed without changing the object itself.

It is common to discover that file names are overloaded with metadata that has little or nothing to do with the use of the name as a reference.

The naming scheme may even impose syntax rules on allowable names to support overloading with metadata.

Names that have no overloading whatever are known as pure names.

### 3.1.3 addresses: names that Locate Objects

In a computer system, an address is the name of a physical location or of a virtual location that maps to a physical location.

The overloaded location information found in addresses can cause name fragility. When an object moves, its address, and thus its name, changes. For this reason, system designers usually follow the example of telephone switching systems: they apply the design principle decouple modules with indirection to hide addresses. Adding a layer of indirection provides a binding from some externally visible, but stable, name to an address that can easily be changed when the object moves to a new location. Ideally, addresses never need to be exposed above the layer of interpretation that directly manipulates the objects.

### 3.1.4 Generating Unique Names

The usual approach is for the naming scheme to generate a name for a newly created object, rather than relying on the creator to propose a unique name.

### 3.1.5 Intended Audience and User-Friendly Names

When a name is intended to be user-friendly, a tension arises between a need for it to be a unique, easily resolvable identifier and a need to respect other, non-technical values such as being easy to remember or being the same as some existing place or personal name. This tension may be resolved by maintaining a second, machineoriented identifier, in addition to the user-friendly name.

> the Principle of Least A stonishment is a design principle aimed at ensuring that user interfaces and system behaviors are intuitive and reasonable for users. According to this principle, system operations and responses should align with user expectations, avoiding surprises or confusion.
> 

### 3.1.6 Relative Lifetimes of Names, Values, and Bindings

The naming scheme for a limited name space typically assigns the names, rather than letting the user choose them. On the other hand, if the name space is unlimited, meaning that it does not significantly constrain name lengths, it is usually possible to allow the user to choose arbitrary names.

[4.4 Case study: The Internet Domain Name System (DNS)](Lecture%202%20Naming%20in%20Systems%20c28bebc24d294ce1b4c9c5e5e080c009/4%204%20Case%20study%20The%20Internet%20Domain%20Name%20System%20(DN%20eaeb2044bab840269b1f8cb4e8e642f9.md)