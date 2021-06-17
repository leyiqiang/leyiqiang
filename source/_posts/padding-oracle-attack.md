---
title: Padding Oracle Attack
date: 2018-10-18 19:35:48
tags: [Network Security]
categories: Learning Notes
---

### Requirement
The system must use CBC mode with [PKCS7](https://tools.ietf.org/html/rfc2315) for the padding block.

This is a chosen-ciphertext attack, The adversary can submit encrypted messages to the system, and the system will return valid or invalid as response. Assume block size is 8.

### Background
|Encryption   | Decryption  |
|---|---|
| C<sub>i</sub> = Encrypt(P<sub>i</sub> ⊕ C<sub>i-1</sub>)  |  P<sub>i</sub> = Decrypt(C<sub>i</sub> ⊕ C<sub>i-1</sub>)  |

<!--more-->
### Steps
1. For each block C[i], the attacker creates C': 
    ```
    C' = C[i-1] ⊕ 0xX ⊕ 0x01
    ```
    where X is a guessed byte.
2. The attacker then submits (IV, C' + C[i]) to the Oracle. Then the oracle will decrypt this message:
	```
    Pi’ = Decrypt(C[i]) ⊕ C'
        = Decrypt(C2[i]) ⊕ C2[i-1] ⊕ 00…000X ⊕ 00…0001
        = Pi ⊕ 00…000X ⊕ 00…0001
    ```
    If the last byte of Pi = X, then the last byte of Pi’ will be 01, and Oracle will indicate this message is valid. Otherwise Oracle will return invalid because the number doesn’t match padding length. 
3. The attacker can just iterate all the possible bytes of X (256 possibilities) and find the one that returns valid.
4. Then, to guess the last second byte, attacker can submit 
   ```
   C' = C2[i-1] ⊕ 00…00YX ⊕ 00…0202 
   ```
   where Y is the guessed byte. 
5. The attacker can do the same thing again and again until all the bytes of this block is guessed correctly.

### Protection
- Use MAC for integrity protection
- Re-implement the system so that it won't return any useful information to the attacker