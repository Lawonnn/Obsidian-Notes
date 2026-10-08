# System Service
## **1. Core System Architecture**

When a Linux system boots up, the Linux kernel initializes hardware, mounts the root filesystem, and executes the very first user-space process: **`systemd`** (**PID 1**).

Everything that runs after boot—from background daemons to scheduled tasks, network sockets, and user login sessions—is managed directly or indirectly as a child process of `systemd`.
 
## **2. System Mechanics: Controller vs. Logger**

`systemd` delegates its administrative tasks into two primary command-line interfaces:

### **A. System Control (`systemctl`)**

- **Role:** The administrative interface used to manage, inspect, start, stop, reload, enable, or disable system units and query overall system state.
    
      
    
- **Mechanism:** Communicates directly with the `systemd` core process over D-Bus (Desktop Bus) to alter memory structures and process execution states.
    
      
    

### **B. Journal Control (`journalctl`)**

- **Role:** The central log query interface used to read, search, filter, and stream system logs.
    
      
    
- **Mechanism:** Interacts with the **`systemd-journald`** daemon. Unlike traditional plaintext log files (e.g., `/var/log/syslog`), `systemd-journald` captures binary structured logs from standard output (`stdout`), standard error (`stderr`), kernel events, and syslog API calls, storing them in structured, indexed binary files under `/var/log/journal/`.
    
      
    

## **3. The Complete Unit Taxonomy**

In the `systemd` ecosystem, resources are defined declaratively in **Unit Configuration Files**. Each extension defines a distinct operational type:

  

|**Unit Type**|**Primary Extension**|**Role & Operational Logic**|**Restaurant Analogy**|
|---|---|---|---|
|**Service**|`.service`|Manages long-running background processes, network daemons, and application services.|Department Standard Operating Procedure (e.g., Kitchen Department)|
|**Socket**|`.socket`|Listens on IPC (Inter-Process Communication) or network sockets for traffic; triggers lazy-loading of matching `.service` units on demand.|Front Desk Host waiting to seat incoming customers|
|**Timer**|`.timer`|Triggers a target `.service` unit based on precise calendar times or relative events (replaces traditional `cron`).|Scheduled Alarm Clock set for specific work shifts|
|**Target**|`.target`|Groups multiple units into logical execution states (replaces traditional SysVinit runlevels).|Operational Mode (e.g., "Prep Shift" vs. "Dinner Service" vs. "Closing")|
|**Mount**|`.mount`|Controls storage filesystem attachments and disk partitions (complements `/etc/fstab`).|Pantry / Storage Room delivery truck unloader|
|**Automount**|`.automount`|Dynamically mounts filesystems when accessed by a process.|On-demand storage vault opener|
|**Path**|`.path`|Monitors filesystem paths for changes (creation, modification, deletion) and triggers a `.service`.|Inventory Inspector watching delivery shelves|
|**Device**|`.device`|Represents physical or virtual hardware components recognized by `udev`.|Kitchen appliances and hardware tools|
|**Slice / Scope**|`.slice` / `.scope`|Manages cgroups (Control Groups) for CPU, memory, and I/O resource allocation/limits.|Budget allocations per department|

## **4. Master Reference Matrix**

|**Feature / Objective**|**System Control (systemctl)**|**Journal Control (journalctl)**|**Process Status (ps)**|
|---|---|---|---|
|**Primary Domain**|System state, unit configuration, lifecycle|Log collection, binary database, event tracing|Kernel process table snapshot|
|**Input Source**|Systemd unit files & D-Bus status|`/var/log/journal/` binary database|`/proc` filesystem|
|**Check Active State**|`systemctl is-active <unit>`|N/A|`ps aux \| grep <proc>`|
|**Read Crash Logs**|N/A|`journalctl -u <unit> -p err`|N/A|
|**Trace PID to Unit**|`systemctl status <PID>`|`journalctl _PID=<PID>`|`ps -ef` or `systemd-cgls`|
|**Real-time Monitoring**|N/A|`journalctl -f`|`top` / `htop`|

## **5. Command Reference Cheat Sheet**

### **System Control (`systemctl`)**

Bash

```
# Start, stop, and restart a service unit immediately
systemctl start ssh.service
systemctl stop ssh.service
systemctl restart ssh.service

# Enable or disable a service unit to start automatically at boot
systemctl enable ssh.service
systemctl disable ssh.service

# View detailed status, main PID, active state, and last few log lines
systemctl status ssh.service

# List all active systemd units currently loaded in memory
systemctl list-units

# Filter active units by specific type (e.g., service, timer, socket)
systemctl list-units --type=service
systemctl list-units --type=timer

# List all failed units on the system (useful for system diagnostics)
systemctl list-units --failed

# Reload systemd manager configuration after editing or adding unit files
systemctl daemon-reload
```

### **Journal Control (`journalctl`)**

Bash

```
# View all logs for a specific unit (e.g., SSH daemon)
journalctl -u ssh.service

# Stream logs in real-time as events occur (similar to tail -f)
journalctl -u ssh.service -f

# Filter logs generated during the current boot session only
journalctl -b

# Filter logs from the previous boot session
journalctl -b -1

# Filter logs within a specific time window
journalctl --since "1 hour ago"
journalctl --since "2026-08-08 00:00:00" --until "2026-08-08 12:00:00"

# Filter logs by priority severity level (e.g., errors and warnings)
journalctl -p err..warning

# View logs generated by a specific Process ID (PID)
journalctl _PID=1234
```

# Kill

## 1. Core Signals for Process Control

| **Signal**    | **Number** | **Default Action** | **Interceptable?** | **Description**                                                 |
| ------------- | ---------- | ------------------ | ------------------ | --------------------------------------------------------------- |
| **`SIGTERM`** | `15`       | Terminate process  | **Yes**            | Standard graceful termination request.                          |
| **`SIGKILL`** | `9`        | Terminate process  | **No**             | Unkillable, immediate process termination by kernel.            |
| **`SIGINT`**  | `2`        | Terminate process  | **Yes**            | Sent when pressing **`Ctrl + C`** in terminal.                  |
| **`SIGHUP`**  | `1`        | Terminate process  | **Yes**            | Sent when a terminal disconnects; reloads configs in daemons.   |
| **`SIGQUIT`** | `3`        | Core dump & exit   | **Yes**            | Sent when pressing **`Ctrl + \`**; dumps memory state to disk.  |
| **`SIGSTOP`** | `19`       | Pause execution    | **No**             | Unkillable pause request (sent by **`Ctrl + Z`** as `SIGTSTP`). |

## 2. Deep Dive: Key Signals and Mechanics

### `SIGTERM` (Signal 15 - Terminal)

- **Logic:** This is the default signal sent by the **`kill`** command. It politely asks a process to shut down.
    
      
    
- **Why it matters:** The targeted process can **catch** (intercept) this signal and execute cleanup routines—such as closing open file descriptors, writing temporary data to disk, or terminating child processes—before exiting safely.
    
      
    

### `SIGKILL` (Signal 9 - Kill)

- **Logic:** Unlike **`SIGTERM`**, **`SIGKILL`** does **not** go to the process itself; it goes directly to the Linux kernel. The kernel instantly revokes the process's execution rights and frees its allocated memory.
    
      
    
- **Why it matters:** A process **cannot catch, ignore, or block `SIGKILL`**. Use this only as a last resort when a process is completely frozen or unresponsive, as it may leave corrupted files or orphaned child processes behind.
    
      
    

### `SIGHUP` (Signal 1 - Hang Up)

- **Logic:** Originally designed to signal a lost modem connection. Modern Linux system administration uses **`SIGHUP`** to instruct background services (daemons like `nginx`, `sshd`, or `Apache`) to **reload their configuration files** without restarting the entire process.
    
      
    

## 3. Command Syntax & Practical Usage

You can send signals using either their numerical values or signal names with the **`kill`**, **`killall`**, or **`pkill`** utilities.

  

### Listing All Signals

To view the full mapping of signals on your operating system, execute:

  

Bash

```
# Display all available signals with their corresponding numbers
kill -l
```

### Sending Signals via `kill`

Syntax: `kill -<SIGNAL> <PID>`

  

Bash

```
# 1. Gracefully terminate a process with PID 1234 (default SIGTERM)
kill 1234

# 2. Forcefully terminate a stuck process with PID 1234 (SIGKILL)
kill -9 1234
# Equivalent using explicit signal name:
kill -SIGKILL 1234

# 3. Instruct a daemon (PID 5678) to reload its configuration file
kill -1 5678
# Equivalent using explicit signal name:
kill -SIGHUP 5678
```

- Flags used:
    
      
    - **`-9`** or **`-SIGKILL`**: Sends the non-catchable termination signal.
        
          
        
    - **`-1`** or **`-SIGHUP`**: Sends the hangup/reload signal.
        
          
        

### Targeted Termination with `pkill`

Syntax: `pkill -<SIGNAL> <PROCESS_NAME>`

  

Bash

```
# Send SIGKILL specifically to all instances of a stuck process by name
pkill -9 python3
```

## Summary Checklist for Memory

Focus your learning strictly on these four operational scenarios:

  

1. **Clean Shutdown:** Use default **`kill <PID>`** (**`SIGTERM` / 15**).
    
      
    
2. **Hard Force-Kill:** Use **`kill -9 <PID>`** (**`SIGKILL` / 9**).
    
      
    
3. **Reload Configuration:** Use **`kill -1 <PID>`** (**`SIGHUP` / 1**).
    
      
    
4. **Terminal Interruption:** Keyboard shortcut **`Ctrl + C`** sends **`SIGINT` / 2**.

Here is a clean Markdown note structured specifically for Obsidian, complete with tags and callouts. You can copy and paste it directly:


# Foreground vs. Background

In Unix-like operating systems, processes run in either the **Foreground (FG)** or **Background (BG)** depending on how they interact with the terminal shell.

---

## Core Concepts

### Foreground Process (FG)
- **Definition:** A process executing directly in front of the user.
- **Behavior:** Takes control of standard input (`stdin`) and standard output (`stdout`). Blocks the terminal prompt so no other commands can be entered until the process finishes or is suspended.
- **Use Case:** Interactive commands (e.g., `nano`, `vim`, `top`) or short scripts.

### Background Process (BG)
- **Definition:** A process executing independently in the background.
- **Behavior:** Does not block standard input. Releases the terminal prompt immediately, allowing the user to continue typing commands while the process executes.
- **Use Case:** Long-running tasks, non-interactive scripts, or local servers (e.g., file downloads, data processing).

---

## Key Terminal Commands & Shortcuts

> [!tip] Quick Reference
> Use `Ctrl + Z` to suspend a foreground task, then run `bg` to keep it running in the background.

| Command / Shortcut | Action |
| :--- | :--- |
| `<command> &` | Executes a command directly in the background. |
| `Ctrl + Z` | Suspends (pauses) the current foreground process. |
| `bg` | Resumes a paused process in the background. |
| `fg` | Brings a background process back into the foreground. |
| `jobs` | Lists all active jobs managed by the current shell session. |
| `kill %<job_id>` | Terminates a specific background job by its job number. |

---

## Example Workflow

```bash
# 1. Start a long task directly in the background
sleep 100 &

# 2. Start a task in the foreground, then pause it
ping google.com
# Press [Ctrl + Z] here

# 3. Resume the paused task in the background
bg

# 4. View active jobs
jobs

# 5. Bring the job back to the foreground
fg %1
````


---

*Note on your original prompt:* "Can u make me a explanation text..." can be refined depending on context:
* **Formal:** *"Could you please write an explanatory note for my Obsidian database?"*
* **Informal:** *"Can you whip up a quick note I can throw into Obsidian?"*