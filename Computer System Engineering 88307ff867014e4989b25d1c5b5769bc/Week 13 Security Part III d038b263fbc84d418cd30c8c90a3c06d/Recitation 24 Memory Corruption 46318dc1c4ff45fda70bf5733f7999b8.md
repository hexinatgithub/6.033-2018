# Recitation 24: Memory Corruption

> Stack canaries (or cookies) and ASLR are both widely-deployed. What is good about those solutions? What attacks do they prevent? What attacks *don’t* they prevent?
> 

Stack canaries (or cookies) and ASLR overhead is very low.

Stack canaries can prevent buffer overflow in a local variable to overwrite the return address on the stack. Stack canaries do not protect indirect calls and jumps, and they are vulnerable to direct overwrite attacks and information leaks.

ASLR is the most comprehensive currently deployed protection against hijacking attacks.  They are vulnerable Information leaks.

> Many of the proposed solutions worsen performance. Are we at a point where these performance hits are outweighed by security concerns?
> 

Our comparison analysis in Section IX shows that techniques introducing an overhead larger than roughly 10% do not tend to gain wide adoption in production environments.

> What do the authors mean by “the eternal war in memory”?
> 

The authors use the phrase “the eternal war in memory” to describe the ongoing and unresolved conflict between attackers who exploit memory corruption vulnerabilities in software and defenders who develop protection mechanisms to prevent or mitigate such attacks. The authors argue that this war has lasted for more than 30 years and that none of the existing solutions can provide complete and efficient security against all types of memory errors and attacks. They also suggest that the war is not over and that new challenges and opportunities arise as software and hardware evolve.

> How has this war progressed over time? (i.e., how have attacks and solutions evolved)
> 

Over time, the **eternal war in memory** has witnessed significant developments in both attacks and solutions. Let’s delve into the evolution:

1. **Attacks**:
    - **Classic Buffer Overflows**: In the early days, attackers exploited buffer overflows by overflowing memory buffers with malicious code, gaining control over a program’s execution flow.
    - **Heap Exploits**: As defenses improved, attackers shifted focus to heap-based vulnerabilities. Techniques like heap spraying and use-after-free attacks emerged.
    - **Return-Oriented Programming (ROP)**: To bypass non-executable memory protections, attackers crafted ROP chains from existing code snippets (gadgets) to execute arbitrary code.
    - **Spectre and Meltdown**: Recent attacks leverage speculative execution and side channels to leak sensitive data from memory.
2. **Defensive Solutions**:
    - **Stack Canaries**: Introduced to detect buffer overflows by placing a guard value before the return address.
    - **Address Space Layout Randomization (ASLR)**: Randomizes memory addresses to prevent predictable exploitation.
    - **Data Execution Prevention (DEP)**: Marks memory regions as non-executable to thwart code injection.
    - **Control-Flow Integrity (CFI)**: Verifies control flow to prevent ROP attacks.
    - **Hardware Mitigations**: CPUs now include features like speculative execution barriers and memory isolation to counter attacks.
3. **Cat-and-Mouse Game**:
    - Attackers adapt to new defenses, finding novel ways to exploit memory.
    - Defenders continuously enhance techniques, but vulnerabilities persist.
    - Zero-day exploits remain valuable commodities.

In summary, the war in memory continues, with each side innovating and countering the other. The quest for robust memory security remains ongoing. 

> Why hasn’t the war ended?
> 

The **eternal war in memory** persists for several reasons:

1. **Complexity of Systems**: Modern software systems are intricate, with numerous layers and components. Each layer introduces potential vulnerabilities, making it challenging to eliminate all security risks.
2. **Legacy Code**: Many systems still rely on legacy code written decades ago. Rewriting or securing such code is time-consuming and costly. As a result, vulnerabilities persist.
3. **Human Error**: Developers, despite their best efforts, can introduce bugs or overlook security considerations. Even a small oversight can lead to exploitable vulnerabilities.
4. **Innovation in Attacks**: Attackers continuously evolve their techniques. New attack vectors emerge, exploiting novel vulnerabilities or bypassing existing defenses.
5. **Trade-offs**: Balancing security and usability is tricky. Stricter security measures may hinder functionality or user experience, leading to compromises.
6. **Economic Incentives**: Zero-day exploits fetch high prices on the black market. As long as there’s demand, attackers will persist.
7. **Resource Constraints**: Organizations allocate limited resources to security. Prioritizing all vulnerabilities equally is impractical.

In essence, the war endures due to the dynamic nature of technology, human fallibility, and the ever-evolving threat landscape. Vigilance and adaptation remain crucial in this ongoing battle.