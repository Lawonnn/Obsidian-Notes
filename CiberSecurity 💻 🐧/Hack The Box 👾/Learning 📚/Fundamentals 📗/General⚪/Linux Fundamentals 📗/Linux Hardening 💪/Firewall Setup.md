The primary goal of firewalls is to provide a security mechanism for controlling and monitoring network traffic between different network segments, such as internal and external networks or different network zones. Firewalls play a crucial role in protecting computer networks from unauthorized access, malicious traffic, and other security threats.

# Iptables
`iptables` processes network traffic using three hierarchical layers:

- **Tables**: Group rules by purpose (e.g., filtering, network address translation).
    - **`filter`**: Default table. Used for standard traffic control (accepting or blocking packets).
    - **`nat`**: Used for port forwarding or network address translation (SNAT/DNAT).
    - **`mangle`**: Used for modifying packet headers (QoS, TTL).
        
- **Chains**: Points in the network stack where traffic is inspected:
    - **`INPUT`**: Traffic destined for the local system.
    - **`OUTPUT`**: Traffic originating from the local system and going out.
    - **`FORWARD`**: Traffic routed through the local system (used when acting as a router/gateway).
		
- **Targets**: Action taken when traffic matches a rule (`-j` flag):
    - **`ACCEPT`**: Allows the packet to pass.
    - **`DROP`**: Blocks the packet quietly without notifying the sender.
    - **`REJECT`**: Blocks the packet and sends an ICMP error message back to the sender.

## Command Structure & Syntax Reference
`iptables` is an administration tool for IPv4 packet filtering and NAT in Linux. It interacts directly with the kernel's **Netfilter** framework to inspect, modify, redirect, or drop network packets.

### 1. Universal Syntax Blueprint
Every `iptables` rule follows a strict execution sequence:
```
sudo iptables [-t TABLE] <ACTION_FLAG> <CHAIN> [MATCH_CONDITIONS] -j <TARGET> [TARGET_OPTIONS]
```
#### Command Components
- **`Table`** (`-t`): Specifies the functional database. Default is `filter`.
- **`Action Flag`** (`-A`/`-I`/`-D`): Defines how the rule is added or modified in the chain.
- **`Chain`**: The checkpoint location in the network stack inspecting the packet.
- **`Match Conditions`**: Criteria the packet must fulfill (IPs, ports, protocols, interfaces, states).
- **`Target`** (`-j`): The action taken when all match conditions are satisfied.
### 2. Table & Chain Hierarchy
![[Pasted image 20260817100147.png]]

#### Table Overview

|**Table**|**Primary Function**|**Available Chains**|
|---|---|---|
|**`filter`** _(Default)_|Packet filtering (accepting or blocking traffic).|`INPUT`, `OUTPUT`, `FORWARD`|
|**`nat`**|Network Address Translation (rewriting source/destination IPs & ports).|`PREROUTING`, `POSTROUTING`, `INPUT`, `OUTPUT`|
|**`mangle`**|Modifying IP packet headers (TTL, TOS, QoS flags, mark tags).|`PREROUTING`, `INPUT`, `FORWARD`, `OUTPUT`, `POSTROUTING`|
|**`raw`**|Exempting packets from connection state tracking (`NOTRACK`).|`PREROUTING`, `OUTPUT`|
|**`security`**|Setting Mandatory Access Control (SELinux) marks.|`INPUT`, `OUTPUT`, `FORWARD`|

### 3. Detailed Flag Reference

#### A. Chain Management Flags (Actions)

> [!WARNING]
> 
> Rules are evaluated **sequentially from top to bottom**. Rule position in the chain determines priority.
> 
>   

```
# Append a rule to the end of the INPUT chain
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT

# Insert a rule at position 1 (top priority) in the INPUT chain
sudo iptables -I INPUT 1 -s 10.10.10.50 -j DROP

# Delete rule #2 from the INPUT chain
sudo iptables -D INPUT 2

# List all rules in the filter table with numeric IPs, byte counters, and line numbers
sudo iptables -L -v -n --line-numbers

# Change default policy of INPUT chain to DROP
sudo iptables -P INPUT DROP

# Flush (wipe) all rules across all chains in the default filter table
sudo iptables -F
```

|**Flag**|**Parameter**|**Description**|
|---|---|---|
|**`-A`**|`<CHAIN>`|**A**ppend rule to the end of the specified chain.|
|**`-I`**|`<CHAIN> [NUM]`|**I**nsert rule at specified position (defaults to line `1` if omitted).|
|**`-D`**|`<CHAIN> <NUM>`|**D**elete rule by line number or exact match.|
|**`-L`**|`[CHAIN]`|**L**ist active rules in a chain or table.|
|**`-F`**|`[CHAIN]`|**F**lush (delete) all rules in specified chain or entire table.|
|**`-P`**|`<CHAIN> <TARGET>`|Set default **P**olicy for a chain (`ACCEPT` or `DROP`).|
|**`-Z`**|`[CHAIN]`|Zero packet and byte counters.|
|**`-v`**|N/A|**V**erbose output (shows interface names and packet/byte counters).|
|**`-n`**|N/A|**N**umeric output (disables slow reverse-DNS lookups for IPs/ports).|

#### B. Packet Match Flags (Filtering Conditions)
Match flags filter traffic based on network attributes. All specified match conditions in a single command must evaluate to `TRUE` for the target action to execute.
```
# Match incoming TCP packets on tun0 targeting port 4444 from subnet 10.10.14.0/24
sudo iptables -A INPUT -i tun0 -p tcp -s 10.10.14.0/24 --dport 4444 -j ACCEPT
```

|**Flag**|**Argument**|**Description**|
|---|---|---|
|**`-s`**|`<IP/Subnet>`|**S**ource address match (e.g., `-s 10.10.10.5` or `-s 192.168.1.0/24`).|
|**`-d`**|`<IP/Subnet>`|**D**estination address match (e.g., `-d 172.16.5.10`).|
|**`-p`**|`<protocol>`|**P**rotocol match (`tcp`, `udp`, `icmp`, `all`).|
|**`--dport`**|`<port>`|**D**estination **port** match (Requires `-p tcp` or `-p udp`).|
|**`--sport`**|`<port>`|**S**ource **port** match (Requires `-p tcp` or `-p udp`).|
|**`-i`**|`<interface>`|**I**ncoming network interface match (Valid in `INPUT`, `FORWARD`, `PREROUTING`).|
|**`-o`**|`<interface>`|**O**utgoing network interface match (Valid in `OUTPUT`, `FORWARD`, `POSTROUTING`).|
|**`!`**|Variable|Logical **NOT** operator (inverts match, e.g., `! -s 10.10.10.5`).|

#### C. Extension Modules (`-m`)
Extension modules provide advanced state tracking, rate limiting, and MAC address inspection.
```
# State tracking module for handling return traffic dynamically
sudo iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT

# Limit incoming ICMP (ping) packets to prevent flooding
sudo iptables -A INPUT -p icmp -m limit --limit 5/min -j ACCEPT
```

|**Module**|**Flag / Argument**|**Description**|
|---|---|---|
|**`-m conntrack`**|`--ctstate <STATE>`|Tracks connection state (`NEW`, `ESTABLISHED`, `RELATED`, `INVALID`).|
|**`-m mac`**|`--mac-source <MAC>`|Matches source MAC address (`-m mac --mac-source 00:11:22:33:44:55`).|
|**`-m multiport`**|`--dports <p1,p2...>`|Matches multiple non-contiguous ports (`-m multiport --dports 80,443,8080`).|
|**`-m limit`**|`--limit <rate>`|Rate-limits matching packets (`-m limit --limit 3/minute`).|

#### D. Targets (`-j`) & Target Parameters
The **`-j`** (**J**ump) flag defines the action to execute upon a full match.
```
# Terminating Target: Drop packet silently
sudo iptables -A INPUT -s 10.10.10.100 -j DROP

# NAT Target: Rewrite destination IP and port
sudo iptables -t nat -A PREROUTING -p tcp --dport 80 -j DNAT --to-destination 192.168.1.50:8080
```

| **Target**       | **Table Context**     | **Behavior**                                                  | **Associated Parameters**      |
| ---------------- | --------------------- | ------------------------------------------------------------- | ------------------------------ |
| **`ACCEPT`**     | `filter`              | Allows the packet through the firewall.                       | None                           |
| **`DROP`**       | `filter`              | Discards packet silently with no response to sender.          | None                           |
| **`REJECT`**     | `filter`              | Discards packet and sends ICMP error response.                | `--reject-with <type>`         |
| **`LOG`**        | Any                   | Non-terminating: Logs packet to syslog/dmesg and continues.   | `--log-prefix "<msg>"`         |
| **`DNAT`**       | `nat` (`PREROUTING`)  | Rewrites **destination** IP and/or Port.                      | `--to-destination <IP[:PORT]>` |
| **`SNAT`**       | `nat` (`POSTROUTING`) | Rewrites **source** IP and/or Port (Static IP).               | `--to-source <IP[:PORT]>`      |
| **`MASQUERADE`** | `nat` (`POSTROUTING`) | Rewrites **source** IP dynamically to match egress interface. | None                           |
| **`REDIRECT`**   | `nat` (`PREROUTING`)  | Redirects packet to local host (`127.0.0.1`).                 | `--to-ports <PORT>`            |

### 4. Operational Cheat Sheet
#### Essential Security Baseline
```
# 1. Allow loopback interface traffic
sudo iptables -A INPUT -i lo -j ACCEPT

# 2. Allow established response traffic
sudo iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT

# 3. Allow SSH inbound
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT

# 4. Set default drop policy for inbound traffic
sudo iptables -P INPUT DROP
```

#### Pivot & Port Forwarding (HTB / Lab Scenarios)

Bash

```
# Forward incoming port 80 traffic on VPN interface (tun0) to internal machine
sudo iptables -t nat -A PREROUTING -i tun0 -p tcp --dport 80 -j DNAT --to-destination 172.16.5.10:8080

# Enable IP masquerading to route internal LAN traffic out through tun0
sudo iptables -t nat -A POSTROUTING -o tun0 -j MASQUERADE
```