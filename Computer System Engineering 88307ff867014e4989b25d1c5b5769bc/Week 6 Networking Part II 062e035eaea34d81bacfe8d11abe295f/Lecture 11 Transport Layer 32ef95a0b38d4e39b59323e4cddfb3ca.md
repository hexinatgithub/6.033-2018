# Lecture 11: Transport Layer

# CHAPTER 19 Reliable Data Transport Protocols

    Many applications, such as Web page downloads, file transfers, and interactive terminal sessions would like a reliable, in-order stream of data, receiving exactly one copy of each byte in the same order in which it was sent. A reliable transport protocol does the job of hiding the vagaries of a best-effort network—packet losses, reordered packets, and duplicate packets—from the application, and provides it the abstraction of a reliable packet stream. We will develop protocols that also provide in-order delivery.

    All reliable transport protocols use the same powerful ideas: redundancy to cope with packet losses and receiver buffering to cope with reordering, and most use adaptive timers. The tricky part is figuring out exactly how to apply redundancy in the form of packet retransmissions, in working out exactly when retransmissions should be done, and in achieving good performance.

## 19.2 Stop-and-Wait Protocol