# Comprehensive Linux Socket & Process Enumeration Cheat Sheet

## 1. Network Sockets vs. Unix Domain Sockets (`AF_INET` vs `AF_UNIX`)

Understanding socket address families is crucial for identifying how processes communicate—either over local loopback/network interfaces or directly through system memory.

|**Metric**|**Network Sockets (AF_INET / AF_INET6)**|**Unix Domain Sockets (AF_UNIX)**|
|---|---|---|
|**Addressing Mechanism**|**IP Address + Port Number** (e.g., `127.0.0.1:8080`)|**Filesystem Path** (e.g., `/run/dbus/system_bus_socket`) or **Abstract Name** (e.g., `@/tmp/dbus-X`)|
|**Kernel Overhead**|Traverses loopback (`lo`), builds IP/TCP/UDP headers, calculates checksums|Bypasses network stack; copies data directly between process buffers in memory|
|**Scope**|Local host and external network machines|strictly local Inter-Process Communication (IPC) on the same machine|
|**Access Control**|Network firewall rules (`iptables`, `nftables`)|Standard Linux filesystem permissions (`chmod`, `chown`)|

## 2. Socket Types & Kernel Mechanics

Sockets define how applications request communication handles from the Linux kernel.

### **Stream Sockets (`SOCK_STREAM`)**

- **Network Protocol:** **TCP**
    
- **Characteristics:** Connection-oriented, reliable, sequential, error-checked data streams.
    
- **Kernel Function:** Manages three-way handshakes, sequence numbers, packet acknowledgments, and retransmissions.
    

### **Datagram Sockets (`SOCK_DGRAM`)**

- **Network Protocol:** **UDP**
    
- **Characteristics:** Connectionless, fast, unreliable delivery without ordering or state tracking.
    
- **Kernel Function:** Wraps payload in minimal headers and fires packets directly down to the interface.
    

### **RAW Sockets (`SOCK_RAW`)**

- **Network Protocol:** **ICMP**, **Custom TCP/IP**
    
- **Characteristics:** Bypasses Layer 4 (Transport Layer); permits manual construction of custom Layer 3/2 headers.
    
- **Kernel Function:** Serves as a pass-through (requires `CAP_NET_RAW` / `root`). Application code handles header assembly, checksum calculation, and state tracking.
# PCI vs. PCIe vs. PCI Devices
- **PCI / PCIe (The Highway):** The physical bus standard, copper motherboard traces, and communication protocol regulating data flow to the CPU.
    
- **PCIe Slot (The Entrance):** The physical motherboard expansion slot where devices plug in.
    
- **PCI Device (The Vehicle):** The actual hardware cards plugged into those slots (e.g., Nvidia GPU, Intel Wi-Fi card, NVMe SSD)
    
- **Direct Memory Access (DMA):** A feature of the PCI bus allowing devices to read/write directly to system RAM without asking the CPU, making unencrypted physical PCIe slots a target for hardware-level attacks (e.g., PCILeech).