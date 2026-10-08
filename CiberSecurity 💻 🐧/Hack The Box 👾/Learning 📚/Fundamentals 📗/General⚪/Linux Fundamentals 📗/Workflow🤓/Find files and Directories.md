Once we gain control of a Linux system, it is vital to know how to find configuration files or directories, as well as user scripts, among other things.
# which:
It is one of the most widely used commands. It serves to find the path of an executable file or link.

# find:
## Basic Syntax & Structure
The **`find`** command searches through directory trees to locate files and directories based on specified criteria. Unlike **`locate`**, which queries a pre-built database, **`find`** searches the real-time file system directly.

Bash
```
find [starting_directory] [expression/flags] [action]
```

- **`starting_directory`**: The path where the search begins (e.g., **`/`**, **`/home`**, or **`.`** for the current working directory). **`find`** recursively searches all subdirectories beneath this point.
- **`expression/flags`**: Tests used to match specific attributes like filename, size, permissions, or modification times    
- **`action`**: What to do with the matching results (e.g., print, delete, execute a command). If no action is specified, **`find`** defaults to printing the matching paths (**`-print`**).
## Searching by Name and Extension
To locate files using their filename or extension, use the **`-name`** or **`-iname`** flags.

Bash
```
# Case-sensitive search for a specific filename in /var/log
find /var/log -name "auth.log"

# Case-insensitive search for any .php file in the web root
find /var/www/html -iname "*.php"

# Search for files that do NOT match a specific extension
find . -type f -not -name "*.txt"
```

- **`-name`**: Performs a case-sensitive match against the specified pattern. Always wrap patterns containing wildcards (`*`, `?`) in quotes to prevent shell expansion before **`find`** runs.
- **`-iname`**: Performs a case-insensitive match (e.g., matches `.PHP`, `.Php`, and `.php`).
- **`-not`** (or **`!`**): Inverts the matching logic, returning entries that do not meet the criteria.
## Searching by File Type and Size
Filtering by file type or size helps narrow down target files during enumeration or investigation.

Bash
```
# Find regular files only in /tmp
find /tmp -type f

# Find directories only in /etc
find /etc -type d

# Find symbolic links in /usr/bin
find /usr/bin -type l

# Find files larger than 100 megabytes in the current directory
find . -type f -size +100M

# Find files exactly 0 bytes (empty files)
find /var/log -type f -size 0
```

- **`-type`**: Restricts results by file system object type:
       - **`f`**: Regular file
    - **`d`**: Directory
    - **`l`**: Symbolic link
    - **`b`**: Block device
    - **`c`**: Character device
   - **`-size`**: Filters by file size using unit suffixes:  
    - **`c`**: Bytes    
    - **`k`**: Kilobytes
	- **`M`**: Megabytes
    - **`G`**: Gigabytes
    - **`+` prefix**: Greater than the specified value.
    - **`-` prefix**: Less than the specified value.
    - **No prefix**: Exactly equal to the specified value.
## Searching by Time (Access, Modification, Change)
Linux tracks three distinct timestamps for every file: Access Time (**`atime`**), Modification Time (**`mtime`**), and Change Time (**`ctime`**).

Bash
```
# Find files modified within the last 24 hours (1 day)
find /home/user -mtime -1

# Find files accessed more than 30 days ago
find /var/www -atime +30

# Find files whose status/metadata changed in the last 60 minutes
find /etc -cmin -60
```

- **`-mtime`**: Filters based on file contents modification time (in days).
- **`-atime`**: Filters based on when the file was last read or accessed (in days).
- **`-ctime`**: Filters based on when file inode metadata/status (like permissions or ownership) was last changed (in days).
- **`-mmin`**, **`-amin`**, **`-cmin`**: Same as above, but measured in **minutes** instead of 24-hour periods.
- **`+N`**: Greater than $N$ units ago.
- **`-N`**: Less than $N$ units ago (within the last $N$ units).
## Searching by Permissions and Ownership
Finding misconfigured permissions or files owned by specific users/groups is vital for system auditing and privilege escalation assessments.

Bash
```
# Find files with exact permissions 777 (read, write, execute for all)
find /var/www -type f -perm 0777

# Find files with at least the SUID bit set (octal 4000)
find / -type f -perm -4000 2>/dev/null

# Find files owned by a specific user
find /home -user www-data

# Find files with no valid user assigned to their UID
find / -nouser 2>/dev/null
```

- **`-perm`**: Matches file permissions using octal or symbolic modes:
    - **`0777`**: Matches exact permissions ($rwxrwxrwx$).
    - **`-4000`**: Matches files where the **SUID** bit (4000) is set, regardless of other permissions. The **`-`** prefix acts as a bitwise AND mask.
- **`-user`**: Matches files owned by the specified username or UID.
- **`-group`**: Matches files owned by the specified group name or GID.
- **`-nouser`** / **`-nogroup`**: Finds files that belong to a deleted or non-existent user/group ID.
- **`2>/dev/null`**: Redirects Standard Error (stderr) to **`/dev/null`** to discard "Permission Denied" noise when running as an unprivileged user.
## Executing Actions on Found Files
The **`find`** command can pass matched files directly to other commands for processing.  

Bash
```
# Print results with detailed long listing (ls -la equivalent)
find /etc -name "*.conf" -ls

# Safely delete all temporary files ending with .tmp
find /tmp -type f -name "*.tmp" -delete

# Execute 'file' command on each match individually
find /usr/bin -type f -perm -4000 -exec file {} \;

# Execute 'grep' across matches in bulk for improved performance
find /var/log -type f -name "*.log" -exec grep -H "FAILED LOGIN" {} +
```

- **`-ls`**: Prints current matching file details in **`ls -dils`** format to stdout.
- **`-delete`**: Removes matched files directly. Note: Position this flag at the end of your command expression, as it executes immediately on match.
- **`-exec`**: Runs a specified shell command against each matching result:  
    - **`{}`**: Placeholder string replaced by the path of the current matched file.
    - **`\;`**: Terminates the **`-exec`** command block. Executes the command once per matched file.
    - **`+`**: Alternative termination to **`\;`**. Groups multiple matched files into a single command invocation to reduce process overhead.
## Advanced Operators & Security Use Cases
Combining multiple operators with logical conditions creates targeted search pipelines.

Bash
```
# Logical AND: Find .sh files that are also executable
find /opt -type f -name "*.sh" -a -executable

# Logical OR: Find files ending in .bak OR .old
find /var/www -type f \( -name "*.bak" -o -name "*.old" \)

# Limit search depth to avoid traversing deep file structures
find / -maxdepth 3 -name "config.py"

# Privilege Escalation Recon: Locate SUID binaries owned by root
find / -type f -user root -perm -4000 -exec ls -ld {} \; 2>/dev/null
```

- **`-a`**: Logical AND operator (implied by default between adjacent tests).
- **`-o`**: Logical OR operator. Requires parentheses **`\( ... \)`** escaped from shell interpretation to group criteria cleanly.
- **`-maxdepth N`**: Restricts directory traversal to at most $N$ levels below the starting directory.
- **`-mindepth N`**: Forces **`find`** to ignore tests for directory levels shallower than level $N$.
- **`-executable`**: Matches files that are readable and executable by the current running user process.![[Find Commands.png]]

# locate:
It is like a simpler `find`. Unlike `find`, `locate` works with a local database that contains information about our system's files and directories. We can update it using `sudo updatedb`.