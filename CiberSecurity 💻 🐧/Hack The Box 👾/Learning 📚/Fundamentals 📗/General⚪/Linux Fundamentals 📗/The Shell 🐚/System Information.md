# Commands
## `whoami`
Display current name

## `id`
Return user ids

## `hostname`
Sets or print the name of current host system

## `pwd`
Print work directory

## `ifconfig`
Manipulate network

## `ip`
Manipulate network, more modern than `ifconfig`

## `netstat`
Shows network status. 

## `ss`
When you run **`ss`** with no arguments, it outputs a list of **all established, non-listening sockets** across every supported socket domain on the system—including TCP, UDP, raw, and local **Unix domain sockets** (which usually make up 90%+ of the output).
### Column Structure Breakdown
The output is formatted into six distinct columns:
```
Netid  State Recv-Q  Send-Q Local  Address:Port  Peer Address:Port
```

#### **`Netid`** (Network Identifier / Protocol)
Specifies the address family or protocol layer of the socket:

- **`u_str`**: Unix Stream socket (connection-oriented local IPC, like TCP but internal).
- **`u_dgr`**: Unix Datagram socket (connectionless local IPC, like UDP).
- **`u_seq`**: Unix Sequential Packet socket.
- **`tcp`**: TCP connection over IPv4 or IPv6.
- **`udp`**: UDP connection over IPv4 or IPv6.
- **`raw`**: Low-level raw network socket.
#### **`State`** (Connection Status)
Shows the current lifecycle phase of the socket:

- **`ESTAB`**: Established (actively connected and communicating).
- **`UNCONN`**: Unconnected (common for UDP or unconnected Unix sockets).
- **`TIME-WAIT`**: Socket is waiting after close to handle trailing packets.
- **`CLOSE-WAIT`**: Remote end has shut down; waiting for local process to close.
#### **`Recv-Q`** (Receive Queue)
- For **Established TCP/Unix** sockets: The count of bytes received by the network buffer but not yet read by the user application.
- Non-zero values indicate the local application is processing data slowly.
#### **`Send-Q`** (Send Queue)
- For **Established TCP/Unix** sockets: The count of bytes sent by the local application but not yet acknowledged by the remote host.
- Non-zero values point to network latency or packet loss.
#### **`Local Address:Port`**
- The local IP address (or local path file) and the bound port/service name.
- For **Unix domain sockets**, this column displays either the filesystem path (e.g., `/run/dbus/system_bus_socket`) or an abstract socket string starting with `@` or `*`.
#### **`Peer Address:Port`**
- The remote IP address (or remote path) and foreign port/service name to which the socket is connected.

## `ps`
`ps` (Process Status) is the standard Linux command used to capture a snapshot of currently running processes. In offensive security and administration, `ps` is critical for local enumeration—helping you identify running applications, process owners, background services, and privilege escalation vectors (such as credentials passed via command-line arguments).

### Syntax & Operating Styles
`ps` is unique because it supports three distinct option syntaxes:

1. **UNIX options**: Preceded by a single dash `-` (e.g., `ps -ef`).
2. **BSD options**: Written *without* a dash (e.g., `ps aux`).
3. **GNU long options**: Preceded by double dashes `--` (e.g., `ps --forest`).

In practice, **BSD style (`ps aux`)** and **UNIX style (`ps -ef`)** are the most common.

### The Standard BSD Enumeration: `ps aux`

This is the most widely used command to list every process on the system.

```bash
ps aux

```

#### Flag Breakdown

* **`a`**: Displays processes for **all users**, not just the current user's session.
* **`u`**: Displays user-oriented format (shows process owner, memory/CPU usage, start time, and command string).
* **`x`**: Includes processes that are **not attached to a terminal** (TTY), such as background daemons and system services.

#### Column Structure Breakdown
* **`USER`**: The Linux user running the process (e.g., `root`, `www-data`, `nobody`).
* **`PID`**: Process ID (unique numerical identifier).
* **`%CPU` / `%MEM**`: Percentage of CPU and RAM being consumed.
* **`VSZ`**: Virtual memory size (in KB).
* **`RSS`**: Resident Set Size (non-swapped physical memory used, in KB).
* **`TTY`**: The controlling terminal (e.g., `pts/0` for an SSH/terminal session, `?` for background services).
* **`STAT`**: Process state code (e.g., **S** = sleeping, **R** = running, **Z** = zombie).
* **`START` / `TIME**`: Execution start time and total CPU execution time consumed.
* **`COMMAND`**: The full binary path and command-line arguments used to launch the process.

## `who`
Displays who is logged in.

## `env`
Print environment  variables

## Other commands
| **Command** | **Primary Focus**           | **Security & Enumeration Purpose**                                                 |
| ----------- | --------------------------- | ---------------------------------------------------------------------------------- |
| **`lsof`**  | Open files, sockets, pipes  | Correlates processes with open files, ports, and sockets.                          |
| **`lsblk`** | Storage drives & partitions | Identifies unmounted drives, secondary volumes, and backup disks.                  |
| **`lspci`** | Motherboard PCI components  | Detects virtual machine environments, GPU capabilities, and network hardware.      |
| **`lsusb`** | USB buses & plugged devices | Identifies connected USB storage, rogue hardware adapters, or physical keyloggers. |
