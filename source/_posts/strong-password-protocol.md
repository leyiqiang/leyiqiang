---
title: "Password-Based Authentications: Strong Password-Based Protocol"
date: 2018-10-12 13:57:25
tags: [Network Security, Authentication Protocols]
categories: Learning Notes
---

Previous posts: {% post_link broadcast-authentication-protocol "The TESLA Broadcast Authentication Protocol" %}, {% post_link Lamport-s-Hash "Lamport's Hash" %}

## Requirements
A Strong Password-Based Protocols should have these requirements:
<!-- more -->
- Prevent attackers to eavesdrop the conversation on an authentication exchange
- Prevent attackers to obtain any information by impersonating either endpoint
- Prevent attackers to do off-line attack *
- Attackers should not be able to obtain any further information from observing any number of legitimate exchanges
- Ensure forward secrecy

\* An attacker impersonating either endpoint will be able to do a single or small amount of on-line password guess, which is unavoidable. However, multiple guesses should generate alarms to the server.

## Strong Password Protocols

### Encrypted Key Exchange (EKE)
This is the basic form of strong password protocol.

**Premise**: 
A has a weak password (i.e. A and B share a weak secret W derived from password, usually hash(password)).

**Approach**: 
A -> B: W{g<sup>a</sup> mod p}
B -> A: W{g<sub>b</sub> mod p}, c1
The key K establishment is based on Diffie-Hellman exchange, so K = g<sup>ab</sup> mod p
A -> B: K{c1, c2}
B -> A: K{c2 - 1}
The last two steps is to authenticate between A and B by providing challenges c1 and c2.

**Does it prevent off-line attack?**
Yes. T can only get Diffle-Hellman messages from off-line guessing, which is impossible to derive K. Also, W{g<sup>a</sup> mode p} is just a random number. For any password W' there exist an r such that W'{r} = W{g<sup>a</sup> mod p}

**How about Forward Secrecy?**
It ensures forward secrecy. a, b, K are only used in this session and will be forgotten later on. If T compromises K, T will not be able to get further information in latter communication.
The only information that will not be forgotten is W. However, T still cannot get K with W.(Unless A or B get compromised and a, b is known by T).


### Simple Password Expotential Key Exchange (SPEKE)

**Approach**:
It uses W in place of g in the Diffle-Hellman exchange:

A -> B: w<sup>a</sup> mod p
B -> A: w<sup>b</sup> mod p

Where K = w<sup>ab</sup> mod p

### Password Derived Moduli (PDM)

**Approach**:
It uses a modulus p which is a function of the password. And uses 2 as the base:

A -> B: 2<sup>a</sup> mod p
B -> A: 2<sup>a</sup> mod p

Where K = 2<sup>ab</sup> mod p


## Flaws and Attacks

### In EKE

If p is only a little more than a power of 2, for example, p = 2<sup>k</sup>+i for small i.
Since g<sup>a</sup> mod p < p. A eavesdropper can try guessing the password, if the password is correct, the result will definitely less than p, if it is not correct, the result will fall randomly between \[0, 2<sup>k+1</sup>-1](\[0, p+p-1]). The attacker will know the result is incorrect if it is larger than p.
The changes of getting a correctly password will be (2<sup>k</sup>+i)/2<sup>b</sup>, which is almost 50%.
To eliminate this flaw, we need to choose a p which is a little less than a power of 2.

### In SPEKE

The eavesdropper might be able to eliminate some pasword guesses based on seeing w<sup>a</sup> mod p. If some of the Ws generated from passwords for use in SPEKE were perfect squares and some not, then if w<sup>a</sup> mod p was not a perfect square, the attacker could know none of passwords resulted in Ws that were perfect square could have been A's password.
To check if a number w=x<sup>2</sup> mod p is a perfect square mod p, we can do w<sup>(p-1)/2</sup> mod p. If the result is 1 mod p, then it is a perfect square.

To eliminate this problem, we simply need to ensure every w is a perfect square mod p.

## Augmented Strong Password Protocols

**Goal**: If an attacker T compromises B, T cannot succeed with an offline attack

**Approach**: Avoiding storing W in B's database but only something derived from W.

**Requirement**: The password must be strong so that the off-line attack will not succeed.

### Augmented PDM

1. B stores A, p, 2<sup>w</sup> mod p (p is derived from password)
2. A -> B: 2<sup>a</sup> mod p
3. B -> A: 2<sup>b</sup> mod p, hash(2<sup>ab</sup> mod p, 2<sup>bw</sup> mod p)
4. A -> B: hash'(2<sup>ab</sup> mod p, 2<sup>bw</sup> mod p) (this hash' is for B to authenticate A)

Note that it is **not possible** for attacker T to do this:
T impersonate A, sends 2<sup>k</sup> mod p to B. T gets 2<sup>b</sup> mod p and hash(2<sup>kb</sup> mod p, 2<sup>bw</sup> mod p) from B, then do offline attacks by trying different w.

Because p is derived from password, so B will recognize it's not a valid message.

### Augmentation Based on RSA Public Keys

1. B stores A, W, {A}(A's public key), Y=W'{\[A]}(A's private key)
2. A -> B: A, W{g<sup>a</sup> mod p}
3. B choose b and challenge c
4. B -> A: W{g<sup>b</sup> mod p}, ((g<sup>ab</sup> mod p)){Y}
5. A -> B: \[hash(g<sup>ab</sup> mod p, c)]<sub>sign-A</sub>

Note that if T breaks into B, T can get W, but T cannot get W'

### Secure Remote Password (SRP)

1. B stores A, g<sup>w</sup> mod p
2. A choose a, compute W from password
3. A -> B: A, g<sup>a</sup> mod p
4. B choose b, c1 and a 32-bit number u
5. B sends g<sup>b</sup>+g<sup>w</sup> mod p, u, c1
(K = g<sup>b(a+uW)</sup> mod p)
6. A -> B: K{c1}, c2
7. B -> A: K{c2}

** How is the common key computed on both ends? **
For A: g<sup>b</sup>+g<sup>w</sup> mod p - g<sup>w</sup> mod p = g<sup>b</sup>
Then (g<sup>b</sup>)<sup>(a+uw)</sup> mod p
For B: (g<sup>a</sup>)<sup>b</sup>*(g<sup>w</sup>)<sup>bu</sup> mod p