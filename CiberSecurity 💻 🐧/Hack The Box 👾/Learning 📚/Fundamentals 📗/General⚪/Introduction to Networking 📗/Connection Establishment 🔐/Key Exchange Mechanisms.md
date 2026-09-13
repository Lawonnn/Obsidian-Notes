Key exchange methods are used to exchange [cryptographic keys](https://www.cloudflare.com/learning/ssl/what-is-a-cryptographic-key/) between two parties securely. These methods typically work by allowing the two parties to agree on a `shared secret key` over an insecure communication channel that encrypts the communication between them


# Key Exchange Method
## Diffie-Hellman
The **Diffie-Hellman (DH) Key Exchange** is a cryptographic protocol that enables two parties to securely establish a **shared secret key** over an insecure, public channel

### The Paint Mixing Analogy
The classic way to visualize Diffie-Hellman is through color mixing:

![[Pasted image 20260801154921.png|357]]

1. **Public Base (Yellow Paint):** Alice and Bob publicly agree on a starting color (**Yellow**). An eavesdropper (Eve) sees this public color.

2. **Private Colors:** Alice picks a secret color (**Red**). Bob picks a secret color (**Blue**). Neither reveals their private color to anyone.

3. **Public Mix:**
    - Alice mixes Yellow + Red = **Orange**, and sends Orange across the wire to Bob.
    - Bob mixes Yellow + Blue = **Light Blue**, and sends Light Blue across the wire to Alice.
    - Eve sees Orange and Light Blue passing through the network, but because separating mixed paint back into its original colors is practically impossible, she cannot extract Red or Blue.
        
4. **Shared Secret:**
    - Alice takes Bob's **Light Blue** paint and adds her secret **Red** paint $\rightarrow$ **Brown**.
    - Bob takes Alice's **Orange** paint and adds his secret **Blue** paint $\rightarrow$ **Brown**.

Both end up with the exact same final color (**Brown**), while Eve only saw Yellow, Orange, and Light Blue.

### Vulnerability
Standard Diffie-Hellman **does not provide authentication**. It guarantees that you and the person on the other end share a secret, but it does not prove _who_ is on the other end.

```
[ Alice ] <- Secret S1 -> [ Attacker (Eve) ] <- Secret S2 -> [ Bob ]
```

- If Eve sits in the middle of an unauthenticated DH exchange, she intercepts Alice's public key $A$ and sends her own key $E_1$ to Alice.
- She intercepts Bob's public key $B$ and sends her own key $E_2$ to Bob.
- Eve establishes shared secret $S_1$ with Alice and $S_2$ with Bob, allowing her to decrypt, inspect, modify, and re-encrypt all traffic transparently.

## RSA (Rivest-Shamir-Adleman)
RSA relies on a fundamental rule of asymmetric cryptography: **what one key encrypts, only the matching key can decrypt**.

- **Public Key:** Shared freely with the world. Anyone uses it to **encrypt** a message meant for you.
- **Private Key:** Kept strictly secret on your system. Only you use it to **decrypt** ciphertext encrypted with your public key.
- **Digital Signatures (Reversed):** You encrypt data (or a hash) with your **private key**, and anyone can verify it using your **public key** to prove the message came from you.

It is also widely used in many other applications and protocols that require secure communication and data protection, including but not limited to:
- Encrypting and signing messages to provide confidentiality and authentication
- Protecting data in transit over networks, such as in the [Secure Socket Layer](https://www.cloudflare.com/learning/ssl/what-is-ssl/) (`SSL`) and `TLS` protocols
- Generating and verifying digital signatures, which are used to provide authenticity and integrity for electronic documents and other digital data
- Authenticating users and devices, such as in the [Public Key Cryptography for Initial Authentication in Kerberos](https://www.ietf.org/rfc/rfc4556.txt) (`PKINIT`) protocol used by the Kerberos network authentication system
- Protecting sensitive information, such as in the encryption of personal data and confidential documents

## ECDH (Elliptic Curve Diffie-Hellman)
Elliptic curve Diffie-Hellman (`ECDH`) is a variant of Diffie-Hellman key exchange that uses elliptic curve cryptography (`ECC`) to generate the shared secret key. It has the advantage of being more efficient and secure than the original Diffie-Hellman algorithm, including but not limited to:

- Establishing secure communication channels, such as in the `TLS` protocol
- Providing forward secrecy, which ensures that past communications cannot be revealed even if the private keys are compromised
- Authenticating users and devices, such as in the Internet Key Exchange (`IKE`) protocol used in VPNs

## ECDSA
The Elliptic Curve Digital Signature Algorithm (`ECDSA`) uses elliptic curve cryptography (`ECC`) to generate digital signatures that can authenticate the parties involved in the key exchange.


## Summary

| **Algorithm**                                | **Acronym** | **Security**                                                                                                                   |
| -------------------------------------------- | ----------- | ------------------------------------------------------------------------------------------------------------------------------ |
| `Diffie-Hellman`                             | `DH`        | Secure with strong params and authentication; slower than `ECDH` at similar security                                           |
| `Rivest–Shamir–Adleman`                      | `RSA`       | Widely used and considered secure with adequate key sizes; more computationally heavy than `ECC` at comparable security levels |
| `Elliptic Curve Diffie-Hellman`              | `ECDH`      | Provides enhanced security and speed compared to traditional `Diffie-Hellman`                                                  |
| `Elliptic Curve Digital Signature Algorithm` | `ECDSA`     | Provides enhanced security and efficiency for digital signature generation                                                     |

## Internet Key Exchange (IKE)
**Internet Key Exchange (IKE)** is the control-plane protocol used primarily by **IPsec VPNs** to authenticate parties, negotiate security parameters, and set up an encrypted tunnel using cryptographic algorithms like **Diffie-Hellman/RSA** (for key setup) and **AES** (for bulk encryption).

### Key Concepts
- **Core Role:** Establishes and manages secure VPN sessions and authenticates clients/servers.
- **Operating Modes:**
    - **Main Mode (More Secure):** Uses **3 two-way exchanges (6 messages total)**. It hides identity information behind encryption, prioritizing security over speed.
    - **Aggressive Mode (Faster):** Uses **2 exchanges (3 messages total)** by bundling all parameters into the initial phase. It is faster, but **lacks identity protection** (transmits identity in plaintext), making it vulnerable to sniffing.

- **Pre-Shared Keys (PSK):**
    - An optional secret password shared out-of-band before the exchange to authenticate both ends.
    - **Trade-off:** Easy to configure, but weak or poorly exchanged PSKs are susceptible to offline brute-forcing and Man-in-the-Middle (MitM) attacks.