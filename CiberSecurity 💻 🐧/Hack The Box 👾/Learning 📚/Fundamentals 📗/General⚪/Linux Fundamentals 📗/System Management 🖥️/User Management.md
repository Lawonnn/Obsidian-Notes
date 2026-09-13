## 1. System Configuration Files

Linux tracks users, authentication, and permissions across four core files:

  

- **`/etc/passwd`**: World-readable file storing user account metadata (Username, UID, Primary GID, Home Directory, Default Shell).
    
      
    
- **`/etc/shadow`**: Root-only file storing encrypted password hashes, salt values, and password expiration rules. The configuration file of this one is `/etc/logins.defs`
    
      
    
- **`/etc/group`**: Lists system groups, GIDs, and supplementary group memberships.
    
      
    
- **`/etc/sudoers`**: Defines administrative privilege rules executed via **`sudo`**.
    
      
    

## 2. Managing Users

Creation and deletion are handled via high-level (interactive) or low-level (scriptable) commands:

  

|**Action**|**High-Level Tool**|**Low-Level Utility**|**Key Flags / Examples**|
|---|---|---|---|
|**Create User**|**`adduser`**|**`useradd`**|**`sudo useradd -m -s /bin/bash <user>`**<br><br>  <br>  <br><br>• **`-m`**: Creates home directory.<br><br>  <br>  <br><br>• **`-s`**: Sets default shell.|
|**Delete User**|**`deluser`**|**`userdel`**|**`sudo deluser --remove-home <user>`**<br><br>  <br>  <br><br>• **`--remove-home`**: Purges `/home/<user>`.<br><br>  <br>  <br><br>**`sudo userdel -r -f <user>`**<br><br>  <br>  <br><br>• **`-r`**: Recursively deletes home dir.<br><br>  <br>  <br><br>• **`-f`**: Forces removal if active.|

## 3. Managing Groups & Memberships

Groups aggregate permissions for access control across multiple users.

  

Bash

```
# Create a new group
sudo addgroup blue-team

# Delete an empty group
sudo delgroup blue-team

# Append an existing user to a secondary group
sudo usermod -aG blue-team analyst

# Remove a user from a specific group without deleting either entity
sudo deluser analyst sudo
```

- **`-aG`**: Critical combination—**`-a`** (**append**) prevents removing the user from their existing secondary groups when assigning **`-G`** (**groups**).
    
      
    

## 4. Password Management & Expiration

Passwords are managed via **`passwd`** and aging settings via **`chage`**.

  

### Password Commands

Bash

```
# Set or change a user's password
sudo passwd analyst

# Clear a password (makes account passwordless)
sudo passwd -d analyst

# Lock an account (prepends '!' to hash in /etc/shadow)
sudo passwd -l analyst

# Unlock an account
sudo passwd -u analyst

# Force password change on next login
sudo passwd -e analyst
```

### Password Expiration (`chage`)

Linux passwords are **not eternal by default**—they are controlled by expiration limits tracked in **`/etc/shadow`**.

  

Bash

```
# View password expiration parameters
sudo chage -l analyst

# Make a password eternal (never expires)
sudo chage -M 99999 analyst

# Enforce a 90-day password rotation with a 7-day warning
sudo chage -M 90 -W 7 analyst
```

## 5. Elevated Privileges: `sudo` & `visudo`

To grant administrative power:

  

1. **Group Assignment**: Add the user to the default administrative group:
    
      
    
    Bash
    
    ```
    sudo usermod -aG sudo analyst
    ```
    
2. **Direct Configuration**: Safely edit **`/etc/sudoers`** using **`visudo`** to prevent syntax errors:
    
    Plaintext
    
    ```
    # Syntax: User Hosts=(As_Users:As_Groups) Commands
    analyst ALL=(ALL:ALL) ALL
    ```
    

## 6. Auditing & Troubleshooting Commands

Bash

```
# Display User ID (UID), Group ID (GID), and groups for a user
id analyst

# Check user entry in /etc/passwd
grep analyst /etc/passwd

# List active user's sudo privileges
sudo -l

# Scan for orphaned files left behind by deleted users/groups
sudo find / -nogroup -o -nouser 2>/dev/null
```