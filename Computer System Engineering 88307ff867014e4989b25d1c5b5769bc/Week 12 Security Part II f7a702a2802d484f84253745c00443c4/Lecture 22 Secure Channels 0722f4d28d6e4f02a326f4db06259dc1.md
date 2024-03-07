# Lecture 22: Secure Channels

## 11.3 Authenticating Messages

    When receiving a message, the guard needs an ensured way of determining what the sender said in the message and who sent the message. Answering these two questions is the province of message authentication. Message authentication techniques prevent an adversary from forging messages that pretend to be from someone else, and allow the guard to determine if an adversary has modified a legitimate message while it was en route.

    To establish that a message belongs to a chain of messages, a guard must be able to verify the authenticity of the message. Message authenticity requires both:

- data integrity: the message has not been changed since it was sent;
- origin authenticity: the claimed origin of the message, as learned by the receiver from the message content or from other information, is the actual origin.

### 11.3.2 Closed versus Open Designs and Cryptography

    These problems with closed designs led Kerchkoffs to propose a design rule, now known as Kerchkoffs’ criterion, which is a particular application of the principles of open design and least privilege: minimize secrets. For a cryptographic system, open design means that we concentrate the secret in a corner of a cryptographic transformation, and make the secret removable and easily changeable. An effective way of doing this is to reduce the secret to a string of bits; this secret bit string is known as a cryptographic key, or key for short. By choosing a longer key, one can generally increase the time for the adversary to compromise the transformation.

### 11.3.3 Key-Based Authentication Model

Signing produces as output an authentication tag: a key-based cryptographic transformation (usually shorter than the message). We can write the operation of signing as follows:

T ← SIGN (M, K1)

where T is the authentication tag.

Let’s assume that the sender sends a message {M, T}. The receiver receives a message {M', T'}, which may be the same as {M, T} or it may not. The purpose of message authentication is to decide which. The receiver unmarshals {M', T'} into its components M' and T', and verifies the authenticity of the received message, by performing the computation:

result ← VERIFY (M', T', K2)

This computation returns ACCEPT if M' and T' match; otherwise, it returns REJECT.

### 11.3.4 Properties of SIGN and VERIFY

The sender sends {M, T} and the receiver receives {M', T'}. The requirements for an authentication system with shared-secret key K are as follows:

1. VERIFY (M', T', K) returns ACCEPT if M' = M, T' = SIGN (M, K)
2. Without knowing K, it is difficult for an adversary to compute an M' and T' such
that VERIFY (M', T', K) returns ACCEPT
3. Knowing M, T, and the algorithms for SIGN and VERIFY doesn’t allow an adversary to
compute K

A corresponding set of properties must hold for public-key authentication systems:

1. VERIFY (M', T', K2) returns ACCEPT if M' = M, T' = SIGN (M, K1)
2. Without knowing K1, it is difficult for an adversary to compute an M' and T' such
that VERIFY (M', T', K2) returns ACCEPT
3. Knowing M, T, K2, and the algorithms for verify and sign doesn’t allow an adversary
to compute K1

## 11.4 Message Confidentiality

### 11.4.1 Message Confidentiality Using Encryption

    Encryption transforms a plaintext message into ciphertext in such a way that an observer cannot construct the original message from the ciphertext version, yet the intended receiver can.     

    Decryption transforms the received ciphertext into plaintext. Thus, one challenge in the implementation of channels that provide confidentiality is to use an encrypting scheme that is difficult to reverse for an adversary. That is, even if an observer could copy a message that is in transit and has an enormous amount of time and computing power available, the observer should not be able to transform the encrypted message into the plaintext message.

### 11.4.2 Properties of ENCRYPT and DECRYPT

    For both the shared-key and public-key encryption systems, the procedures ENCRYPT and DECRYPT should have the following properties. It should be easy to compute:

- C← ENCRYPT (M, K1)
- M' ← DECRYPT (C, K2)

and the result should be that M = M'.

## 11.5  Security Protocols

    A security protocol is an exchange of messages designed to allow mutually-distrustful parties to achieve an objective. Security protocols often use cryptographic techniques to achieve the objective.

    For performance reasons, computer systems typically use public-key systems for distributing and authenticating keys and shared-secret systems for sending messages in an authenticated and confidential manner. The operations in public-key systems (e.g., raising to an exponent) are more expensive to compute than the operations in shared-secret cryptography (e.g., table lookups and computing several XORs). Thus, a session between two parties typically follows two steps:

1. At the start of the session use public-key cryptography to authenticate each party to the other and to exchange new, temporary, shared-secret keys;
2. Authenticate and encrypt subsequent messages in the session using the temporary shared-secret keys exchanged in step 1.

Using this approach, only the first few messages require computationally expensive operations, while all subsequent messages require only inexpensive operations.

### 11.5.2  Designing Security Protocols

The new attacks to protect against fall in the following categories:

- Known-key attacks. An adversary obtains some key used previously and then uses this information to determine new keys.
- Replay attacks. An adversary records parts of a session and replays them later, hoping that the recipient treats the replayed messages as new messages. These replayed messages might trick the recipient into taking an unintended action or divulging useful information to the adversary.
- Impersonation attacks. An adversary impersonates one of the other principals in the protocol. A common version of this attack is the person-in-the-middle attack, where an adversary relays messages between two principals, impersonating each principal to the other, reading the messages as they go by.
- Reflection attacks. An adversary records parts of a session and replays it to the party that originally sent it. Protocols that use shared-secret keys are sometimes vulnerable to this special kind of replay attack.

The participants are thus interested not only in confidentiality and authentication, but also in the three following properties:

- Freshness. Does this message belong to this instance of this protocol, or is it a replay from a previous run of this protocol?
- Explicitness. Is this message really a member of this run of the protocol, or is it copied from an run of another protocol with an entirely different function and different participants?
- Forward secrecy. Does this protocol guarantee that if a key is compromised that confidential information communicated in the past stays confidential? A protocol has forward secrecy if it doesn’t reveal, even to its participants, any information from previous uses of that protocol.

A brief summary of standard approaches to ensure freshness and explicitness include:

- Ensure that each message contains a nonce (a value, perhaps a counter value, serial number, or a timestamp, that will never again be used for any other message in this protocol), and require that a reply to a message include the nonce of the message being replied to, as well as its own new nonce value. The receiver and sender of course have to remember previously used nonces to detect duplicates. The nonce technique provides freshness and helps foil replay attacks.
- Ensure that each message explicitly contain the name of the sender of the message and of the intended recipient of the message. Protocols that omit this information, and that use shared-secret keys for authentication, are sometimes vulnerable to reflection attacks. Including names provides explicitness and helps foil impersonation and reflection attacks.
- Ensure that each message specifies the security protocol being followed, the version number of that protocol, and the message number within this instance of that protocol. If such information is omitted, a message from one protocol may be replayed during another protocol and, if accepted as legitimate there, cause damage. Including all protocol context in the message provides explicitness and helps foil replay attacks.

The explicitness property is an example of the be explicit design principle: ensure that each message be totally explicit about what it means. If the content of a message is not completely explicit, but instead its interpretation depends on its context, an adversary might be able to trick a receiver into interpreting the message in a different context and break the protocol. Leaving the names of the participants out of the message is a violation of this principle.

# Example

[TSL Overview](https://www.ibm.com/docs/en/ibm-mq/9.3?topic=tls-overview-ssltls-handshake)