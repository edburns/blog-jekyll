---
layout: post
title:  "Public Key Infrastructure"
date:   2020-08-28 15:10-0400
comments: true
---

I needed to remind myself of how public key cryptography worked so I
looked up this excerpt I remembered writing in [JSF The Complete Reference](https://www.amazon.com/Ed-Burns/e/B001ILO9Y8).  I'm saving it here for the next time I forget how this works and need to remind myself.

--------------------

The easiest way to share data securely is to encrypt it using some scheme and share the password with the party with whom you want to share the data. Of course, the matter of sharing the password securely is a problem, but let’s just say you can tell the person directly. While the shared password scheme is easy to understand and implement, it doesn’t scale to large numbers of users without compromising security. Public key cryptography, also called public key infrastructure, or PKI, solves the problem by breaking the concept of a password into two parts, a “public key” and a “private key.” The archetypal analogy to describe PKI involves two peopleAlice and Bobwho want to send messages to each other through the postal mail. In order for Alice to send a message to Bob securely, Alice asks Bob to send her his open padlock, to which only Bob has the key. Alice then puts the message in the box and locks it with Bob’s padlock, sending it in the mail. When Bob receives the box he can open the box with his key and see the message. Conversely, when Bob wants to send a message to Alice, he needs Alice’s open padlock. In PKI, the padlock is the public key, and the key for the padlock is the private key. This situation is depicted in Figure 14-2.

In order to share data with someone, you need their public key and your private key; therefore, it is desirable to distribute your public key as widely as possible. That’s where the “infrastructure” in public key infrastructure really comes in. When padlocks (public keys) are freely distributed, the problem is verifying that Alice’s padlock really belongs to Alice and not some malicious postman who opened the mail and replaced Alice’s padlock with his own, only to intercept the return package and open the box. The authenticity of a public key is determined using an “identity certificate.”

![Alice and Bob exchange a secret message]({{ site.url }}/blog/assets/20200828-pki.png)

When speaking of Internet security, an “identity certificate” is an electronic document originating from a trusted source that vouches for the authenticity of a public key. The source from which the certificate originates is known as the certificate authority (CA), and there are a handful of such bodies in business around the world today. CACert, Thawte, and VeriSign are three popular CAs. To bring the matter back to Web applications, servers and clients may possess their own public keys, and identity certificates to vouch for them, to encrypt all the traffic between client and server. Because Web applications are by nature interactive, clients must authenticate themselves to servers as well as servers to clients. This is known as two-way authentication and is the standard practice when using client certificate authentication. Once the certificates have been authenticated, the public keys are used to establish a secure transport connection, such as with the Transport Level Security (TLS). 


Excerpt from JavaServer Faces 2.0: The Complete Reference, McGraw-Hill November 23, 2009

ISBN 978-0071625098

