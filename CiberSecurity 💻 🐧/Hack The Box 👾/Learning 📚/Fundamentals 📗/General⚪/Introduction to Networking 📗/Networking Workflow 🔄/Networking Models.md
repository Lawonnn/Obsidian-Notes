There are 2 networking models that describe the communication and transfer data from one host to another, called `OSI model` and `TCP/IP model`
![[CiberSecurity 💻 🐧/Hack The Box 👾/Learning 📚/Fundamentals 📗/General⚪/Introduction to Networking 📗/Network Structure ⛓️/Images  🖼️/Pasted image 20260724205442.png|697]]

## The OSI model
The `OSI` model, often referred to as `ISO/OSI` layer model, is a reference model that can be used to describe and define the communication between systems. The reference model has `seven` individual layers, each with clearly separated tasks.

The term `OSI` stands for `Open Systems Interconnection` model, published by the `International Telecommunication Union` (`ITU`) and the `International Organization for Standardization` (`ISO`). Therefore, the `OSI` model is often referred to as the `ISO/OSI` layer model.

## The TCP/IP model
`TCP/IP` (`Transmission Control Protocol`/`Internet Protocol`) is a generic term for many network protocols. The protocols are responsible for the switching and transport of data packets on the Internet. The Internet is entirely based on the `TCP/IP` protocol family. However, `TCP/IP` does not only refer to these two protocols but is usually used as a generic term for an entire protocol family.

## Packet Transfers
![[CiberSecurity 💻 🐧/Hack The Box 👾/Learning 📚/Fundamentals 📗/General⚪/Introduction to Networking 📗/Network Structure ⛓️/Images  🖼️/Pasted image 20260724212016.png]]
- **Encapsulation:** This is the core process of data transfer in a layered model. As data moves down from the Application Layer towards the Physical Layer, each layer adds its own `header` (control information) to the data it receives from the layer above.
- **Protocol Data Unit (PDU):** This is the name for the "chunk of data" at any given layer. The combination of the header and the data becomes the PDU for the next layer down. For example, a **Segment** (Transport Layer PDU) is encapsulated within an IP header to become a **Packet** (Internet Layer PDU).
- **Decapsulation:** The receiving device performs the reverse process. It unwraps the headers at each layer, moving the data up until it reaches the application, which can then use it.

**Practical Application:**  
As a penetration tester, understanding this process is essential for network traffic analysis. When you intercept network communications, you are capturing these encapsulated PDUs (like packets and frames). By examining the headers at each layer, you can analyze the communication in detail, understand how connections are established, and identify potential weaknesses.