Encryption is used on the Internet to transmit data, such as payment information, e-mails, or personal data, confidentially and protected against manipulation. Data is encrypted using various cryptographic algorithms based on mathematical operations

In principle, we can distinguish between `symmetric` and `asymmetric` encryption techniques

## Symmetric Encryption
Symmetric encryption, also known as secret key encryption, is a method that uses the same key to encrypt and decrypt the data. This means the sender and the receiver must have the same key to decrypt the data correctly.

If the secret key is shared or lost, the security of the data is no longer guaranteed. Critical actions for symmetric encryption methods represent the distribution, storage, and exchange of the keys. [Advanced Encryption Standard](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) (`AES`) and [Data Encryption Standard](https://en.wikipedia.org/wiki/Data_Encryption_Standard) (`DES`) are examples of symmetric encryption algorithms

## Asymmetric Encryption
Asymmetric encryption, also known as `public-key encryption`, is a method of encryption that uses two different keys:

- a `public key`
- a `private key`

The public key is used to encrypt the data, while the private key is used to decrypt the data. This means anyone can use a public key to encrypt data for someone, but only the recipient with the associated private key can decrypt the data. Examples of asymmetric encryption methods include [Rivest–Shamir–Adleman](https://en.wikipedia.org/wiki/RSA_\(cryptosystem\)) (`RSA`), [Pretty Good Privacy](https://en.wikipedia.org/wiki/Pretty_Good_Privacy) (`PGP`), and [Elliptic Curve Cryptography](https://en.wikipedia.org/wiki/Elliptic-curve_cryptography) (`ECC`). Asymmetric encryption is used in a variety of applications, some of which include:

| E-Signatures | SSL/TLS | VPNs      |
| :----------: | :-----: | --------- |
|   **SSH**    | **PK**  | **Cloud** |
## DES vs. 3DES vs. AES

- **DES (Data Encryption Standard):** Legacy cipher that processes **64-bit blocks** of data. Uses a 64-bit key, but **8 bits** are used as a checksum, leaving an effective key length of only **56 bits** (insecure and easy to break).
    
- **3DES (Triple DES):** Applied DES three times (**Encrypt $\rightarrow$ Decrypt $\rightarrow$ Encrypt**) using up to three keys to increase security, but remains limited and slower due to its legacy structure.
    
- **AES (Advanced Encryption Standard):** The current standard. Uses **128-bit blocks** and key sizes of **128, 192, or 256 bits**. It is significantly faster and more secure than DES/3DES. Used everywhere (**SSH**, **IPsec**, **Wi-Fi**, **OpenSSL**).
    
## Cipher Modes Cheat Sheet

Cipher modes determine **how** block ciphers process messages larger than a single block:

|**Mode**|**Primary Use Case**|**Key Takeaway**|
|---|---|---|
|**ECB** _(Electronic Code Book)_|Legacy / Simple data|**Insecure.** Exposes patterns in data because identical inputs produce identical ciphertext blocks.|
|**CBC** _(Cipher Block Chaining)_|Disk encryption, Email, TLS/SSL|Standard block mode. Used in **VeraCrypt** and **TrueCrypt**.|
|**CFB** _(Cipher Feedback)_|Real-time stream encryption|Good for files in transit and network streams (**BitLocker**).|
|**OFB** _(Output Feedback)_|Real-time communication|Alternative stream mode used in **SSH** and **PKCS**.|
|**CTR** _(Counter)_|Fast stream encryption|Parallelizable and fast; used in **IPsec** and **BitLocker**.|
|**GCM** _(Galois/Counter)_|Secure network traffic|High security; provides both **confidentiality** and **integrity** protection (VPNs, Wireless).|