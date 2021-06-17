---
title: "Password-Based Authentications: Lamport's Hash"
date: 2018-10-09 14:37:46
tags: [Network Security, Authentication Protocols]
categories: Learning Notes
---

## Intro
Suppose A wants to log into a server B, and A's work station has no user specific configurations(e.g. CAs or private keys). Here are several ways A can use a password to authenticate to server:
- Send password directly to B (vulnerable to eavesdropping and impersonating)
- Diffie-Hellman key establishment(vulnerable to impersonating)
- SSL (need trust configuration and certificates)
- Use A's hash(password) as a secret key:
  B->A: R
  A->B: hash(pwd, R)
  (vulnerable to dictionary attack)
 
- Use one time password scheme like **Lamport's Hash**
- Use Strong password protocol


## Lamport's Hash
### Implementations
A->B: A
B->A: n(a reasonably large number)
B: Computes x<sub>n</sub>=hash<sup>n</sup>(password)
A->B: x<sub>n-1</sub>=hash<sup>n-1</sup>(password)
B: Computes x<sub>n</sub>' = hash(x<sub>n-1</sub>)
B then compares x<sub>n</sub>' and x<sub>n</sub>, if they match, B consider the response is valid, then replace n to n-1

### Advantages
- Prevents Eavesdropping attack
- If attacker compromises B, it cannot find the pre image of A's password.

### Problems
- If n get to 1, A needs to set its password again with B. This process is vulnerable to Man in the Middle Attack(Small N attack):
    1. A sends B A
    2. B sends A n. T intercepts it and replaces n to n' = n-10
    3. A computes h = hash<sup>n-11</sup>(password). T intercepts it and replace h to h' = hash<sup>n-1</sup>(password) = hash<sup>10</sup>(h)
    4. B hashes h' once, get the match.

    A possible solution to this is for A to remember the approximate value of n, and do some sanity check for n.
    Now T can calculate the next 10 hashes that matches B's expectations.
This allows T login as A multiple times.

- When n drops to 1, A have to change its password for security issue(Enhancement: using salt--hash(password|salt)) 

- Time complexity

## Human and Paper

Note that Lamport's Hash can be used in environments that workstation do not have the hash. We call this Human and Paper environment(vs. Workstation environment).
The way it works is that the values hash<sup>i</sup>(password) will be pre-computed for all i in n. Then printed these hashes on a paper and give it to A. A crosses out hashes whenever it uses that hash to log in. Next time A will use the next value to login.