## Some common protocols

#### 1. Secure Shell (SSH)

- **Description**: A secure and encrypted protocol used to remotely log into a machine and execute commands. It replaced older, insecure protocols like Telnet and RSH.
- **Cybersecurity Context**: As a penetration tester, you will often scan for open SSH ports to see if you can log in using weak or default credentials.
- **Standard Port**: `22/TCP`

#### 2. File Transfer Protocol (FTP)

- **Description**: A protocol for transferring files between a client and a server. It is notoriously insecure because it sends credentials and data in cleartext (unencrypted).
- **Cybersecurity Context**: FTP servers are a common target. You'll often check for "anonymous" (no password) access, or try to intercept credentials using a network sniffer.
- **Standard Port**: `21/TCP` (for commands) and `20/TCP` (for data).

#### 3. Simple Mail Transfer Protocol (SMTP)

- **Description**: The primary protocol for sending emails between servers.
- **Cybersecurity Context**: Misconfigured SMTP servers can sometimes be used to send unauthorized emails (spam) or to enumerate valid user email addresses on a server.
- **Standard Port**: `25/TCP`

#### 4. Hypertext Transfer Protocol (HTTP)

- **Description**: The foundation of the World Wide Web. It's used by web browsers to request and display web pages from web servers. Like FTP, it is unencrypted.
- **Cybersecurity Context**: This is the protocol where most web application attacks occur (like SQL Injection, XSS, etc.). All traffic can be intercepted and read.
- **Standard Port**: `80/TCP`

##### Related: HTTPS (HTTP Secure)

- **Description**: This is the secure version of HTTP, which encrypts the data between your browser and the web server. It uses TLS/SSL encryption.
- **Cybersecurity Context**: Even though the traffic is encrypted, the web application running on the server can still have vulnerabilities.
- **Standard Port**: `443/TCP`

#### 5. Server Message Block (SMB)

- **Description**: A protocol primarily used by Windows systems for sharing files, printers, and other resources on a local network.
- **Cybersecurity Context**: SMB is a major focus in internal network penetration tests. It has historically had many critical vulnerabilities (like EternalBlue) and is often misconfigured, allowing for unauthorized access to files.
- **Standard Port**: `445/TCP`

#### 6. Network File System (NFS)

- **Description**: The primary protocol used by Linux and Unix systems to share files and directories over a network. It's the Unix/Linux equivalent of SMB.
- **Cybersecurity Context**: Misconfigured NFS shares can allow an attacker to access sensitive files on a server, sometimes with read and write permissions.
- **Standard Port**: `2049/TCP`

#### 7. Simple Network Management Protocol (SNMP)

- **Description**: Used to monitor and manage network devices like routers, switches, and servers.
- **Cybersecurity Context**: SNMP often uses default "community strings" (like passwords, e.g., `public` or `private`). If left unchanged, attackers can use SNMP to gather a huge amount of information about your network, including device models, network routes, and user accounts.
- **Standard Port**: `161/UDP`

## Foundational Protocols (Essential Knowledge)

These are so fundamental to how networks operate that you absolutely must know them.

#### 8. Domain Name System (DNS)

- **Description**: Translates human-readable domain names (like `www.hackthebox.com`) into machine-readable IP addresses (like `104.22.60.128`). The internet wouldn't be usable without it.
- **Cybersecurity Context**: DNS is used for reconnaissance (finding a company's servers), and it can be exploited through attacks like DNS spoofing or used to exfiltrate data from a compromised network.
- **Standard Port**: `53/UDP` (for queries) and `53/TCP` (for zone transfers).

#### 9. Remote Desktop Protocol (RDP)

- **Description**: A proprietary protocol from Microsoft that provides a user with a graphical interface to connect to another computer over a network. It's like SSH but for a full desktop experience.
- **Cybersecurity Context**: Exposed RDP servers are a massive target for attackers who try to guess passwords (brute-force) to gain full remote control of a Windows machine.
- **Standard Port**: `3389/TCP`
