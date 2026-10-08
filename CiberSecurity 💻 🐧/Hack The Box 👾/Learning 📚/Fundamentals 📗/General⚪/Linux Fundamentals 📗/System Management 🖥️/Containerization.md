Containerization is the process of packaging and running applications in isolated environments, typically referred to as containers. These containers provide lightweight, consistent environments for applications to run, ensuring that they behave the same way, regardless of where they are deployed. Technologies like Docker, Docker Compose, and Linux Containers (LXC) make containerization possible, primarily in Linux-based systems. Containers differ from virtual machines in that they share the host system's kernel, making them far more lightweight and efficient. With these technologies, users can quickly create, deploy, and manage applications with improved security, portability, and scalability.

# Docker
Imagine Docker containers as a sealed lunchbox. You can eat the food (run applications) inside, but once you close the box (stop the container), everything resets. To make a new lunchbox (new container) with updated contents (modified configurations), you create a new recipe (Dockerfile) based on the original. When serving multiple lunchboxes in a restaurant (production), you'd use a kitchen system (Kubernetes/Docker Compose) to manage all the orders smoothly.

## Install Docker-Engine

### 1. Preparation & Security Configuration
Before fetching Docker, the system must set up secure channels to verify that the downloaded packages haven't been tampered with.

Bash
```
# Update the local package index to ensure we fetch current package metadata
sudo apt update -y

# Install essential utilities for secure network communication and repository management:
# - ca-certificates: Validates SSL/TLS certificates for HTTPS downloads
# - curl: Command-line tool to fetch files over HTTPS
# - gnupg: Enables GPG key management for package signature verification
# - lsb-release: Retrieves Debian/Ubuntu distribution release details dynamically
sudo apt install ca-certificates curl gnupg lsb-release -y

# Create a dedicated directory with strict permissions (0755: read/write/exec for root, read/exec for others)
# to store trusted third-party GPG encryption keys
sudo mkdir -m 0755 -p /etc/apt/keyrings

# Fetch Docker's official GPG public key via curl (-f: fail silently on HTTP errors, -s: silent, -S: show errors, -L: follow redirects),
# pipe (|) it to gpg to convert (dearmor) from ASCII-armored format into a binary keyring format,
# and write (-o) it to /etc/apt/keyrings/docker.gpg
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# Register Docker's official APT repository in a custom sources file (/etc/apt/sources.list.d/docker.list):
# - arch=$(dpkg --print-architecture): Dynamically inserts system architecture (e.g., amd64)
# - signed-by=/etc/apt/keyrings/docker.gpg: Tells APT to explicitly verify this repo using ONLY the downloaded Docker GPG key
# - $(lsb_release -cs): Dynamically resolves the codename of your OS release (e.g., jammy, focal)
# - sudo tee: Writes the echoed string into the output file while ignoring stdout (/dev/null)
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

#### Why not just `apt install`?
Operating system repositories (like Debian or Ubuntu) prioritize extreme stability over cutting-edge updates. Their default repos often carry **outdated Docker builds** or generic community maintainer builds (`docker.io`). Adding Docker's explicit third-party repository guarantees access to **official security updates** and essential features like Compose V2 plugins.

### 2. Docker Engine Installation
Once the new repository is registered and trusted, the script syncs the package index and installs the full Docker runtime stack.

Bash

```
# Refresh the APT cache so the system indexes packages from the newly added Docker repository
sudo apt update -y

# Install the actual Docker ecosystem components:
# - docker-ce: Docker Community Engine (the core daemon service managing containers, images, and networks)
# - docker-ce-cli: Command-line interface tool used to issue commands to the daemon (e.g., `docker ps`)
# - containerd.io: The underlying industry-standard container runtime executing low-level container tasks
# - docker-buildx-plugin: CLI plugin enabling advanced container image build capabilities (BuildKit)
# - docker-compose-plugin: Native plugin for managing multi-container deployments using `docker compose`
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
```

### 3. Privilege Management & Initial Run

By default, interacting with the Docker daemon socket (`/var/run/docker.sock`) requires `root` privileges. To avoid appending `sudo` to every single `docker` command, your user account must be granted permissions.

Bash

```
# Modify the target user account to add (-a) it into a supplementary group (-G) named docker
sudo usermod -aG docker htb-student

# Display an informational message warning that group permissions do not take effect 
# in current running subshells until a new session login occurs
echo '[!] You need to log out and log back in for the group changes to take effect.'

# Pull and run a minimal test image from Docker Hub to confirm the engine, 
# network, and permissions are functioning properly
docker run hello-world
```

#### Critical Security Note on the `docker` Group
Adding a user to the **docker** group is functionally equivalent to giving that user full **root** access to the host machine. Anyone who can execute `docker` commands without `sudo` can easily mount the host filesystem (`-v /:/mnt`) inside a container and modify system files (e.g., `/etc/shadow`). In penetration testing and HTB labs, understanding this misconfiguration is critical for host-level privilege escalation.

## Dockerfile & Docker Hub

### 1. Dockerfile Overview
A **Dockerfile** is a plain-text script (named `Dockerfile` with no extension) containing ordered commands to assemble a **Docker Image**.

![[Pasted image 20260813120635.png|700]]

### 2. Core Dockerfile Instructions

|**Keyword**|**Purpose**|**Real-World Example**|**Offsec / Lab Significance**|
|---|---|---|---|
|**`FROM`**|Specifies the base OS or runtime image to build upon. Must be the first instruction.|`FROM kalilinux/kali-rolling`|Defines target OS, available binaries, and kernel compatibility.|
|**`WORKDIR`**|Sets the active directory inside the container for all following commands (creates it if missing).|`WORKDIR /var/www/html`|Avoids root directory clutter; defines path depth for web vulnerabilities (e.g., LFI/Path Traversal).|
|**`COPY`**|Copies local host files/directories into the image filesystem.|`COPY app.py .`|Moves source code or exploit scripts directly into the built image.|
|**`RUN`**|Executes shell commands **during build time** to install software or dependencies.|`RUN apt update && apt install -y nmap`|Installs system libraries, pen-testing tools, or missing binaries.|
|**`EXPOSE`**|**Documentation flag** stating which internal port the application listens on.|`EXPOSE 8080`|Reveals running services during container inspection (`docker inspect`).|
|**`CMD`**|Sets the default command executed **when the container starts**.|`CMD ["python3", "app.py"]`|Dictates entry process behavior (e.g., launching a web server vs spawning `/bin/bash`).|

### 3. Docker Hub Overview

**Docker Hub** (`hub.docker.com`) is the public, cloud-based registry where users and organizations store, share, and distribute container images.

  

- **Official Images:** Maintained by Docker and core developers (e.g., `ubuntu`, `python`, `postgres`, `nginx`). Highly optimized and scanned for vulnerabilities.
    
      
    
- **Community / User Images:** Published by third parties using the format `username/repository` (e.g., `kalilinux/kali-rolling`).
    
      
    
- **Pulling vs Building:** When you run `FROM python:3.11`, Docker checks your local cache first. If missing, it downloads the corresponding layers directly from Docker Hub.
    

### 4. Basic Build Workflow Cheat Sheet

Bash

```
# 1. Create directory and Dockerfile
mkdir my-app && cd my-app
nano Dockerfile

# 2. Build the image from current directory (.) with tag 'my-app:1.0'
docker build -t my-app:1.0 .

# 3. Launch a container with port mapping (-p <host>:<container>)
docker run -d -p 8080:8000 --name running-app my-app:1.0
```


## Docker Management
| **Command**      | **Description**               |
| ---------------- | ----------------------------- |
| `docker ps`      | List all running containers   |
| `docker stop`    | Stop a running container.     |
| `docker start`   | Start a stopped container.    |
| `docker restart` | Restart a running container.  |
| `docker rm`      | Remove a container.           |
| `docker rmi`     | Remove a Docker image.        |
| `docker logs`    | View the logs of a container. |

# LXC
However, while LXC and Docker are both containerization technologies, they serve different purposes and have unique features.

Docker builds upon the idea of containerization by adding ease of use and portability, which has made it highly popular in the world of DevOps. Docker emphasizes packaging applications with all their dependencies in a portable "image", allowing them to be easily deployed across different environments

| **Category**     | **Description**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Approach`       | LXC is often seen as a more traditional, system-level containerization tool, focusing on creating isolated Linux environments that behave like lightweight virtual machines. Docker, on the other hand, is application-focused, meaning it is optimized for packaging and deploying single applications or microservices.                                                                                                                                                                                                                                         |
| `Image building` | Docker uses a standardized image format (Docker images) that includes everything needed to run an application (code, libraries, configurations). LXC, while capable of similar functionality, typically requires more manual setup for building and managing environments.                                                                                                                                                                                                                                                                                        |
| `Portability`    | Docker excels in portability. Its container images can be easily shared across different systems via Docker Hub or other registries. LXC environments are less portable in this sense, as they are more tightly integrated with the host system’s configuration.                                                                                                                                                                                                                                                                                                  |
| `Easy of use`    | Docker is designed with simplicity in mind, offering a user-friendly CLI and extensive community support. LXC, while powerful, may require more in-depth knowledge of Linux system administration, making it less straightforward for beginners.                                                                                                                                                                                                                                                                                                                  |
| `Security`       | Docker containers are generally more secure out of the box, thanks to additional isolation layers like AppArmor and SELinux, along with its read-only filesystem feature. LXC containers, while secure, may need additional configurations to match the level of isolation Docker offers by default. Interestingly enough, when misconfigured, both Docker and LXC can present a vector for local privilege escalation (these techniques are covered in depth in our [Linux Local Privilege Escalation module](https://academy.hackthebox.com/module/details/51). |

## Install LXC
 ```
 sudo apt install lxc -y
 ```

## Creating Container
```
sudo lxc-create -n connatertest -t ubunto (or "download" to see all distros)
```

## Managing LXC Containers
|Command|Description|
|---|---|
|`lxc-ls`|List all existing containers|
|`lxc-stop -n <container>`|Stop a running container.|
|`lxc-start -n <container>`|Start a stopped container.|
|`lxc-restart -n <container>`|Restart a running container.|
|`lxc-config -n <container name> -s storage`|Manage container storage|
|`lxc-config -n <container name> -s network`|Manage container network settings|
|`lxc-config -n <container name> -s security`|Manage container security settings|
|`lxc-attach -n <container>`|Connect to a container.|
|`lxc-attach -n <container> -f /path/to/share`|Connect to a container and share a specific directory or file.|

## Secure LXC
Let us limit the resources to the container. In order to configure `cgroups` for LXC and limit the CPU and memory, a container can create a new configuration file in the `/usr/share/lxc/config/<container name>.conf`

In this configuration file, we can add the following lines to limit the CPU and memory the container can use.

```
lxc.cgroup.cpu.shares = 512 lxc.cgroup.memory.limit_in_bytes = 512M
```

When working with containers, it is important to understand the `lxc.cgroup.cpu.shares` parameter. This parameter determines the CPU time a container can use in relation to the other containers on the system. By default, this value is set to 1024, meaning the container can use up to its fair share of CPU time. However, if we set this value to 512, for example, the container can only use half of the CPU time available on the system. This can be a useful way to manage resources and ensure all containers have the necessary access to CPU time.

One of the key parameters in controlling the resource allocation of a container is the `lxc.cgroup.memory.limit_in_bytes` parameter. This parameter allows you to set the maximum amount of memory a container can use. It's important to note that this value can be specified in a variety of units, including bytes, kilobytes (K), megabytes (M), gigabytes (G), or terabytes (T), allowing for a high degree of granularity in defining container resource limits.