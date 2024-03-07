# Recitation 22: Domain Name System Security Extensions (DNSSEC)

> What tools and/or threat models are violated by Torpig?
> 

Unauthorized information release, Unauthorized denial of use.

> Think about the key factors that allowed the authors to infiltrate the Torpig botnet. Would their techniques work for all botnets?
> 

Torpig use domain flux to locating its C&C hosts. Different botmaster use different measures to locating C&C server.

> Was the authors’ methodology—taking over the botnet—necessary to collect the data they wanted? Was it ethical?
> 

Yes, DGA is deterministic. All data will send to authors C&C server. 

Authors operated our C&C servers based on previously established legal and ethical principles.

> What does it mean for a computer to be a “bot”?
> 

Malware is install in a computer that are under the control of a malicious entity, typically referred to as the botmaster.

> In Torpig, how does a machine become a bot, and how does it receive instructions to carry out attacks?
> 

In the case of **Torpig**, a notorious botnet, let’s delve into how a machine transforms into a bot and how it receives instructions for its nefarious activities.

1. **Becoming a Bot**:
    - A computer becomes a **bot** when it gets infected by the **Torpig malware**.
    - The infection process typically involves the following steps:
        - **Infiltration**: Torpig infects a user’s machine through various means, such as malicious email attachments, compromised websites, or drive-by downloads.
        - **Rootkit Installation**: Torpig installs a **rootkit** on the victim’s system. The rootkit remains hidden and undetected, allowing Torpig to operate stealthily.
        - **Bot Activation**: Once the rootkit is in place, the machine becomes an active bot within the Torpig botnet. It is now under the control of the botnet operators.
2. **Instructions and Communication**:
    - Torpig bots receive instructions from a central entity known as the **command and control (C&C) server**.
    - The communication between the bot and the C&C server is facilitated using a technique called **domain flux**. This method involves dynamically changing domain names associated with the C&C server.
    - Torpig bots periodically connect to the C&C server to receive commands. These instructions can include tasks like launching DDoS attacks, stealing sensitive information (such as login credentials), or participating in spam campaigns.

> Why is it difficult to prevent a machine from becoming a bot? Why is it difficult to simply block bots from accessing the C&C server?
> 

**Challenges in Prevention**:

- **Preventing Bot Infection**: Detecting and preventing a machine from becoming a bot is challenging due to the stealthy nature of rootkits. Traditional antivirus tools may struggle to identify and remove them.
- **Blocking C&C Communication**: Simply blocking bots from accessing the C&C server is difficult because of domain flux. The constantly changing domain names make it hard to predict and block all communication channels.