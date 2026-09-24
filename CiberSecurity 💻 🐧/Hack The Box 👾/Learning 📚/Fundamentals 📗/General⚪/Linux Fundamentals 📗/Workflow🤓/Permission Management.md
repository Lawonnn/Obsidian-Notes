## Linux Permission Management Cheat Sheet

---

### 1. Basic Classes & Permission Types

Linux categorizes file access across **three classes** using **three core permission types**:

* **Classes**:
* **`u`**: User (Owner)
* **`g`**: Group
* **`o`**: Others (World)
* **`a`**: All (`u`, `g`, and `o` combined)


* **Permissions**:
* **`r`** (Read): View file contents / List directory contents.
* **`w`** (Write): Edit file contents / Create, delete, or rename files in a directory.
* **`x`** (Execute): Run file as a program or script / Enter (`cd` into) a directory.



---

### 2. Changing Permissions (`chmod`)

Permissions are modified using **`chmod`** via two systems:

#### A. Symbolic Notation

Uses operators (**`+`** add, **`-`** remove, **`=`** set explicitly) to tweak specific bits.

```bash
chmod u+x script.sh       # Add execute permission for owner
chmod go-w notes.txt      # Remove write permission for group and others
chmod a+r document.pdf    # Add read permission for everyone

```

#### B. Absolute / Octal Notation

Uses a 3-digit number calculated by summing weighted values for each class:

$$\text{Read } (r) = 4 \quad \mid \quad \text{Write } (w) = 2 \quad \mid \quad \text{Execute } (x) = 1$$

| Octal Value | Binary | Permissions | Common Usage |
| --- | --- | --- | --- |
| **0** | `000` | `---` | No access |
| **4** | `100` | `r--` | Read-only |
| **5** | `101` | `r-x` | Read + Execute (Standard for shared binaries/dirs) |
| **6** | `110` | `rw-` | Read + Write (Standard for documents/files) |
| **7** | `111` | `rwx` | Read + Write + Execute (Owner full access) |

```bash
chmod 700 exploit.py     # rwx------ (Owner full access, others restricted)
chmod 644 document.txt   # rw-r--r-- (Owner edit, world read-only)
chmod 755 binary.sh      # rwxr-xr-x (Owner full access, world read/execute)

```

---

### 3. Special Permissions (4-Digit Octal Notation)

Special bits are prefixed to standard octal modes (e.g., `4755`) or applied symbolically to modify execution/directory behavior.

| Special Bit | Octal Value | Symbolic | Target | Behavior | `ls -l` Indicator |
| --- | --- | --- | --- | --- | --- |
| **SUID** | **`4`** | `u+s` | File | Executes with privileges of the **file owner** (e.g., `passwd`). | `rws------` |
| **SGID** | **`2`** | `g+s` | File / Dir | Files run as the **group**; new files in dir **inherit parent group**. | `rwxr-s---` |
| **Sticky Bit** | **`1`** | `o+t` | Directory | Only **file owner** or **root** can delete files inside (e.g., `/tmp`). | `rwxrwxrwt` |

```bash
chmod 4755 tool          # Set SUID on an executable
chmod 2775 shared_dir/   # Set SGID on a shared folder for group inheritance
chmod 1777 /tmp          # Set Sticky Bit on a multi-user directory

```

---

### 4. Ownership Management (`chown` & `chgrp`)

File permissions work alongside owner and group assignments:

```bash
# Change file owner
chown kali target.txt

# Change file group
chgrp security target.txt

# Change owner and group simultaneously
chown kali:security target.txt

# Recursively change ownership for an entire directory
chown -R kali:security /var/www/html/

```

---

### 5. Essential Verification Commands

```bash
# View long-listing format (shows permission string, owner, and group)
ls -l /path/to/file

# View directory metadata itself rather than its contents
ls -ld /path/to/directory

```