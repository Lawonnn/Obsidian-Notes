## Pivoting
The technique of ==using a single compromised computer or device as a launchpad to attack other systems==, networks, or devices that were otherwise hidden or unreachable

## Active Directory (AD)
Is a Directory Service built by Microsoft for Windows domain networks. At its core, it's an extensible, ==hierarchical/central database==. Everything inside AD is treated as an Object (users, computers, groups, printers, shared folders)

> [!example]
> **Active Directory (AD) is the Database / Guest Registry:** It’s the digital book or list that holds all the information. It lists who is staying in room 204, which keys open the gym, who the manager is, and what the VIP pass lets you do. **AD is the data itself.**
> - **Object-Based Structure:** Everything inside AD is treated as an **Object** (users, computers, groups, printers, shared folders).

- **Data Hierarchy:**
    
    - **Forest:** The top-level security boundary that contains one or more domain trees.
        
    - **Tree:** A collection of one or more domains that share a contiguous namespace (e.g., `corp.example.com`).
        
    - **Domain:** A logical administrative boundary containing objects.
        
    - **Organizational Units (OUs):** Containers inside a domain used to organize objects and apply policies.

## Domain Controller (DC)
Is a Windows Server host running the Active Directory Domain Services (ADDS) role. It serves as the authoritative authority for authenticating and authorizing all entities in the domain

In brief, is ==a central server that **manages user access**, **handles login security**, and **enforces network rules**==

> [!example]
> **Domain Controller (DC) is the Front Desk Clerk / Receptionist:** It’s the actual machine (the server) running that registry. When a guest walks up and says, _"Hi, I'm John, here is my ID, can I have my room key?"_, the Clerk (DC) looks at the Registry (AD), confirms John's identity, and hands over the key. **The DC is the worker doing the job.**

## Demilitarized Zone (DMZ)
==Is an isolated network segment that sits between a company's trusted internal network and the untrusted public internet.==

> [!example]
> - **The Internet** is the street outside. Anyone can be there.
> - **The DMZ** is the lobby. Visitors (internet users) are allowed in here to talk to the receptionist (the web server).
> - **The Internal Network** is the secure office area behind the lobby. Only authorized employees can get in.
> 

The purpose of a DMZ is to add a layer of security for services that need to be accessible from the public internet, like a web server, email server, or DNS server.

### How it works
A DMZ is created by using a firewall to enforce strict rules about what traffic is allowed to move between the three zones:

1. **Internet to DMZ:** Traffic is **ALLOWED**, but only to specific services. For example, the public can connect to the Web Server on web ports (80 and 443).
2. **DMZ to Internal Network:** Traffic is **BLOCKED** by default. A server in the DMZ (like the web server) cannot initiate a connection to a computer on the internal network. This is the most important rule.
3. **Internal Network to DMZ:** Traffic is typically **ALLOWED** so that administrators can manage the servers located in the DMZ.

### Importance
A web server is a prime target for attackers. If an attacker compromises the web server and it's in a DMZ, they are trapped in that isolated "lobby." They cannot directly attack the company's internal workstations, file servers, or domain controllers because the firewall blocks that path.

Without a DMZ, that compromised web server would be on the same network as all the company's critical assets, giving the attacker a direct path to pivot and cause much more damage.

## OSPF (Open Shortest Path First)
==OSPF is a dynamic routing protocol== used by routers to automatically find the most efficient paths for data to travel across a network. Think of it as a GPS system for your internal network; it helps routers build a map and calculate the best routes to every destination.

### How does it work (in simple terms)?

1. **Discover Neighbors:** Routers send out "Hello" packets to find other OSPF-enabled routers on the same network.
2. **Share Maps:** Once they become "neighbors," they exchange information about all the networks they know about. These messages are called **advertisements**.
3. **Build a Full Picture:** Each router collects all these advertisements to build a complete topological map of the entire network.
4. **Find the Best Route:** Using this map, each router independently calculates the "shortest path" (based on a metric called `cost`, which is usually related to link speed) to every possible destination.

> [!important]
> Think of it like this:
> 
> `[Your PC]` <--> `[Your Home Router]` <--> **THE INTERNET** <--> `[HTB's Router]` <--> `[HTB's Web Server]`
> 
> OSPF is not used for the link between `[Your Home Router]` and `[The Internet]`.
> 
> Instead, OSPF is used _inside_ the complex networks that make up "THE INTERNET" and _inside_ the "HTB Network" to manage traffic flow between all their internal devices

### The Security Risk (As seen in the module):  
The primary security weakness of OSPF is that, in its basic configuration, it can be too trusting. It was designed to work on networks where all devices were assumed to be trusted infrastructure.

- **The Problem:** If OSPF is allowed to run on a regular user network (like a workstation network), it will listen for advertisements from _any_ device on that network.
- **The Attack:** An attacker on a compromised workstation can send out a **malicious advertisement**. They can lie and claim to have a very low-cost (i.e., very fast) route to an important destination, like the company's main server or the gateway to the internet.
- **The Result:** The legitimate routers will believe this lie and update their internal maps. They will start forwarding traffic intended for the server or the internet to the attacker's machine instead. This creates a classic **Man-in-the-Middle (MITM)** attack, allowing the attacker to intercept, read, or alter sensitive company data.

## Trusted and untrusted network
- **An Untrusted Network:** This is like the public lobby of your office building. It's full of employees, visitors, and delivery people (workstations, phones, printers). ==You expect to interact with them, but you would **never** take instructions from them== on how to manage the building's security or power systems.
- **A Trusted Network:** This is like the secure "control room" in the basement. The only people in this room are other authorized building engineers (other routers, switches, admin computers). ==You **do** take instructions from the people in this room.==

## Flat Network
==Is a simple computer setup where **all devices connect to one main switch or hub==** and share a single broadcast area without routers or complex subnets dividing them