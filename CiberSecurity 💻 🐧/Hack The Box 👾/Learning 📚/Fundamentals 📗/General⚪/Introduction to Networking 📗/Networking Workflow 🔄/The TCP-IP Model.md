The term `TCP/IP` stands for 2 protocols `Transmission Control Protocol (TCP)` and `Internet Protocol (IP)`.
`IP` is located withing the `Network Layer (Layer 3)`, and `TCP` is located within the  `Transport Layer (Layer 4)` of the `OSI model`
![[CiberSecurity 💻 🐧/Hack The Box 👾/Learning 📚/Fundamentals 📗/General⚪/Introduction to Networking 📗/Network Structure ⛓️/Images  🖼️/Pasted image 20260724205442.png]]
## Layer 4: Application
The Application Layer provides a way for applications to access network services. It defines the rules and protocols that software must use to send and receive data over the network.
 

> [!example]
> Think of it as the bridge between the software you use and the underlying network stack. Without this layer, your web browser wouldn't know how to ask a server for a webpage, and an email client wouldn't know how to send an email. It makes network communication meaningful for specific tasks.

### Protocols
The protocols you use every day operate at this layer. Some common examples include:

- **HTTP/HTTPS:** Used for browsing websites.
- **DNS (Domain Name System):** Used to translate human-readable names (like `academy.hackthebox.com`) into computer-readable IP addresses.
- **FTP (File Transfer Protocol):** Used for transferring files between computers.
- **SMTP (Simple Mail Transfer Protocol):** Used for sending emails.


The Application Layer creates the data for a request (like an HTTP GET request for a webpage) and then passes it down to the **Transport Layer**. The Transport Layer then takes that data and prepares it for transmission across the network.


## Layer 3: Transport
See [[The OSI model]] "Layer 4: Transport" 

## Layer 2: Internet
See [[The OSI model]] "Layer 3: Network"

## Layer 1:  Link
The **Link Layer** is responsible for placing TCP/IP packets onto the physical network medium (like an Ethernet cable or Wi-Fi) and receiving packets from it. It handles the communication between devices on the _same local network_.

### Key Concepts
- **Physical Addressing (MAC Address):**
    - Every network-capable device has a unique physical address called a **MAC (Media Access Control) address**. This address is burned into the network interface card (NIC) by the manufacturer and doesn't change.
    - **Analogy:** If an IP address is like your home's mailing address (which can change if you move), a MAC address is like your government ID number—unique and permanent to you (the device).
    - On a local network, devices use MAC addresses to send data directly to each other.
    
- **Frames:**
    - The Link Layer takes the **packet** from the Internet Layer and wraps it in another layer of information, creating a **frame**.
    - This frame contains the source and destination **MAC addresses** needed for local delivery. For example, if you're sending data to a website, the destination MAC address in the frame will be that of your local router.
    
- **Hardware Independence:**
    - A key design feature of TCP/IP, mentioned in the module, is that it's independent of the physical medium. The Link Layer is what makes this possible. The layers above it don't need to know or care if you're using **Ethernet**, **Wi-Fi**, **Fiber Optics**, or another technology. The Link Layer handles the specific rules for whatever hardware you're using.