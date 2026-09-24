
## Cisco
### What is Cisco IOS?
- **Definition:** Proprietary Network Operating System for Cisco devices like **routers** and **switches**.
- **Primary Interface:** Managed mainly via **CLI** (Command-Line Interface).
- **Remote Access:** Accessed remotely via **SSH** (secure) or **Telnet** (insecure).
- **Fingerprint:** Identified by the signature response banner `"User Access Verification"`.
### Capabilities & Protocols

|**Protocol Type**|**Description**|
|---|---|
|`Routing protocols`|Such as [OSPF](https://en.wikipedia.org/wiki/Open_Shortest_Path_First) and [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) are used to route data packets on a network.|
|`Switching protocols`|Such as [VLAN Trunking Protocol](https://en.wikipedia.org/wiki/VLAN_Trunking_Protocol) (`VTP`) and [Spanning Tree Protocol](https://en.wikipedia.org/wiki/Spanning_Tree_Protocol) (`STP`) is used to configure and manage switches on a network.|
|`Network services`|Such as [Dynamic Host Configuration Protocol](https://en.wikipedia.org/wiki/Dynamic_Host_Configuration_Protocol) (`DHCP`) are used to automatically provide clients on the network with IP addresses and other network configurations.|
|`Security features`|Such as [Access Control Lists](https://en.wikipedia.org/wiki/Access-control_list) (`ACLs`), which are used to control access to network resources and prevent security threats.|
### Password Types
- **User Password:** Restricts basic entry (User EXEC mode).
- **Enable Password:** Protects privileged mode (`enable` mode) using **plain-text** (insecure).
- **Secret:** Secures specific user accounts or services using **hashes**.
- **Enable Secret:** Protects privileged mode (`enable` mode) using **strong encryption/hashes** (overrides Enable Password).

## VLANs
#### 1. Core Concept & Problem Solved
- **What it is:** A way to logically split **one physical switch into multiple virtual "mini-switches"**.
- **Problem it solves:** Prevents insider attacks, unauthorized packet sniffing, and **broadcast storms** (where rogue or malfunctioning traffic floods the entire physical network).
- **Key Mechanism:** A broadcast frame sent inside one VLAN will **never** reach a device in another VLAN.
#### 2. VLANs vs. Subnets (Rule of Thumb)
- Each VLAN acts as an isolated **Layer 2 broadcast domain**.
- Because they are separate domains, **each VLAN requires its own Layer 3 Subnet** (1-to-1 mapping).
##### Example Network Design (Startup XQ)

| **Department / Function** | **VLAN ID** | **Subnet**       |
| ------------------------- | ----------- | ---------------- |
| **Servers**               | VLAN 10     | `192.168.1.0/24` |
| **C-Level**               | VLAN 20     | `192.168.2.0/24` |
| **Finance**               | VLAN 30     | `192.168.3.0/24` |
| **HR**                    | VLAN 40     | `192.168.4.0/24` |
| **Marketing**             | VLAN 50     | `192.168.5.0/24` |
| **Support**               | VLAN 60     | `192.168.6.0/24` |
#### 3. Key Benefits

1. **Security:** Restricts unauthorized users from sniffing traffic across different departments
2. **Performance:** Drops unnecessary broadcast traffic, freeing up bandwidth.
3. **Organization & Admin:** Allows logical grouping based on role/function regardless of physical desk locations.

#### 4. Cisco Technical Details (VLAN Ranges)
- **Valid VLAN ID Range:** `1 – 4094` (`0` and `4095` are reserved).
- **Default VLAN:** `VLAN 1` (Cannot be modified or deleted).
- **Normal Range (`1 – 1005`):**
    - Configurations are stored in the switch's `vlan.dat` file.
    - IDs `1002–1005` are reserved for legacy protocols (Token Ring/FDDI).
- **Extended Range (`1006 – 4094`):**
    - Customizations are **not** saved in `vlan.dat`.

### VLAN Memberships
####  Static VLANs (The "Hardcoded" Way)
- **How it works:** The network admin manually configures switch ports ahead of time. For example:
    - Port 1 = Accounting VLAN 
    - Port 2 = Guest VLAN
- **What the device knows:** **Nothing.** Your laptop has no idea it’s on a VLAN; it just sends regular network traffic, and the switch handles the isolation.
- **Security & Admin effort:** High security, high manual effort. If you unplug a device and plug it into Port 1, you are stuck in the Accounting VLAN no matter who you are.
#### Dynamic VLANs (The "Automatic" Way)
- **How it works:** Port assignments happen automatically when a device plugs in.
    1. You plug your device into a port.
    2. The switch looks at your device's **MAC address** (its unique hardware fingerprint).
    3. The switch asks a central server (like **VMPS**): _"Hey, which VLAN does MAC address `AA:BB:CC:11:22:33` belong to?"_
    4. The server replies with the VLAN ID, and the switch assigns the port on the fly.
- **Why admins use it:** Flexibility. Users can move around the office, plug into any port, and automatically land in their assigned network segment.

### VLAN Ports
- **Access port**: Belong to and can carry the traffic of only one VLAN (or in some cases two, with the second being for `voice traffic`)
- **Trunk port**: `trunk ports` can carry multiple `VLANs` at the same time;`trunk links` connect two `trunk ports` on two switches (or a switch and router) to allow information from multiple `VLANs` to be carried out across switches.

### VLAN Identification
Plain Ethernet frames (IEEE 802.3) carry source/destination MAC addresses and data, but **zero VLAN info**. When Switch A sends a frame across a trunk to Switch B, how does Switch B know if it belongs to Accounting (VLAN 10) or HR (VLAN 20)?
#### IEEE 802.1Q (Dot1q) — _The Universal Standard_
- **How it works:** It modifies the existing Ethernet frame by squeezing a small **4-byte tag** (which includes the VLAN ID) right into the middle of the header.
- **Why it's popular:** It is an open IEEE standard supported by almost every network vendor in the world. **This is what everyone uses today.**
#### ISL (Inter-Switch Link) — _The Legacy Cisco Way_
- **How it works:** Instead of modifying the frame, it wraps the entire original frame inside a brand-new 26-byte header and a 4-byte trailer (like putting an envelope inside a larger envelope).
- **Current status:** It was a proprietary Cisco standard developed before 802.1Q existed. It is now obsolete and no longer supported on modern switches, but you'll still see it on certifications and HTB theory modules.

### VLANs Attacks
#### VLAN Hopping
A Layer 2 attack that allows an attacker to bypass network segmentation, sending traffic from their current VLAN into a restricted target VLAN without passing through a router or firewall. Requires **direct physical or local Layer 2 access** (e.g., plugged into an RJ-45 wall jack or controlling an endpoint on the switch). **Layer 2 frames do not cross routers or the Internet.**
##### DTP Exploitation
- **Mechanism:** Exploits Dynamic Trunking Protocol (**DTP**), a Cisco-proprietary protocol that automatically negotiates trunking links between switches.
- **Prerequisite:** The target switch port is left in an active dynamic trunking mode (`dynamic auto` or `dynamic desirable`).
##### Attack Steps:
1. Attacker crafts fake DTP negotiation frames (using tools like **Yersinia**).
2. The switch misinterprets the attacker's endpoint as another switch and converts the port into a **Trunk Port**.
3. The attacker creates local VLAN sub-interfaces (`eth0.10`, `eth0.20`) to send and receive traffic on **any allowed VLAN**.
- **Traffic Flow:** **Bidirectional (Two-Way)** — Full access to send and receive packets.

#### Double-tagging VLAN Hopping
Double Tagging is a frame-injection technique that tricks a switch into stripping the outer VLAN header while transmitting across an 802.1Q trunk, revealing a hidden inner VLAN tag to the downstream switch. It exploits the fundamental IEEE 802.1Q standard requirement: **Native VLAN traffic MUST travel across trunk links UNTAGGED.**

For Double Tagging to succeed, **all three conditions** must be true simultaneously:
1. **Access Port Matching:** Attacker is connected to an Access Port whose assigned VLAN ID matches the Trunk link's **Native VLAN** ($\text{Port VLAN} == \text{Trunk Native VLAN}$).
2. **Trunk Link Present:** The target switch must be connected to the local switch via an 802.1Q trunk link.
3. **Victim Location:** The victim host resides on a different VLAN connected to that secondary switch across the trunk.

##### Step-by-Step
- **Frame Crafting:** Attacker sends an Ethernet frame with two 802.1Q headers:
    - **Outer Tag (`VLAN 1`):** Matches local access port and Trunk Native VLAN.
    - **Inner Tag (`VLAN 20`):** Target VLAN ID.
- **Switch 1 Ingress:** Port receives the frame. Switch 1 checks the outermost tag (`VLAN 1`). Since it matches the port's assigned access VLAN, the frame is accepted into the switch fabric.
- **Switch 1 Egress (Trunk):** Switch 1 forwards the frame across the trunk. Because `VLAN 1` is configured as the Native VLAN on this trunk, Switch 1 **strips off the Outer Tag (`VLAN 1`)**.
- **Switch 2 Ingress:** Switch 2 receives the incoming frame off the trunk cable carrying only `[ Inner Tag: 20 ]`.
- **Delivery:** Switch 2 reads `VLAN 20`, strips the tag, and forwards the raw frame to the target's switch port on VLAN 20.

## VXLAN
**VXLAN (Virtual Extensible LAN)** is the modern standard for network segmentation and virtualization, designed to solve the massive scale limitations of traditional VLANs.

Where traditional **VLANs** were built for campus networks, **VXLAN** was built for modern cloud providers, massive data centers, and multi-tenant environments.

### The Core Problem: Why VLANs Weren't Enough
Traditional 802.1Q VLANs have two huge flaws in cloud/data center architectures:
1. **The 4,096 Limit:** VLAN IDs use a **12-bit tag**, capping total unique networks at $2^{12} = 4096$. In a cloud infrastructure housing thousands of customers (tenants), 4,096 segments run out almost immediately.
2. **Layer 2 Boundaries:** Traditional VLANs cannot span across routers without hacky Layer 2 extensions or dangerous Spanning Tree Protocol (STP) topologies. If Virtual Machine A moves to a server on another rack across a routed Layer 3 boundary, keeping its IP address on a traditional VLAN becomes a nightmare.
### How VXLAN Solves It: Overlay vs. Underlay
VXLAN introduces an **Overlay / Underlay** model using **MAC-in-UDP Encapsulation**:
- **Underlay Network:** The physical routers and switches running standard Layer 3 IP routing (like OSPF or BGP).
- **Overlay Network:** The virtual Layer 2 networks created _on top_ of the physical network by wrapping original Ethernet frames inside IP/UDP packets.

## Cisco Discovery Protocol
Cisco Discovery Protocol (CDP) is a layer-2 network protocol from Cisco that is used by Cisco devices such as routers, switches, and bridges to gather information about other directly connected Cisco devices. This information can be used to discover and track the network's topology and help manage and troubleshoot the network. This protocol is usually enabled in Cisco devices, but it can be disabled if it is not needed or if it should be disabled for security reasons.
### CDP Network Traffic

```
22:14:11.563654 CDPv2, ttl: 180s, checksum: 0xebc1 (incorrect -> 0x8b71), length: 180
	Device-ID (0x01), length: 14 bytes: 'router.inlanefreight.loc'
	Addresses (0x02), length: 8 bytes:
	IPv4 (0x01), length: 4: 10.129.100.1
	Port-ID (0x03), length: 9 bytes: 'Ethernet0/0'
	Capability (0x04), length: 4: (0x00000010): Router
	Version String (0x05), length: 27 bytes: 'Cisco IOS Software, C880 Software'
	Platform (0x06), length: 26 bytes: 'Cisco 881 (MPC8300) processor'`
```

The shown message contains information about the device itself, such as the device name, IP address, port name, and functionality of the router, as well as information about the operating system and hardware platform of the device. Besides, we can see in the first line from the `CDPv2` that we are dealing with the `Cisco Discovery Protocol`.

## STP Protocol
The `STP` is a network protocol that ensures no loops in a network with multiple connections between switches. There are no loops, and it prevents data packets from circulating in a loop and congesting the network.
### STP Network Traffic

```
22:14:11.563654 STP 802.1w, Rapid STP, Flags [Learn, Forward], bridge-id 8001.00:11:22:33:44:55.8000, length 43
	root-id 8001.AA:AA:AA:AA:AA:AA, cost 0, port-id 8001, message-age 0.00s, max-age 20.00s, hello-time 2.00s, forward-delay 15.00s`
```

In this example, we see that an `STP` message was sent containing information about the root switch, the MAC address of the root switch, the ID of the port over which the message was sent, and other configuration parameters such as the maximum aging time, hello time, and forward delay.