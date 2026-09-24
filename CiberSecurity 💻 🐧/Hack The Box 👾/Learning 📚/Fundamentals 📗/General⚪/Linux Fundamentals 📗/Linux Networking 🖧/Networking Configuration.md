## Network Access Control
NAC is a security approach that restricts unauthorized users and non-compliant devices from connecting to a private network. Key NAC models include:

|**Type**|**Description**|
|---|---|
|Discretionary Access Control (`DAC`)|This model allows the owner of the resource to set permissions for who can access it.|
|Mandatory Access Control (`MAC`)|Permissions are enforced by the operating system, not the owner of the resource, making it more secure but less flexible.|
|Role-Based Access Control (`RBAC`)|Permissions are assigned based on roles within an organization, making it easier to manage user privileges.|
NAC is like managing the building's security where some rooms are open to everyone (`DAC`), while others are only accessible to certain people based on strict rules (`MAC` or `RBAC`)

#  IP command
## 1. Core Architecture & Syntax

The modern **`ip`** command interacts directly with the Linux kernel via Netlink, replacing the deprecated `net-tools` package (`ifconfig`, `route`, `arp`).

  

Bash

```
# Standard Syntax Structure:
ip [OPTIONS] OBJECT COMMAND [PARAMETERS]
```

### The Primary Objects

- **`link`**: Layer 2 physical/virtual network interface properties (MAC address, state, MTU).
    
      
    
- **`address`** (or **`addr`** / **`a`**): Layer 3 IPv4/IPv6 address assignments.
    
      
    
- **`route`** (or **`r`**): Kernel routing table rules.
    
      
    
- **`neighbor`** (or **`neigh`** / **`n`**): Layer 2 to Layer 3 mapping table (ARP cache).
    
      
    

## 2. Layer 2 Interface Management (`ip link`)

Controls hardware and link states. Use **`dev`** to target specific Network Interface Cards (NICs) like **`eth0`** or **`tun0`**.

  

Bash

```
# Display all network interfaces and their Layer 2 states
ip link show

# Bring an interface down (disable)
sudo ip link set dev eth0 down

# Bring an interface up (enable)
sudo ip link set dev eth0 up
```

> **Syntax Tip:** Modern Linux allows dropping the **`dev`** keyword for brevity (e.g., `sudo ip link set eth0 up`).
> 
>   

## 3. Layer 3 IP Address Assignment (`ip addr`)

Handles IP addresses. A single physical NIC can hold **multiple IP addresses** simultaneously because MAC addresses are hardware (Layer 2) while IP addresses are logical software assignments (Layer 3).

  

### Assigning & Modifying IPs

Bash

```
# Display all IPv4 addresses
ip -4 addr show dev eth0

# Add a secondary IP address (does NOT overwrite existing IPs)
sudo ip addr add 10.10.14.5/24 dev eth0

# Remove a specific IP address
sudo ip addr del 10.10.14.5/24 dev eth0

# Replace/Update an existing IP definition in one line
sudo ip addr replace 10.10.14.20/24 dev eth0

# Wipe ALL IP addresses off an interface (Clean slate)
sudo ip addr flush dev eth0
```

### Understanding Subnet Masks & CIDR

- **CIDR Notation (`/24`)**: Represents the netmask length in bits. You **do not** need to write out `255.255.255.0`.
    
      
    
- **Forgetting CIDR**: Running `ip addr add 10.10.14.5 dev eth0` without a slash defaults to **`/32`**, which isolates the host and breaks local subnet routing.
    
      
    

## 4. Kernel Routing & Gateways (`ip route`)

Controls how packets leave your system.

  

### Viewing & Troubleshooting Routes

Bash

```
# Display the full routing table
ip route show

# Display ONLY the default gateway route
ip route show default

# Test which route/gateway the kernel will pick for a destination IP
ip route get 8.8.8.8
```

### Changing Default Gateways

A gateway **must exist and be reachable on your local subnet**, or traffic will drop.

  

Bash

```
# Method 1: Delete old gateway specifically, then add new gateway
sudo ip route del default
sudo ip route add default via 192.168.1.254 dev eth0

# Method 2: Flush ALL default routes and set a fresh gateway
sudo ip route flush default
sudo ip route add default via 192.168.1.254 dev eth0
```

> **Why `ip route replace` created a 2nd gateway:** If the existing default route has parameters like `proto dhcp` or an explicit `metric`, the kernel treats a basic `replace` command as a new, distinct route rather than an overwrite.

## 5. Key Networking Concepts Covered

### Route Metrics

- A **metric** is a priority rating or "cost" assigned to a route.
    
      
    
- **Lower metric = Higher priority.** (`metric 10` beats `metric 100`).
    
      
    
- Routes added without an explicit metric parameter implicitly receive a **`metric 0`** (highest possible priority).
    

## 6. Domain Name Resolution (`/etc/resolv.conf`)

The **`/etc/resolv.conf`** file tells the operating system which DNS servers to query when converting domain names (e.g., `hackthebox.com`) into IP addresses.

### Temporary File Editing

Editing this file directly updates your DNS servers **immediately**:

Bash

```
# Open DNS configuration file
sudo vim /etc/resolv.conf
```

**File Contents (`/etc/resolv.conf`):**

Plaintext

```
nameserver 8.8.8.8
nameserver 8.8.4.4
```

- **`nameserver`**: Directive specifying the IP address of a DNS resolver (in this example, Google's public DNS).
    

> **Crucial HTB Warning:** Edits to `/etc/resolv.conf` are **temporary** and non-persistent. Modern daemons like **`NetworkManager`** or **`systemd-resolved`** automatically overwrite this file on reboot or whenever network connections reset.

## 7. Persistent Static Networking (`/etc/network/interfaces`)

Commands like **`ip addr add`** or **`ip route add`** apply changes only in temporary memory (RAM). To make IP, netmask, gateway, and DNS configurations survive system reboots, you define them statically in **`/etc/network/interfaces`**.

### Editing Interface Definitions

Bash

```
# Edit persistent interface definitions file
sudo vim /etc/network/interfaces
```

**Static Configuration Block (`/etc/network/interfaces`):**

Plaintext

```
auto eth0
iface eth0 inet static
  address 192.168.1.2
  netmask 255.255.255.0
  gateway 192.168.1.1
  dns-nameservers 8.8.8.8 8.8.4.4
```

### Line-by-Line Parameter Breakdown

- **`auto eth0`**: Tells systemd to bring up **`eth0`** automatically during bootup.
    
- **`iface eth0 inet static`**: Configures **`eth0`** to use **static addressing** instead of requesting an IP via DHCP (`inet dhcp`).
    
- **`address 192.168.1.2`**: Defines the persistent static IPv4 address assigned to the card.
    
- **`netmask 255.255.255.0`**: Defines the subnet mask (equivalent to `/24`).
    
- **`gateway 192.168.1.1`**: Sets the persistent default router IP for outbound traffic.
    
- **`dns-nameservers 8.8.8.8 8.8.4.4`**: Automatically populates `/etc/resolv.conf` upon interface initialization, making your DNS settings **permanent**.
    

## 8. Applying Persistent Configuration Changes

Edits made to configuration files on disk do **not** take effect immediately in kernel memory. You must reload or restart the networking daemon:

Bash

```
# Restart the core Debian/Ubuntu/Kali networking service
sudo systemctl restart networking
```

- **`systemctl`**: Central control utility for systemd service daemons.
    
- **`restart networking`**: Stops and re-initializes all network interfaces according to `/etc/network/interfaces`.
