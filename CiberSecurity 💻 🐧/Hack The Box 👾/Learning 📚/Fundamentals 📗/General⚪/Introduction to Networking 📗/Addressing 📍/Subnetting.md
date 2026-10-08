The division of an address range of IPv4 addresses into several smaller address ranges is called `subnetting`.

With the help of subnetting, we can create a specific subnet by ourselves or find out the following outline of the respective network:

- `Network address`
- `Broadcast address`
- `First host`
- `Last host`
- `Number of hosts`
## 1. The Core Golden Rule: Octet Boundaries

IPv4 addresses have **32 total bits** divided into 4 octets (8 bits each). Always identify which boundary your CIDR belongs to:

|**CIDR Range**|**Active Octet**|**Boundary to Subtract From**|
|---|---|---|
|**/25 to /32**|4th Octet (`.X`)|Subtract from **32**|
|**/17 to /24**|3rd Octet (`.X.0`)|Subtract from **24**|
|**/9 to /16**|2nd Octet (`.X.0.0`)|Subtract from **16**|

## 2. Converting CIDR to Dotted-Decimal Subnet Mask

To turn a `/CIDR` into a decimal mask (e.g., `/27` $\rightarrow$ `255.255.255.224`):

1. **Find the Block Size:** $2^{(\text{Boundary} - \text{CIDR})}$
    
2. **Subtract from 256:** $\text{Magic Octet} = 256 - \text{Block Size}$
    
3. Fill all octets before it with `255` and after it with `0`.
    

> [!NOTE]
>  **Example for `/27`:**
>  
>  - Boundary = 32 $\rightarrow 32 - 27 = 5 \rightarrow 2^5 = \mathbf{32}$ (Block Size)
>      
>  - Active octet = $256 - 32 = \mathbf{224}$
>      
>  - Subnet Mask: **`255.255.255.224`**
>      


## 3. Extracting Full Network Info from IP + Mask

**Scenario:** You find target IP `10.100.50.25 / 13`.

### Step 1: Calculate Block Size

- `/13` lives in 2nd octet (Boundary 16).
    
- $16 - 13 = 3 \rightarrow 2^3 = \mathbf{8}$ (Jump by 8s in 2nd octet).
    

### Step 2: Find Network ID

- Look at 2nd octet value (`100`). Count by 8s from 0: $0, 8, \dots, 96, \mathbf{104}$.
    
- `100` falls between 96 and 104 $\rightarrow$ **Network ID:** `10.96.0.0`
    

### Step 3: Map Out the Range

- **Network ID (Start):** `10.96.0.0`
    
- **First Usable IP:** `10.96.0.1`
    
- **Last Usable IP:** `10.103.255.254`
    
- **Broadcast ID (End):** `10.103.255.255` _(One IP below next block `10.104.0.0`)_
    

## 4. How to Split a Network into Subnets

To break a big network into smaller subnets, **borrow bits from the host side**:

$$\text{Number of Subnets Created} = 2^n \quad (n = \text{Borrowed Bits})$$

**1. Choose Subnet Count: Calculate borrowed bits**.

Want 4 subnets? $2^2 = 4 \rightarrow$ You need **2 borrowed bits**.

**2. Update CIDR: Add to prefix**.

Original CIDR + Borrowed Bits.

_(e.g., `/24` + 2 bits = **`/26`**)_

**3. Find New Block Size: 2^(32 - New CIDR)**.

$32 - 26 = 6 \rightarrow 2^6 = \mathbf{64}$ IPs per subnet.

**4. Map New Networks: Count up from .0**

Count up in the active octet by **64**:

- Subnet 1: `192.168.1.0 / 26`
    
- Subnet 2: `192.168.1.64 / 26`
    
- Subnet 3: `192.168.1.128 / 26`
    
- Subnet 4: `192.168.1.192 / 26`
    

## Quick Reference Cheat Sheet

| **CIDR**          | **Bits in Octet** | **Block Size** | **Decimal Value** |
| ----------------- | ----------------- | -------------- | ----------------- |
| **/25, /17, /9**  | 1                 | **128**        | `128`             |
| **/26, /18, /10** | 2                 | **64**         | `192`             |
| **/27, /19, /11** | 3                 | **32**         | `224`             |
| **/28, /20, /12** | 4                 | **16**         | `240`             |
| **/29, /21, /13** | 5                 | **8**          | `248`             |
| **/30, /22, /14** | 6                 | **4**          | `252`             |