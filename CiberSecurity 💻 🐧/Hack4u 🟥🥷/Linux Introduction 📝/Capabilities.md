They're special privileges or capabilities you need to set on certain binaries beforehand so they can run specific tasks. 

SUID binaries like `/usr/bin/ping` needed root privileges to open raw sockets, but grating full root access introduced massive risk if the binary contained vulnerabilities. Capabilities solve this by granting only the exact privilege required

# Key Capability Sets
- **Permitted (`p`)**: The capabilities that the process or file is allowed to execute.
- **Effective (`e`)**: The capabilities that are currently active and being evaluated by the kernel for permission checks.
- **Inheritable (`i`)**: Capabilities that can be passed down to child processes executed by the current process.

# Common Dangerous Capabilities
|**Capability**|**Linux Kernel Function**|**Security Risk / Abuse Vector**|
|---|---|---|
|**CAP_SETUID**|Allows a process to arbitrary change its effective **UID**.|Privilege Escalation: Execute code and force process **UID** to `0` (root).|
|**CAP_DAC_READ_SEARCH**|Bypasses File Read and Directory Search checks (**DAC**).|Data Leakage: Read arbitrary system files (e.g., **/etc/shadow**).|
|**CAP_NET_RAW**|Permits binding to raw sockets and network packet forging.|Traffic Sniffing: Perform ARP spoofing or raw network interception.|
|**CAP_SYS_ADMIN**|Overloaded administrative privileges ("new root").|System Takeover: Mount filesystems, debug kernel code, load modules.|

# Essential Commands
## Enumerating Files Capabilities
We use `getcap`
```
getcap -r / 2>/dev/null
```
- `-r`: Recursive
## Setting Capabilities
We use `setcap`
```
sudo setcap cap_setuid+ep /usr/bin/python3
```

>[!TIP] Hack Tip
>
># [GTFObins](https://gtfobins.org/)
>It's a website where we can find some exploitable binaries if they have inadequate permissions