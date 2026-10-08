At its core, a wireless network is a computer network that doesn't use physical cables to connect devices. Instead, it uses **radio frequency (`RF`)** technology to transmit data through the air.

- **How it works:** Devices like your laptop or smartphone have a wireless adapter. This adapter takes digital data and converts it into radio signals to send out. Other devices receive these signals with their own adapters and convert them back into usable data.
- **Range:** As the module explains, the range varies. A **Local Area Network (LAN)** using **WiFi** covers a small area like a home or office. A **Wireless Wide Area Network (WWAN)** uses cellular technology (`4G`, `5G`) to cover a whole city or region.

The central device in a typical WiFi network is the **Wireless Access Point (WAP)**, which is often built into your home router. It acts as a bridge, connecting your wireless devices to the wired network (the internet) and controlling who can access the network.

# The WiFi Connection Process
This part details how your device actually joins a WiFi network. To do this, your device and the WAP need to have a structured conversation using a specific protocol.

- **The Protocol:** The standard for this communication is called **IEEE 802.11**. It defines all the technical rules for how wireless devices talk to each other.
- **The "Handshake":** When your device wants to join a network, it sends out a message called a **connection request frame** (or **association request**). Think of this as your device introducing itself to the WAP.

As the module highlights, this request contains several key pieces of information:

| `MAC address`                  | A unique identifier for the device's wireless adapter.                                       |
| ------------------------------ | -------------------------------------------------------------------------------------------- |
| `SSID`                         | The network name, also known as the `Service Set Identifier` of the WiFi network.            |
| `Supported data rates`         | A list of the data rates the device can communicate.                                         |
| `Supported channels`           | A list of the `channels` (frequencies) on which the device can communicate.                  |
| `Supported security protocols` | A list of the security protocols that the device is capable of using, such as `WPA2`/`WPA3`. |

## WEP Challenge-Response Handshake

### 1. Key Terminology & Concepts
- **WEP Key:** The pre-shared passphrase/password configured on the router and client (e.g., `secret123`).
    
- **IV (Initialization Vector):** A short, 24-bit random number generated for _every_ packet to ensure different encrypted outputs for duplicate messages. Sent in plain text alongside the packet.
    
- **RC4:** The stream cipher algorithm used by WEP. It combines the `IV + WEP Key` to generate a stream of pseudorandom bits.
    
- **Keystream:** The output of RC4. This is the random stream of bits used as a "scrambler."
    
- **Plaintext:** The readable, unencrypted data payload.
    
- **Ciphertext:** The encrypted, unreadable data sent over the air.
    
- **ICV (Integrity Check Value):** A 32-bit **CRC-32** hash added to the end of a payload to detect bit corruption.
    
- **Challenge String:** A 128-byte random string used during **Shared Key Authentication** to test if a client knows the WEP Key.
    

### 2. Encryption Process (Sending Data)
1. **Combine Keys:** Client merges `IV (24-bit) + WEP Key`.
    
2. **Generate ICV:** Client computes a **CRC-32** hash of the Plaintext.
    
3. **Generate Keystream:** Client feeds the combined key into the **RC4 cipher engine**.
    
4. **Scramble (XOR):** Client performs an XOR operation on `(Plaintext + ICV) ⊕ Keystream` to produce the **Ciphertext**.
    
5. **Transmit:** The packet is sent over the air as `[ Unencrypted IV ] + [ Ciphertext ]`.
    

```
[ IV + WEP Key ] ──> ( RC4 ) ──> [ Keystream ]
                                      ⊕
[ Plaintext + CRC-32 (ICV) ] ───────────────> [ Ciphertext ]
```

### 3. Shared Key Authentication (The Challenge String)
Before data transmission, the client authenticates using a 4-step challenge-response process:

1. **Request:** Client asks to authenticate.
    
2. **Challenge:** Router sends a 128-byte unencrypted **Challenge String** (Plaintext).
    
3. **Response:** Client encrypts the Challenge String with its WEP Key + IV and sends it back (Ciphertext).
    
4. **Verification:** Router decrypts the response. If it matches the original Challenge String, access is granted.
    

### 4. Fundamental Security Vulnerabilities (Red Team / Offensive Takeaways)

### A. The IV Reuse Flaw (Key Recovery Attack)
- **Problem:** The 24-bit IV space is way too small ($\approx 16.7 \text{ million combinations}$). On a busy network, IVs repeat within minutes.
    
- **Exploit:** When the same IV is used, RC4 outputs the **exact same Keystream**. By capturing a large volume of packets containing repeated IVs (using tools like `aircrack-ng`), an attacker can solve for the static WEP Key mathematically in under a minute.
    

### B. Challenge String Keystream Leakage
- **Problem:** Shared Key Authentication exposes both the **Plaintext** (Challenge String) and **Ciphertext** (Response) over the air.
    
- **Exploit:** Because $\text{Keystream} = \text{Plaintext} \oplus \text{Ciphertext}$, any eavesdropper watching the handshake can immediately derive the Keystream and forge authentication without knowing the WEP password.
    

### C. CRC-32 Bit-Flipping / Tampering
- **Problem:** CRC-32 is a non-cryptographic, linear error-detection mechanism, not a secure integrity check.
    
- **Exploit:** XOR encryption is malleable. Flipping a bit in the Ciphertext flips the exact corresponding bit in the decrypted Plaintext. An attacker can alter message contents in mid-air, mathematically recalculate the required CRC change, flip the corresponding bits in the encrypted ICV, and the router will accept the tampered packet as authentic (enabling ARP Packet Injection).

## Security Features
WiFi networks have several security features to protect against unauthorized access and ensure the privacy and integrity of data over network. Some of de the leading features include but are not limited to:
### Encryption
We can use various encryption algorithms to protect the confidentiality of data transmitted over wireless networks. The most common encryption algorithms in WiFi networks are Wired Equivalent Privacy (WEP), WiFi Protected Access 2 (WPA2), and WiFi Protected Access 3 (WPA3)
### Access Control
WiFi networks are configured by default to allow authorized devices to join the network using specific authentication methods. However, these methods can be changed by requiring a password or a unique identifier (such as a MAC address) to identify authorized devices.
### Firewall
A firewall is a security system that controls incoming and outgoing network traffic based on predetermined security rules. For example, WiFi routers often have built-in firewalls that can block incoming traffic from the Internet and protect against various types of cyber threats.

## Encryption Protocols
### WEP
See "[[#WEP Challenge-Response Handshake]]"
#### WAP
`WAP` can use different encryption algorithms, including Advanced Encryption Standard (AES). It provides the highest level of security and is not susceptible to the same types of attacks as WEP. In addition, WPA uses more secure authentication methods, such as a Pre-Shared Key (`PSK`) or an 802.1X authentication server, which provide stronger protection against unauthorized access. Although older devices may not support WPA is compatible with most devices and operating systems. All wireless networks, especially in critical infrastructure like offices, should generally implement at least `WPA2` or even `WPA3` encryption.

## Authentication Protocols
### LEAP, PEAP & EAP
`Lightweight Extensible Authentication Protocol` (LEAP) and `Protected Extensible Authentication Protocol` (PEAP) are authentication protocols used to secure wireless networks to provide a secure method for authenticating devices on a wireless network and are often used in conjunction with WEP or WPA to provide an additional layer of security. 

LEAP and PEAP are both based on the Extensible Authentication Protocol (EAP), a framework for authentication used in various networking contexts. However, one key difference between LEAP and PEAP is how they secure the authentication process.

- LEAP uses a shared key for authentication, which means that the same key is used for encryption and authentication. 

This can make it relatively easy for us to gain access to the network if the key is compromised. However, PEAP uses a more secure authentication method called tunneled Transport Layer Security (TLS). This method establishes a secure connection between the device and the WAP using a digital certificate, and an encrypted tunnel protects the authentication process. This provides more robust protection against unauthorized access and is more resistant to attacks.

### TACACS+
Terminal Access Controller Access-Control System Plus (TACACS+) is a protocol used to authenticate and authorize users accessing network devices, such as routers and switches. When a WAP sends an authentication request to a `TACACS+` server, the request typically includes the user's credentials and other information about the session.

Encrypting the authentication request helps to ensure that this sensitive information is not visible to unauthorized parties who may be able to intercept the request. At the same time, it is being transmitted over the network. It also helps prevent tampering with the request or replacing it with a malicious request of their own.

Several encryption methods may be used to encrypt the authentication request, such as `SSL`/`TLS` or `IPSec`. The specific encryption method used may depend on the configuration of the `TACACS+` server and the capabilities of the WAP.

## Disassociation Attack
A `Disassociation Attack` is a type of `all` wireless network attack that aims to disrupt the communication between a WAP and its clients by sending disassociation frames to one or more clients.

The WAP uses disassociation frames to disconnect a client from the network. When a WAP sends a disassociation frame to a client, the client will disconnect from the network and have to reconnect to continue using the network.

We can launch the attack from `within` or `outside` the network depending on our location and network security measures. The purpose of this attack is to disrupt the communication between the WAP and its clients, causing the clients to disconnect and possibly causing inconvenience or disruption to the users. We can also use it as a precursor to other attacks, such as a MITM attack, by forcing the clients to reconnect to the network and potentially exposing them to further attacks.

## Wireless Hardening
There are many different ways to protect wireless networks. However, some examples should be considered to increase wireless networks' security dramatically. These are the following, but not limited to:

- Disabling broadcasting
- WiFi Protected Access
- MAC filtering
- Deploying EAP-TLS
### Disabling Broadcasting
Disabling the broadcasting of the SSID is a security measure that can help harden a WAP by making it more difficult to discover and connect to the network. When the SSID is broadcasted, it is included in beacon frames regularly transmitted by the WAP to advertise the availability of the network. By disabling the broadcasting of the SSID, the WAP will not transmit beacon frames, and the network will not be visible to devices that are not already connected to the network.
### WPA
Again, WPA provides strong encryption and authentication for wireless communications, helping protect against unauthorized network access and sensitive data interception. WPA includes two main versions:

1. WPA-Personal
2. WPA-Enterprise

WPA-Personal, designed for home and small business networks, and WPA-Enterprise, designed for larger organizations and uses a centralized authentication server (e.g., RADIUS or TACACS+) to verify the identity of clients.
### MAC Filtering
MAC filtering is a security measure that allows a WAP to accept or reject connections from specific devices based on their MAC addresses. By configuring the WAP to accept connections only from devices with approved MAC addresses, it is possible to prevent unauthorized devices from connecting to the network.
### Deploying EAP-TLS
EAP-TLS is a security protocol used to authenticate and encrypt wireless communications. It uses digital certificates and PKI to verify the identity of clients and establish secure connections. Deploying EAP-TLS can help to harden a WAP by providing strong authentication and encryption for wireless communications, which can protect against unauthorized access to the network and the interception of sensitive data.