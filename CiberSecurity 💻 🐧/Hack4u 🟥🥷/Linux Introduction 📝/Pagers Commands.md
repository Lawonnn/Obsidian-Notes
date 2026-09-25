## 1. The Core Concept: Misconfigured Boundaries
System administrators sometimes try to restrict users by setting their login shell to a custom script instead of `/bin/bash`.
* **The Goal:** Restrict the user to *only* viewing a specific file or message.
* **The Flaw:** Relying on interactive utility programs (`more`, `less`, `man`, `vi`) that were built for convenience, not containment. These programs feature **built-in command execution and editor spawning**, leading to restricted shell breakouts.
---
## 2. Exploiting Pagers (`more` / `less`)
Pagers are meant to display text page-by-page. However, their interactive features allow users to escape the intended viewing state:

* **The Terminal Height Trick:** If a file is shorter than the terminal window, pagers exit instantly. Shrinking your terminal forces the pager to pause (`--More--`), giving you an interactive window.
* **Spawning `vi` (`v`):** Pressing `v` inside `more` or `less` launches the file inside the `vi` text editor, completely bypassing the original view-only restriction.
* **Direct Shell Execution (`!command`):** Many pagers allow you to type `!bash` or `!sh` at the prompt to execute commands directly in a subshell.

---

## 3. Weaponizing Text Editors (`vi` / `vim`)

Once you are inside `vi` (whether directly or escaped from a pager), you have access to a full programming and system administration toolkit:

* **Executing Commands:** Typing `:!bash` or `:!sh` spawns an unrestricted system shell right from the editor.
* **Reading Restricted Files (`:r`):** Using `:r /path/to/file` pulls the contents of *any* readable system file directly into your workspace (e.g., pulling sensitive configuration data).
* **Writing Files (`:w`):** If permissions allow, you can write or modify critical system files, potentially creating persistence or backdoor access.

---

> [!NOTE]
> **Pro-Tip for Hackers:** Whenever you encounter an unexpected restricted binary or command-line utility during a CTF or pentest, check **GTFOBins** (gtfobins.github.io)—an open-source database detailing how Unix binaries can be exploited to bypass local security restrictions.

---