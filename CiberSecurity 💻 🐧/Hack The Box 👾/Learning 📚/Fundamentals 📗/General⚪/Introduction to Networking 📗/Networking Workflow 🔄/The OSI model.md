The OSI model uses 7 different layers, which are hierarchically based on each other. These layers represent phases establishment of each connection through which the sent packets pass.

The layer 2-4 are transport oriented and the 5layers 5-7 are application oriented
## Layer 7: Application 
This layer control the input and output of data and provides the application function. 
> [!important]
> The Application Layer doesn't include the applications themselves (like your web browser or email client), but it provides the **protocols** that those applications use to communicate over the network.

> [!example]
> - **Initiating Communication:** When you want to browse a website, your web browser uses the **HTTP** (Hypertext Transfer Protocol) or **HTTPS** (secure HTTP) protocol at the Application Layer to request data from the web server.
> - **Resource Sharing:** Protocols like **FTP** (File Transfer Protocol) and **SMB** (Server Message Block) operate here to allow for file sharing and access across a network.
> - **Email Services:** When you send an email, your email client uses **SMTP** (Simple Mail Transfer Protocol) to send it. When you receive an email, it might use **POP3** or **IMAP**. These are all Application Layer protocols.
> - **Name Resolution:** The **DNS** (Domain Name System) protocol, which translates human-readable domain names (like `hackthebox.com`) into machine-readable IP addresses, also functions at this layer.

> [!summary]
> In short, if you're using a program that needs to communicate over a network, it's using a protocol at the Application Layer to get the job done. It's the layer that makes the network useful for a specific task, like browsing, emailing, or file sharing.

## Layer 6: Presentation
Is main job is to take data from application layer and prepare it for transmission, ensuring the receiving system can understand it, regardless of its own internal data format
1. **Translation:** Computers can represent data differently (e.g., using different character encoding standards like ASCII or UTF-8). The Presentation Layer translates the data into a standard format that both the sender and receiver can understand.

2. **Encryption/Decryption:** This is a crucial function for security. Layer 6 is responsible for scrambling the data on the sender's side (encryption) and unscrambling it on the receiver's side (decryption). This is what keeps your data confidential during transmission, as seen in protocols like TLS/SSL that secure your web browsing.

3. **Compression/Decompression:** To make data transfer faster and more efficient, the Presentation Layer can compress the data into a smaller size before sending it. The receiver's Presentation Layer then decompresses it.

> [!summary]
> In short, if you're using a program that needs to communicate over a network, it's using a protocol at the Application Layer to get the job done.


## Layer 5: Session
Its primary job is to establish, manage and terminate connections (or sessions) between application on different computers
1. **Session Establishment:** This is like dialing a number and waiting for the other person to answer. The Session Layer initiates the connection and makes sure the other party is ready to communicate.

2. **Session Maintenance:** This is the actual conversation. The Session Layer keeps the connection alive and manages the dialogue. It determines whose turn it is to "talk" (known as dialog control) and ensures both systems stay in sync.

3. **Session Termination:** This is like saying "goodbye" and hanging up. The Session Layer ensures the connection is closed gracefully, so no resources are left hanging.

> [!important]
> A crucial function here, which relates directly to preventing connection breakdowns, is **synchronization**. Imagine you're downloading a very large file. The Session Layer can place checkpoints in the data stream. If the connection drops halfway through, the session can be resumed from the last checkpoint instead of starting the entire download from scratch.

> [!summary]
> So, in summary, the Session Layer doesn't care about the data itself, but it's obsessed with managing the conversation: starting it, keeping it orderly, and ending it cleanly.

## Layer 4: Transport
Transport layer acts as a bridge between the application-oriented layers (5-7) and the network-focused layers (1-3). Its main responsibility is to provide **end-to-end communication** between processes on the source and destination hosts.

> [!example]
> Think of the Transport Layer as the post office sorting facility for your data. It takes the message from the upper layers and gets it ready for delivery.

### How it work
1. **Segmentation:** It breaks large chunks of data from the Session Layer into smaller, more manageable pieces called **segments** or **datagrams**. Each segment gets a sequence number so they can be reassembled correctly at the destination.
    
2. **Connection-Oriented vs. Connectionless Service:** This is the most critical function of Layer 4. It offers two main "delivery services":
    
    - **TCP (Transmission Control Protocol):** This is the **reliable**, connection-oriented service. Think of it like sending a package with tracking and delivery confirmation. TCP establishes a formal connection (using the famous "three-way handshake"), numbers every segment, and requires the receiver to send acknowledgments (ACKs). If a segment is lost, TCP re-sends it. This is used for web browsing (HTTP), file transfers (FTP), and email (SMTP), where data integrity is essential.
    - **UDP (User Datagram Protocol):** This is the **unreliable**, connectionless "best-effort" service. Think of it like dropping a postcard in a mailbox. You send it and hope it arrives, but there's no tracking or confirmation. It's much faster than TCP because it doesn't have the overhead of handshakes and acknowledgments. This is used for services like video streaming, online gaming, and DNS, where speed is more important than perfect reliability.
	
3. **Flow Control:** It manages the speed of data transmission. If the sender is sending data faster than the receiver can process it, Layer 4 can tell the sender to slow down to prevent data loss.

> [!summary]
> The Transport Layer decides **how** data is sent (reliably with TCP or quickly with UDP) and ensures it gets from the correct application on the source computer to the correct application on the destination computer.

## Layer 3: Network
This layer is the internet's traffic director. Its primary mission is to handle the addressing and routing of data, finding the best path for packets to travel from the source computer to the destination computer, even if they are on different networks thousands of miles apart.

1. **Logical Addressing:** This layer uses logical addresses, most famously the **IP address** (like `192.168.1.10` or a public IP address). Unlike the physical address of a device, the IP address tells you which network the device is on. This is like having a full street address (including city and zip code), which tells you the general location, not just the specific house number. The data unit at this layer is called a **packet**.
    
2. **Routing:** This is the core function of Layer 3. Devices called **routers** operate here. A router receives a packet, inspects its destination IP address, and consults its internal "map" (a routing table) to decide the best path to send the packet on its next "hop" toward the final destination. This process happens at every router along the path.
    
3. **Path Determination:** The Network Layer is responsible for figuring out the best end-to-end path through a complex web of interconnected networks (like the internet).

> [!summary]
> In short, while the Transport Layer (Layer 4) ensures data gets from one _application_ to another, the Network Layer (Layer 3) is responsible for the journey of the data **packets** across all the intermediate networks between the source and destination _machines_.

## Layer 2: Data Link
The **Data Link Layer (Layer 2)** is responsible for node-to-node data transfer—moving data between two directly connected devices on the same local network. It ensures that the transmission is reliable over the physical link.
1. **Framing:** The Data Link Layer takes the **packets** from the Network Layer and encapsulates them into units called **frames**. A frame is like an envelope for the packet.
    
2. **Physical Addressing:** This is a crucial function. Each frame is given a header containing the physical address of the source and destination devices. This physical address is known as the **MAC (Media Access Control) address**. A MAC address is a unique identifier burned into every network interface card (NIC). Devices like network **switches** operate at Layer 2 and use MAC addresses to forward frames only to the intended recipient on a local network.
    
3. **Error Control:** The Data Link Layer adds a trailer to the frame, which typically contains a value for a **Cyclic Redundancy Check (CRC)**. The receiving device calculates its own CRC on the frame it received. If the values don't match, it knows the frame was corrupted during transmission and discards it.
    
4. **Flow Control:** Similar to the Transport Layer, it can manage the transmission rate to ensure a fast sender doesn't overwhelm a slow receiver on the same local link.

> [!info]
> 
> ## Difference between Network layer and Data-Link layer
> The Network Layer's philosophy is "move packets as fast as possible and drop any that are broken. Let the endpoints (using Layer 4) worry about fixing any problems."

> [!summary]
> In summary, the Data Link Layer handles the logistics of getting data from one device to the next immediate device on the same network segment, using physical MAC addresses to find the right machine and error checking to ensure the data arrives intact.

## Layer 1: Physical
Think of it as the **physical rulebook for communication**. It doesn't just define the cable, it defines _how_ to use that cable.

It answers these three basic questions:

1. **What are we transmitting on?**
    
    - Is it a copper Ethernet cable? A fiber optic cable? Or are we using radio waves for Wi-Fi? This is the **medium**.
2. **How do we represent a '1' or a '0'?**
    
    - The layer decides if a '1' is a positive voltage and a '0' is a negative voltage. Or if a '1' is a pulse of light and a '0' is no light. This is called **signal encoding**.
