Is the arrangement of devices in a network. It can be either **physical** (the actual layout of cables and hardware) or **logical** (the path data travels).

- **Star Topology:** All hosts connect to a central network device like a switch or router. This is very common in modern Local Area Networks (LANs).![[Pasted image 20260724163548.png]]

- **Bus Topology:** All hosts are connected to a single shared transmission medium (like a coaxial cable). Only one host can send data at a time.![[Pasted image 20260724163522.png|669]]
- **Ring Topology:** Each host is connected to two others, forming a circular data path. Data is often passed along the ring using a "token."![[Pasted image 20260724163556.png]]

- **Mesh Topology:** Nodes are interconnected, providing multiple paths for data. A **fully meshed** network connects every host to every other host, offering high reliability, while a **partially meshed** network has fewer connections.![[Pasted image 20260724163602.png]]

- **Hybrid Topology:** A network created by combining two or more different basic topologies, such as a star and a bus network.![[Pasted image 20260724163615.png]]


**Practical Application:**  
Understanding different topologies is fundamental for designing, building, and troubleshooting networks. For example, a star topology is easy to manage but depends on its central device, while a mesh topology is highly resilient but more complex and expensive to implement.

**Watch Out For:**  
Don't confuse the physical layout of devices with the logical topology. A network might be cabled in a star formation but logically operate as a ring, where data is passed from port to port in a circular fashion.