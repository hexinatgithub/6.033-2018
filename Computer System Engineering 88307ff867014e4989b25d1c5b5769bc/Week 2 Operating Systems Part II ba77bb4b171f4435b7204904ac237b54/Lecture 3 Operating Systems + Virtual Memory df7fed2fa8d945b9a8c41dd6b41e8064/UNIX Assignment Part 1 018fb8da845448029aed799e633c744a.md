# UNIX Assignment Part 1

> What things in UNIX are named?
> 

In UNIX, many things are named, such as files, directories, processes, users, groups, devices, sockets, pipes, and more. These names are used to identify, access, and manipulate these things.

> How does naming in UNIX compare to naming in DNS? How do layering and hierarchy apply (if at all)?
> 

Naming in UNIX and DNS have some similarities and some differences. Similarities are that they both use hierarchical naming schemes, where names are composed of parts separated by dots or slashes.

Differences are that UNIX names are case-sensitive, while DNS names are case-insensitive.

Layering and hierarchy apply to both UNIX and DNS naming in different ways. In UNIX, layering is used to separate the logical name space from the physical name space. The file system layer is responsible for mapping the logical name to the physical name, which is the i-node number. Hierarchy is used to organize the files and directories into a tree structure, where each node has a name and a parent node, except for the root node.

In DNS, layering is used to divide the domain name space into zones, where each zone is a contiguous portion of the name space that is administered by a single authority.

> What is UNIX?
> 

UNIX is a general-purpose, multi-user, interactive operating system. It offers a number of features seldom found even in larger operating systems, including: (1) a hierarchical file system incorporating demountable volumes; (2) compatible file, device, and inter-process I/O; (3) the ability to initiate asynchronous processes; (4) system command language selectable on a per-user basis; and (5) over 100 subsystems including a dozen languages.

> How is its filesystem designed?
> 

Filesystem contain three kinds of files: ordinary disk files, directories, and special files. The Unix file system is designed as a hierarchical tree structure that starts with a single root directory (/) and branches out into subdirectories and files.

> Why was it designed to work that way?
> 

The hierarchical file system allowed the system to organize files and directories in a logical and consistent way, as well as providing access to other resources through device files.