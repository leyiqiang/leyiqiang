---
title: "Password-Based Authentications: The Broadcast Authentication Protocol"
date: 2018-10-11 21:19:57
tags: [Network Security, Authentication Protocols]
categories: Learning Notes
---

Previous post: {% post_link Lamport-s-Hash "Lamport's Hash" %}.

## Approach

- Sender A splits time into intervals of equal duration I.
- A forms a one-way chain of self-authenticating values X<sub>n</sub>, assign the values sequentially. The one-way chain is used in the reverse order of generation, so any value of a time interval can be used to derive values of previous time intervals.
- A defines a disclosure time for one-way chain values. A will publish the value after the disclosure time. For example, on time interval i, A publish X<sub>n-(i-1)</sub>.
- A attaches MAC to each packet, and use the key on that time interval to compute MAC: MAC(M<sub>i</sub>, X<sub>n-i</sub>)(on time i). A also sends the value it can disclose.
- When B receives the packet, it checks that A has not disclose the value X to  make sure the key is still secret. Then B buffers the packet.
- B also checks the disclosed value is correct and verify MAC of this packet(base on time interval). If MAC is correct. B accept the packet.


## One-Way Chains

{% asset_img one-way-chain.png %}

Any value of a time interval can be used to derive values of previous time intervals.
So even if some disclosed keys are lost, B can still recover the key chain. 


## Requirements

- Receivers should be loosely time synchronized with the sender.
- Either receiver or senders must buffer some messages.

## Advantages

- Low communication overhead (and for generation and verification of authentication information)
- Limited buffering required for the sender and the receiver, hence timely authentication for each individual packet
- Robustness to packet loss
- Scales to a large number of receivers
- Low authentication delay

**Source: [The TESLA Broadcast Authentication Protocol](https://users.ece.cmu.edu/~adrian/projects/tesla-cryptobytes/paper/)**