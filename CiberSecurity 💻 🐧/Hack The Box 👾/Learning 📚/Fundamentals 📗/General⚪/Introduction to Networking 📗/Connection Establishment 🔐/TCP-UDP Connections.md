`TCP` is a connection-oriented protocol that ensures that all data sent from one computer to another is received. It is like a telephone conversation where both parties remain connected until the call is terminated. If an error occurs while sending data, the receiver sends a message back so the sender can resend the missing data. This makes `TCP` reliable and slower than UDP because more time is required for transmission and error recovery.

`UDP`, on the other hand, is a connectionless protocol. It is used when speed is more important than reliability, such as for video streaming or online gaming. With `UDP`, there is no verification that the received data is complete and error-free. If an error occurs while sending data, the receiver will not receive this missing data, and no message will be sent back to resend it. Some data may be lost with `UDP`, but the overall transmission is faster.

# IP Packet
An [Internet Protocol](https://en.wikipedia.org/wiki/Internet_Protocol) (`IP`) packet is the data area used by the network layer of the [Open Systems Interconnection](https://en.wikipedia.org/wiki/OSI_model) (`OSI`) model to transmit data from one computer to another. It consists of a header and the payload, the actual payload data.

## IP Header
### Version
Indicates which version of the IP protocol is being used (IPv4 or IPv6)
### Internet Header Length
Indicates the size of the header in 32-bit words
##### Security Relevance
1. **Header Parsing Attacks:** If an attacker crafts a raw packet with an invalid IHL value (e.g., setting **IHL = 3**, which claims the header is 12 bytes), poorly coded network parsers or older operating systems might crash trying to process it.
2. **Firewall Bypass / Evasion:** Security devices inspect the payload immediately following the IP header (like TCP or UDP ports). If an attacker misreports the IHL value, a vulnerable firewall might look for the TCP header at the wrong offset, failing to detect a malicious payload.
### Class of Service (Type of Service (ToS) or Differentiated Services (DiffServ))
Think of network traffic like cars on a busy highway:
- **Standard Web Browsing or Email:** Regular cars driving in normal lanes. If traffic backs up, they wait in line.

- **Live Voice (VoIP) or Video Streaming:** Ambulances or VIP buses with emergency sirens. They need a fast lane because delays cause stuttering, frozen screens, or dropped calls.

The Class of Service field acts like a **VIP Pass windshield sticker**. A router reading this 8-bit field can put high-priority packets in a "fast lane" queue and drop lower-priority packets first if memory fills up.
##### How it's used
Originally called **ToS**, modern networking rebranded these 8 bits into two main functions:
- **DSCP (Differentiated Services Code Point - 6 bits):**
    - Specifies the actual priority class (e.g., **Best Effort** for normal traffic vs. **Expedited Forwarding** for low-latency voice calls).
- **ECN (Explicit Congestion Notification - 2 bits):**
    - Allows routers to warn senders that a network bottleneck is forming _before_ packets start getting dropped, telling them to slow down gracefully.

##### Security Relevance
- **QoS Evasion & Traffic Prioritization:** Malicious actors or malware can craft packets with high-priority DSCP flags set to bypass queueing limits or gain bandwidth precedence on a compromised target network.
- **Reconnaissance & Fingerprinting:** Internal corporate networks often use specific custom DSCP policies for internal tools. Analyzing captured traffic for unusual ToS values can help reveal internal network architecture or specialized device types (like IP phones).

### Total length
Specifies the total length of the packet in bytes

### Identification (ID)
The **Identification (ID)** field is a **16-bit** number that acts like a unique **tracking label** for an IP packet.
Its main job is to help the receiving machine regroup pieces of a packet if it gets chopped up (**fragmented**) while traveling across the network.

#### Why is important
When a large packet travels across different network segments, a router along the path might need to break it into smaller fragments because the next leg of the journey has a smaller size limit (**MTU**).
When multiple applications on your computer are sending data simultaneously (e.g., streaming music while downloading a file), thousands of fragmented pieces arrive at the target machine mixed together.
Without an ID field, the receiver wouldn't know which fragments belong to which original packet!


> [!example]
> Original Packet A [ID: 0x1337] ──(Router Splits)──>
> 	[Frag 1 (ID: 0x1337)]
> 	[Frag 2 (ID: 0x1337)]
> 
> Original Packet B [ID: 0x9999] ──(Router Splits)──>
> 	[Frag 1 (ID: 0x9999)]
> - Every fragment created from **Packet A** keeps the same **ID: `0x1337`**.
>     
> - Every fragment created from **Packet B** keeps the same **ID: `0x9999`**.
>     
> - The receiver inspects the ID field to group matching pieces back together safely before reassembling the full data.

We may see a computer with multiple IP addresses in different networks. Here we should pay attention to the `IP ID` field. It is used to identify fragments of an IP packet when fragmented into smaller parts. It is a `16-bit` field with a unique number ranging from `0-65535`.

> [!example]
> If a computer has multiple IP addresses, the `IP ID` field will be different for each packet sent from the computer but very similar. Like this:
> 
> ```
> IP 10.129.1.100.5060 > 10.129.1.1.5060: SIP, length: 1329, id 1337
> IP 10.129.1.100.5060 > 10.129.1.1.5060: SIP, length: 1329, id 1338
> IP 10.129.1.100.5060 > 10.129.1.1.5060: SIP, length: 1329, id 1339
> IP 10.129.2.200.5060 > 10.129.1.1.5060: SIP, length: 1329, id 1340
> IP 10.129.2.200.5060 > 10.129.1.1.5060: SIP, length: 1329, id 1341
> IP 10.129.2.200.5060 > 10.129.1.1.5060: SIP, length: 1329, id 1342
> ```
> We can see from the output that two different IP addresses are sending packets to IP address 10.129.1.1. However, from the `IP ID`, we can see that the packets are continuous. This strongly indicates that the two IP addresses belong to the same host in the network.

#### Security Relevance
Because the ID field is key to how operating systems handle memory and network state, it is heavily targeted in penetration testing and offensive security:

- **The Idle / IPID Idle Scan (Nmap `-sI`):** Older operating systems used predictable, strictly sequential ID counters. Attackers can use a quiet "zombie" computer on the network to launch stealthy port scans without revealing their own IP address, simply by monitoring how the zombie's IPID counter increments!
- **Teardrop & Fragmentation Attacks:** Attackers can manually craft custom fragments using the **same ID number**, but with overlapping or corrupted fragment offset locations. When older systems try to reassemble these conflicting pieces, memory buffer overflows or crashes occur (causing a Denial of Service / DoS).
### Flags
The **Flags** field is a **3-bit** section located right next to the **Identification (ID)** field. Its sole job is to control and give directions on **how fragmentation should be handled**.

| **Bit Position** | **Flag Name**      | **Abbreviation** | **What 1 (ON) Means**                                                             | **What 0 (OFF) Means**                                             |
| ---------------- | ------------------ | ---------------- | --------------------------------------------------------------------------------- | ------------------------------------------------------------------ |
| **Bit 0**        | _Reserved_         | N/A              | Unused. Must **always be `0`**.                                                   | Always `0`.                                                        |
| **Bit 1**        | **Don't Fragment** | **DF**           | **"DO NOT chop this packet up!"** If it's too big for a network segment, drop it. | Routers are **allowed to fragment** the packet if needed.          |
| **Bit 2**        | **More Fragments** | **MF**           | **"More pieces are coming!"** This packet is a chunk of a larger message.         | **"This is the LAST piece"** (or the packet was never fragmented). |

### Fragment Offset
The **Fragment Offset** field is a **13-bit** number that acts as the **assembly instructions** for a chopped-up packet.

Its job is to tell the receiving machine: _"Where in the original, unfragmented payload does this specific piece belong?"_

#### Security Relevance
In penetration testing, exploit development, and HTB labs, the **Fragment Offset** field is a notorious vector for triggering vulnerabilities in operating systems and security appliances:
##### Overlapping Fragment Attacks (e.g., Teardrop Attack)
An attacker intentionally crafts two fragments with **conflicting offsets** that overlap each other in memory:
- **Fragment 1:** Bytes `0` to `1479` (`Offset = 0`).
- **Fragment 2:** Bytes `800` to `2000` (`Offset = 100` $\rightarrow 100 \times 8 = \mathbf{800}$).
When an unpatched target operating system receives Fragment 2, it attempts to overwrite memory it already allocated for Fragment 1. Poorly handled pointer arithmetic causes a **kernel panic, blue screen (BSOD), or system crash**.
##### Fragmentation Evasion (IDS/IPS Bypass)
Intrusion Prevention Systems (IPS) and target operating systems sometimes handle overlapping fragments differently:
- **First-write wins (BSD/Linux style):** Keeps the original bytes from Fragment 1.
- **Last-write wins (Windows style):** Overwrites the old bytes with the new bytes from Fragment 2.
Attackers can send an intentionally overlapping fragment containing benign data to blind the IPS, knowing that when the target OS reassembles the packet, the malicious payload will overwrite the benign data and execute on the target.
##### Out-of-Order Memory Exhaustion
If an attacker sends thousands of fragments with huge offset values (e.g., `Offset = 7000`) but **never sends the initial fragments** (`Offset = 0`), the target operating system reserves memory buffers waiting for the missing pieces. This can lead to **memory starvation and Denial of Service (DoS)**.

### Time To Live
Specifies how long the packet may remain on the network.
Every router that forwards an IP packet **decrements its TTL value by `1`**.
When a packet's TTL hits **`0`**, the router drops the packet immediately and sends back an **ICMP Type 11 (Time Exceeded)** error message to the source IP.

### Protocol
Specifies which protocol is used to transmit the data, such as TCP or UDP

### Checksum
The **Header Checksum** (or simply **Checksum**) is a **16-bit** error-detection value used to verify that the IP header was not corrupted in transit.
It acts like a **digital checksum/hash** specifically designed to protect the integrity of the routing instructions in the IPv4 header. It's something like the Cyclic Redundancy Check (CRC)

##### How It Works
1. **At the Sender:** The sending host calculates a mathematical sum (using a technique called **ones' complement sum**) over all the bytes in the IPv4 header and places that value into the **Checksum** field.
2. **At Every Router Along the Path:** The router recalculates the checksum over the received header.
    - If the calculated value matches the checksum in the header, the packet is processed normally.
    - If the values do **NOT** match (e.g., due to electrical noise, faulty hardware, or bit flips over the wire), the router silently **drops the packet**.

> **Important Detail:** The checksum only covers the **IP header** itself, **not** the underlying data payload (like TCP/UDP data). Higher-layer protocols maintain their own separate checksums to protect payload data.

### Source/Destination
Indicate where the packet was sent from and where it is being sent to

### Options
The **Options** field is a variable-length section at the very end of the IPv4 header (ranging from **0 to 40 bytes**).

While the first 20 bytes of an IPv4 header are fixed and mandatory, the Options field was designed to allow network engineers and systems to add extra control, testing, or security parameters to a packet when necessary.

### Padding
The **Padding** field is a variable-length buffer of **zero-bits (`0`s)** appended to the end of the IPv4 header whenever the **Options** field is used.
Its sole purpose is **data alignment**: it ensures that the total IPv4 header length ends on a clean **32-bit (4-byte) boundary**.
##### Why Is Padding Necessary?
As we covered back in the **IHL (Internet Header Length)** section, the IPv4 header length is measured in **32-bit words** (units of 4 bytes).
$$\text{Header Length in Bytes} = \text{IHL} \times 4$$
Because of this mathematical design, the total length of an IPv4 header **must always be a multiple of 4 bytes** (e.g., 20 bytes, 24 bytes, 28 bytes... up to 60 bytes).
- **Without Options:** The standard IPv4 header is exactly **20 bytes** ($5 \times 4$). No padding is needed!
- **With Options:** Options fields can be any length (e.g., 1, 2, or 3 bytes long). If an option makes the total header length **21 bytes**, processor hardware cannot divide 21 by 4 cleanly to store it in the **IHL** field.

### Summary
| **Field**                | **Size**     | **Core Function**                                                       |
| ------------------------ | ------------ | ----------------------------------------------------------------------- |
| **Version**              | 4 bits       | Identifies IP version (`4` for IPv4).                                   |
| **IHL**                  | 4 bits       | Header size in 32-bit words (20–60 bytes).                              |
| **Class of Service**     | 8 bits       | QoS priority labels (DSCP / ECN).                                       |
| **Total Length**         | 16 bits      | Entire packet size (Header + Data) in bytes.                            |
| **Identification (ID)**  | 16 bits      | Unique sequence label for grouping fragments (or tracking hosts).       |
| **Flags**                | 3 bits       | Fragmentation rules (**DF** = Don't Fragment, **MF** = More Fragments). |
| **Fragment Offset**      | 13 bits      | Reassembly index for data chunks (measured in 8-byte blocks).           |
| **Time to Live (TTL)**   | 8 bits       | Hop-counter to prevent loops (used for OS fingerprinting & traceroute). |
| **Protocol**             | 8 bits       | Identifies Layer 4 payload (`1` = ICMP, `6` = TCP, `17` = UDP).         |
| **Checksum**             | 16 bits      | 16-bit ones' complement sum for header error checking.                  |
| **Source / Destination** | 32 bits each | Origin and target IPv4 addresses.                                       |
| **Options**              | Variable     | Extended diagnostic and routing flags (up to 40 bytes).                 |
| **Padding**              | Variable     | Fills remaining bytes with `0`s to align header to a 4-byte boundary.   |

## IP Record-Route Field
The `Record-Route field` in the IP header also records the route to a destination device. When the destination device sends back the `ICMP Echo Reply` packet, the IP addresses of all devices that pass through the packet are listed in the `Record-Route field` of the IP header.
But the reality, **is rarely used today because firewalls and routers usually drop packets with IP options for security and performance reasons**, instead, today is use ==`traceroute`==

## IP Payload
The payload (also referred to as `IP Data`) is the actual payload of the packet. It contains the data from various protocols, such as TCP or UDP, that are being transmitted, just like the contents of the letter in the envelope.
If you think of an IP packet as an envelope:
- **The IP Header** is the address label, postage stamps, and delivery instructions written on the outside of the envelope.
- **The IP Payload** is the letter hidden inside the envelope.

![[Pasted image 20260803083513.png]]

## TCP
TCP packets, also known as `segments`, are divided into several sections called headers and payloads. The TCP segments are wrapped in the sent IP packet.

The header contains several fields that contain important information. The source port indicates the computer from which the packet was sent. The destination port indicates to which computer the packet is sent. The sequence number indicates the order in which the data was sent. The confirmation number is used to confirm that all data was received successfully. The control flags indicate whether the packet marks the end of a message, whether it is an acknowledgment that data has been received, or whether it contains a request to repeat data. The window size indicates how much data the receiver can receive. The checksum is used to detect errors in the header and payload. The Urgent Pointer alerts the receiver that important data is in the payload.

The payload is the actual payload of the packet and contains the data that is being transmitted, just like the content of a conversation between two people.

## UDP
UDP transfers `datagrams` (small data packets) between two hosts. It is a connectionless protocol, meaning it does `not` need to establish a connection between the sender and the receiver before sending data. Instead, the data is sent directly to the target host without any prior connection.

When `traceroute` is used with UDP, we will receive a `Destination Unreachable` and `Port Unreachable` message when the UDP datagram packet reaches the target device. Generally, UDP packets are sent using `traceroute` on Unix hosts.

# Blind Spoofing
**Blind Spoofing** is sending packets with a fake (**spoofed**) source IP when you are outside the network and **cannot see any of the return traffic**. All replies go to the real owner of that fake IP, leaving you "flying blind."

## How it works
Usually, when two computers want to talk over the internet using **TCP** (like logging into a website or server), they do a 3-step handshake:

Plaintext

```
1. Attacker  ──> "Hi, I'm the Boss, let's talk."        ──> Server
2. Server    ──> "OK Boss, use Secret Code #84920."     ──> Boss's Real Computer (Attacker can't see this!)
3. Attacker  ──> "Here is my reply with Code #?????"    ──> Server
```

Because you are blind and didn't see Step 2, you **don't know Secret Code #84920**.
	
If you guess the code wrong in Step 3, the server ignores you and closes the connection. **Blind spoofing** is the act of trying to complete this connection when you can't see the code and have to guess it.

## Why Do It If You Can't See Responses?
You don't need to read a server's reply to make it do something:
1. **One-Way Command Execution:** Force a server to execute an exploit or command (e.g., creating a backdoor account, adding an SSH key, or deleting a file) without needing to see the command output.
2. **Denial of Service (DoS):** Exhaust a server's memory by flooding it with connections from millions of fake IPs (**SYN Flood**).
3. **Traffic Amplification:** Tricking big servers (like DNS/NTP) into bouncing massive response traffic at a victim's IP (**Reflection Attack**).
4. **Session Killing:** Injecting a spoofed **RST (Reset)** packet to drop someone else's active connection.