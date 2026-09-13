Network layer is responsible for the following functions:

- `Logical Addressing`
- `Routing`

## Protocols (not all)
### `IPv4 / IPv6` (Internet Protocol)

- **What it is:** The principal communication protocol for `Logical Addressing` on the internet. Think of it as the postal service for data packets. `IPv4` is the older version, and `IPv6` is the newer one designed to replace it.
- **How it works:** Every device connected to a network gets a unique IP address (e.g., `192.168.1.10` for IPv4). When a packet is sent, its header contains the source and destination IP addresses. Routers use the destination IP address to decide where to forward the packet.
- **Key Difference:** `IPv4` uses a 32-bit address, which allows for ~4.3 billion unique addresses. We have run out of these. `IPv6` uses a 128-bit address, providing a virtually unlimited number of addresses to support the growing number of internet-connected devices.

### `IPsec` (Internet Protocol Security)

- **What it is:** A suite of protocols that secures IP communications by authenticating and encrypting each IP packet in a data stream.
- **How it works:** It operates at the network layer, creating a secure "tunnel" between two points. It can provide confidentiality (encrypting the data), integrity (ensuring the data wasn't changed in transit), and authentication (verifying the sender's identity).
- **Cybersecurity Context:** This is the technology that powers most modern **VPNs** (Virtual Private Networks). When you connect to a VPN, you are often using `IPsec` to create a secure, encrypted connection over the public internet.

### `ICMP` (Internet Control Message Protocol)

- **What it is:** A supporting protocol used by network devices, like routers, to send error messages and operational information. It is not used to transport user data.
- **How it works:** If a router cannot deliver a packet, it uses `ICMP` to send a message back to the source. For example, "Destination Unreachable" or "Time Exceeded."
- **Practical Example:** The `ping` command is a classic use of `ICMP`. It sends an "Echo Request" message, and the destination host replies with an "Echo Reply," allowing you to test connectivity and latency. The `traceroute` command also relies on `ICMP`.

### `IGMP` (Internet Group Management Protocol)

- **What it is:** A protocol used to manage membership in IP multicast groups. Multicast is a method of sending a single packet to multiple specific recipients simultaneously.
- **How it works:** A host sends an `IGMP` message to its local router to say, "I want to join this multicast group" or "I want to leave this group." The router then knows to forward packets for that group to the host.
- **Practical Example:** This is essential for live video streaming or online gaming, where the same data needs to be sent to many users at once without sending thousands of individual copies.

### `RIP` (Routing Information Protocol)

- **What it is:** One of the oldest and simplest routing protocols. It's a "distance-vector" protocol, meaning it makes routing decisions based on a single metric: **hop count**.
- **How it works:** Each router tells its neighbors about the networks it can reach and how many "hops" (routers) away they are. Routers build their routing tables by choosing the path with the lowest hop count.
- **Limitation:** It's considered outdated because it's slow to adapt to network changes and ignores important factors like link speed. A 2-hop path over slow links is considered better than a 3-hop path over fast links.

### `OSPF` (Open Shortest Path First)

- **What it is:** A modern and much more sophisticated routing protocol. It's a "link-state" protocol.
- **How it works:** Instead of just sharing hop counts, each router builds a complete map of the entire network's topology. It then independently calculates the best (shortest) path to every destination based on a "cost," which can factor in link speed and other variables.
- **Advantage over RIP:** `OSPF` is much faster, more scalable, and more efficient. It is the standard routing protocol used within most large enterprise networks.