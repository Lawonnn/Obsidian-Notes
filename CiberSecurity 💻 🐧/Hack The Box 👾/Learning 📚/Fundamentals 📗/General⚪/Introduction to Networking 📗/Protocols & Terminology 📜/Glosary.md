## Cellular Generations (4G, 5G, ...)
### Overview

The **"G"** stands for **Generation**. Each new generation increases speed, reduces latency (lag), handles more connected devices, and upgrades security protocols.

### Key Terms

- **4G (4th Generation):**
    
    - Transitioned mobile networks to pure **IP (Internet Protocol)** data.
        
    - Enabled HD streaming, real-time GPS, and fast web browsing.
        
- **LTE (Long Term Evolution):**
    
    - A high-speed transition standard between 3G and true 4G.
        
    - Now standardly grouped with 4G ("4G LTE").
        
- **5G (5th Generation):**
    
    - Offers ultra-fast speeds and near-zero latency (~1ms).
        
    - Designed for massive device density (IoT, smart cities, autonomous systems).
        

### Quick Comparison

| **Feature**       | **4G LTE**                     | **5G**                                 |
| ----------------- | ------------------------------ | -------------------------------------- |
| **Speed**         | 100 Mbps – 1 Gbps              | Up to 10–20 Gbps                       |
| **Latency (Lag)** | ~30 – 50 ms                    | ~1 ms                                  |
| **Focus**         | High-speed data & mobile media | Real-time communications & massive IoT |

### Cybersecurity & OffSec Notes 🛡️

- **2G / 3G:** Lacks mutual authentication; highly vulnerable to rogue cell towers (_IMSI catchers_).
    
- **4G LTE:** Adds mutual authentication (tower and phone verify each other), but initial handshake identity data is sent unencrypted.
    
- **5G Security:** Encrypts user identity over the air (preventing easy IMSI catching), but relies heavily on **software-defined networks (SDN)** and **APIs**, shifting attack vectors toward cloud and web app vulnerabilities.

## Service Set Identifier (SSI)
Basically, the network name

## Cyclic Redundancy Check (CRC)
Is an error-detection mechanism used to protect against data corruption in wireless communications. A CRC value is calculated for each packet transmitted over the wireless network based on the packet's data. It is used to verify the integrity of the data. When the destination device receives the packet, the CRC value is recalculated and compared to the original value. If the values match, the data has been transmitted successfully without any errors. However, if the values do not match, the data has been corrupted and needs to be retransmitted.