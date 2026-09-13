A network enables 2 computers to communicate with each other. There is a wide array of ==**topologies**== (mesh/tree/star), ==**mediums**== (ethernet/fiber/coax/wireless), and ==**protocols**== (TCP/UDP/IPX) that can be used to facilitate the network. It is important as security professionals to understand networking because when the network fails, the error may be silent, causing us to miss something.

By creating lots of smaller networks, and having them communicate, we can add defense layers

> [!Example No. 1]
> Building smaller networks and putting Access Control Lists around them is like putting a fence around the property's border that creates specific entry and exit points. Yes, an attacker could jump over the fence, but this looks suspicious and is not common, allowing it to be quickly detected as malicious activity. Why is the printer network talking to the servers over HTTP?

> [!Example No. 2]
> Taking the time to map out and document each network's purpose is like placing lights around the property, making sure all activity can be seen. Why is the printer network talking to the internet at all?

> [!Example No. 3]
> Having bushes around windows is a deterrent to people attempting to open the window. Just like Intrusion Detection Systems like Suricata or Snort are a deterrent to running network scans. Why did a port scan originate from the printer network?

## A Pentester Oversight

- Server Gateway: 10.20.0.1/25
- Domain Controller: 10.20.0.10/25
- Client Gateway: 10.20.0.129/25
- Client Workstation: 10.20.0.200/25
- Pentester IP: 10.20.0.252/24 (Set Gateway to 10.20.0.1)

> [!summary]
> This story illustrates the critical importance of understanding a network's true structure instead of making assumptions.
> 
> - **The Network Setup:** The network was not a single flat `/24` network. It was segmented using a `/25` subnet mask, creating two separate logical networks:
>     
>     - **Server Network (`/25`):** Contained high-value targets like the Domain Controller (`10.20.0.10`).
>     - **Client Network (`/25`):** Contained workstations and was where the pentester was placed (`10.20.0.252`).
> - **The Pentester's Mistake:** The pentester assumed a standard `/24` network configuration (`255.255.255.0`). This told their computer that any IP address from `10.20.0.1` to `10.20.0.254` was on the same local network.
>     
> - **The Consequence:** When the pentester tried to connect to the Domain Controller (`10.20.0.10`), their computer thought it was a "local neighbor" and tried to communicate directly. It never sent the traffic to the gateway to be routed to the other network. This communication failed, leading the pentester to incorrectly report that the Domain Controller was offline.
>     
> - **The Lesson:** Never assume a network's configuration. A failure in basic network reconnaissance can lead you to miss entire segments of a network, including your primary targets.

### The Two Paths for Communication:

1. **If the destination is "Local":** The computer believes the target is in the same "room." It will shout (using an ARP broadcast) to find the target's physical address directly. It **completely ignores the gateway**.
2. **If the destination is "Remote":** The computer knows the target is in a different "room." It will package the data and hand it to its configured **Gateway** (the "doorway" of the room), trusting the gateway to deliver it.

> [!The Pentester Mistake]
>The pentester's computer was configured with a `/24` mask (`255.255.255.0`). This told it: "Any IP address starting with `10.20.0` is **local**."
> 
> This was the wrong rule, because the network was actually split into two separate `/25` networks.
> 
> **Let's Trace the Outcomes:**
> 
> **Scenario 1: Pentester on the Client Network (`10.20.0.252`)**
> 
> - **Target: Client Workstation (`10.20.0.200`)**
>     
>     - Computer's Decision: `10.20.0.200` looks **local** (because of the `/24` mask).
>     - Action: Use ARP to find it directly.
>     - Result: **Success.** The workstation is _actually_ local, so it hears the ARP "shout" and responds.
> - **Target: Domain Controller (`10.20.0.10`)**
>     
>     - Computer's Decision: `10.20.0.10` also looks **local** (because of the `/24` mask).
>     - Action: Use ARP to find it directly.
>     - Result: **Failure.** The Domain Controller is in a different "room" (the Server Network). It never hears the ARP "shout," so it can't respond. The pentester thinks the server is offline.
> 
> **The key takeaway is that the computer's action depends entirely on its configured subnet mask.** A wrong mask leads to wrong actions, which can make perfectly reachable machines seem invisible. This is why proper network reconnaissance is not just a suggestion—it's essential.

## Basic Info
![[Pasted image 20260723110824.png|697]]

- **Networking Analogy:** This section explains networking using an analogy of sending mail. Your computer sends a "package" (data) from your `Home Network` to a `Company Network`.
- **Router & ISP:** Your `router` acts as your local post office, sending the package to the `Internet Service Provider` (ISP), which is the main post office.
- **DNS & IP Address:** The `Domain Name Service` (DNS) is like a phonebook. The ISP uses DNS to look up the human-readable name you provide (like `www.hackthebox.eu`) and find its corresponding machine-readable `IP address` (the exact location).
- **FQDN vs. URL:**
    - An **FQDN** (Fully Qualified Domain Name) is like a building's address (e.g., `www.hackthebox.eu`).
    - A **URL** (Uniform Resource Locator) is more specific, including the "floor" and "office" (e.g., `https://www.hackthebox.eu/example?floor=2`), telling the server exactly what resource you need.

**Practical Application:**  
When you type a website address into your browser, your request is sent through your router to your ISP. The ISP uses DNS to translate the website's name into an IP address. Your request is then sent to that IP address, and the web server sends the website's data back to your IP address.

### Extra points:
- **Network Segmentation:** The core principle is to avoid a single "flat" network. Instead, a secure network should be broken down into multiple smaller, isolated networks based on the function and trust level of the devices within them.
- **DMZ (Demilitarized Zone):** Internet-facing services, like a web server, should be placed in a DMZ. This isolates them from the internal network, so if they are compromised, the attacker is not immediately inside your trusted zone.
- **Device-Specific Networks:** Different types of devices should be on their own separate networks:
    - **Workstations:** To prevent a compromised machine from easily attacking other workstations or critical servers.
    - **Switches/Routers:** On an "Administration Network" to protect them from snooping and malicious control attempts from the user network.
    - **IP Phones:** To prevent eavesdropping and to prioritize their traffic to ensure call quality (low latency).
    - **Printers:** Because they are notoriously difficult to secure and can be abused to steal credentials or gain persistence.

**Practical Application:**  
Segmentation allows administrators to create strict firewall rules (Access Control Lists) between networks. This lets you enforce policies like "printers cannot talk to the internet" or "workstations cannot talk to the switch management interface." This strategy slows attackers down, makes their movement through the network much more difficult, and makes their malicious activity easier to detect as it violates established rules