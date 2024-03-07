# Lecture 6: Operating Systems Structure + Virtual Machines

## 5.8 Application: Enforcing modularity using virtual machines

A virtual machine is useful in a number of situations:

- To run several guest operating systems side by side. For example, on one virtual Intel x86 machine, one can run the GNU/Linux operating system, and on another one can run the Windows/XP operating system. If the virtual machine monitor implements the Intel x86 faithfully (i.e., instructions, state, protection levels, page tables), then one can run GNU/Linux, Windows/XP, and their applications on top of the monitor without modifications.
- To contain errors in a guest operating system. Because the guest runs inside a virtual machine, errors in the guest operating system cannot affect the operating systems software on other virtual machines. This feature is handy for debugging a new operating system or for containing an operating system that is flaky but important for certain applications.
- To simplify development of operating systems. The virtual machine monitor can virtualize the physical hardware to provide a simpler interface, which may simplify the development of an operating system. For example, the virtual machine monitor may turn a multiprocessor computer into a few uniprocessor computers to allow the guest operating system to be written for a uniprocessor, which simplifies coordination.

### 5.8.2 Implementing Virtual Machines

To implement virtual machine, the virtual machine monitor must provide three primary functions:

1. Virtualizing the computer. For example, if a guest operating system stores a new value into the page-map address register, then the monitor must make the guest operating system believe that it can do so, even though the guest is running in user mode.
2. Dispatch events. For example, the monitor must forward interrupts, exceptions, and supervisor calls invoked by the applications to the appropriate guest operating systems.
3. Allocate resources. For example, the monitor must divide physical memory among the guest operating systems.

### 5.8.3 Virtualizing Example

<aside>
💡 Virtualizing the computer:

Virtualizing the computer is easy if all instructions are virtualizable. That is, all the instructions that allow a guest to tell the difference between running on the physical and running on a virtual machine must result in an exception to the monitor so that the monitor can emulate the intended behavior. In addition, the exception must leave enough information for the exception handler to emulate the instruction and restart the guest operating system as if it has executed the instruction.

To allow each guest operating system to address all physical memory, but not other guests’ physical memory, the virtual machine monitor must guard the guest’s physical addresses. One way to do so is to virtualize addresses recursively. That is, the guest and virtual machine translate application virtual addresses to virtual machine addresses; the monitor translates machine virtual addresses to physical addresses. One challenge in designing the monitor is to maintain this mapping from application virtual to virtual machine to physical addresses. The general plan is for the monitor to emulate loads and stores to the page-map address register, and keep its own translation map per virtual machine, which we will refer to as the machine map.

The monitor can deduce which virtual machine memory a guest is using and the mappings from virtual to machine addresses when the guest invokes a store instruction to the page-map address register. Because this instruction is privileged, the processor will generate an illegal-instruction exception and transfer control to the monitor. The argument to the store instruction contains the machine address of a page map. The monitor can read that memory and see which virtual machine memory the guest is planning to use and what the guest’s mappings from virtual to machine are (including the permissions).

</aside>

<aside>
💡 Dispatch events:
When guest OS generate a exception, which will invoke an exception handler in the monitor. Then the exception handler in the monitor can invoke the exception handler of the appropriate guest. The guest exception handler now believes it received the missing-page exception directly from the processor, and it can take appropriate action.

</aside>