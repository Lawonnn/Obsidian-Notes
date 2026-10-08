**TCP Wrappers** is a host-based networking Access Control List (ACL) mechanism in Linux used to filter network access to Internet Protocol servers based on IP addresses or domain names.
### Core Concepts
- **Evaluation Order**:      
    1. Checks **/etc/hosts.allow** first. If a match is found, access is **granted** immediately.
    2. Checks **/etc/hosts.deny** second. If a match is found, access is **denied**.        
    3. Default Rule: If no match is found in either file, access is **granted**.
### Configuration Files & Syntax
Both configuration files use the standard syntax format:
``
```
daemon_list : client_list [: option : option ...]
```

#### 1. Permissive Rules (**`/etc/hosts.allow`**)
Defines explicit allowed connections for specific services and IP ranges or domain.

```
# Allow SSH (sshd) traffic only from the 10.129.14.0/24 subnet
sshd : 10.129.14.0/24

# Allow FTP (ftpd) connection exclusively from host IP 10.129.14.10
ftpd : 10.129.14.10

# Allow Telnet (telnetd) from any hostname ending with .inlanefreight.local
telnetd : .inlanefreight.local
```
#### 2. Restrictive Rules (**`/etc/hosts.deny`**)
Defines explicit block rules for specific services and client ranges.
```
# Deny access to ALL wrapped services from the .inlanefreight.com domain
ALL : .inlanefreight.com

# Block SSH (sshd) specifically for client IP 10.129.22.22
sshd : 10.129.22.22

# Deny FTP (ftpd) access for the entire 10.129.22.0/24 IP range
ftpd : 10.129.22.0/24
```
