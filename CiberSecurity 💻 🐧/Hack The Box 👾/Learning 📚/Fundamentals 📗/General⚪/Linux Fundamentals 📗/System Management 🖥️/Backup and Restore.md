# Rsync
**`rsync`** (Remote Sync) is a file transfer and synchronization tool. Unlike standard **`cp`** or **`scp`**, **`rsync`** compares files between the source and destination and only transfers the differences (delta transfer), making it faster for backups. 

## Basic Syntax Structure

The core structure of an **`rsync`** command follows this pattern:

  

Bash

```
rsync [FLAGS] [SOURCE] [DESTINATION]
```

## 1. Local-to-Local Backup

To sync a local directory to a backup folder on the same machine:

  

Bash

```
# Sync contents of 'documents' into 'backup_dir'
rsync -av /home/user/documents/ /backup_dir/
```

### Breakdown of Flags & Logic:

- **`rsync`**: The command name.
    
      
    
- **`-a`** (**`--archive`**): Enables archive mode. This is a combination flag that preserves permissions, ownership, timestamps, symlinks, and recursively syncs directories.
    
      
    
- **`-v`** (**`--verbose`**): Prints detailed output to the terminal showing which files are being transferred.
    
      
    
- **`/home/user/documents/`**: The **SOURCE** directory.
    
      
    
- **`/backup_dir/`**: The **DESTINATION** directory.
    
      
    

> **CRITICAL SLASHTIP:** The trailing slash (`/`) at the end of the source directory determines _how_ files are copied:
> 
>   
> 
> - **`/documents/`** (with trailing slash): Copies the _contents_ inside `documents` into `/backup_dir/`.
>     
>       
>     
> - **`/documents`** (without trailing slash): Copies the `documents` folder _itself_ inside `/backup_dir/` (creating `/backup_dir/documents/`).
>     
>       
>     

## 2. Remote Backup Over SSH

In Hack The Box and real pentesting environments, you frequently sync files across machines over SSH.

  

Bash

```
# Transfer local directory to a remote server over SSH
rsync -avz -e "ssh -p 22" /home/user/notes/ htb-user@10.10.10.15:/var/backups/
```

### Breakdown of Flags & Logic:

- **`-z`** (**`--compress`**): Compresses data during file transfer to save bandwidth and speed up network transmission.
    
      
    
- **`-e "ssh -p 22"`**: Specifies the remote shell command to use (**`ssh`**) along with custom options (such as port **`22`**).
    
      
    
- **`htb-user@10.10.10.15:/var/backups/`**: The remote destination formatted as `user@host:path`.
    
      
    

## 3. Pulling Files From a Remote System

You can reverse the source and destination to download backups from a target server to your attacking machine:

  

Bash

```
SHELL

rsync -avz --backup --backup-dir=/path/to/backup/folder --delete /path/to/mydirectory user@backup_server:/path/to/backup/directory
```

With this, we back up the `mydirectory` to the remote `backup_server`, preserving the original file attributes, timestamps, and permissions, and enabled compression (`-z`) for faster transfers. The `--backup` option creates incremental backups in the directory `/path/to/backup/folder`, and the `--delete` option removes files from the remote host that is no longer present in the source directory.

If we want to restore our directory from our backup server to our local directory, we can use the following command:

```
rsync -av user@remote_host:/path/to/backup/directory /path/to/mydirectory
```
## Essential Flags Reference

|**Flag**|**Long Flag**|**Description**|
|---|---|---|
|**`-a`**|**`--archive`**|Enables recursive sync and preserves permissions, symlinks, timestamps, and groups.|
|**`-v`**|**`--verbose`**|Displays file transfer details in real-time.|
|**`-z`**|**`--compress`**|Compresses file data during transfer.|
|**`-P`**|**`--partial --progress`**|Shows a progress bar during transfer and allows resuming interrupted transfers.|
|**`--delete`**|**`--delete`**|Deletes files in the **DESTINATION** if they no longer exist in the **SOURCE** (keeps target exact replica).|
|**`--dry-run`**|**`--dry-run`**|Performs a trial run without making any actual changes (useful for testing complex syncs).|
