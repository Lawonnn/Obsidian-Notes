Package are files that contain binaries of software, configuration files information about dependencies and keep track of updates and upgrades.

The features that most package management systems provide are:
- Package downloading
- Dependency resolution
- A standard binary package format
- Common installation and configuration locations
- Additional system-related configuration and functionality
- Quality control

# Commands

## Linux Package Management Cheat Sheet

|**Feature**|**APT (apt)**|**dpkg (dpkg)**|
|---|---|---|
|**Primary Scope**|Repository-based remote management|Local `.deb` file management|
|**Dependency Resolution**|Automatic (downloads required libraries)|None (fails if dependencies are missing)|
|**Network Access**|Downloads from mirrors via `/etc/apt/sources.list`|Operates offline on local storage|
|**Primary Use Case**|Installing, updating, and upgrading system software|Installing standalone `.deb` installers|
### APT Commands (`apt`)

- `sudo apt update`
    
      
    - **Purpose:** Refreshes the local package index from remote repositories.
        
          
        
    - **Key Detail:** Does **not** upgrade software; only updates metadata.
        
          
        
- `sudo apt upgrade -y`
    
      
    - **Purpose:** Upgrades all installed packages to their latest available versions.
        
          
        
- `sudo apt install <package> -y`
    
      
    - **Purpose:** Downloads and installs a package along with all necessary dependencies.
        
          
        
- `apt search <keyword>`
    
      
    - **Purpose:** Searches repository descriptions for matching software.
        
          
        
- `apt show <package>`
    
      
    - **Purpose:** Displays package metadata (version, size, dependencies, description).
        
          
        
- `sudo apt remove <package>`
    
      
    - **Purpose:** Uninstalls package binaries while keeping configuration files.
        
          
        
- `sudo apt purge <package>`
    
      
    - **Purpose:** Removes package binaries **and** deletes all related system configuration files.
        
          
        
- `sudo apt autoremove --purge -y`
    
      
    - **Purpose:** Cleans out orphaned packages installed as dependencies that are no longer needed.
        
          
        

### dpkg Commands (`dpkg`)

- `sudo dpkg -i <file.deb>`
    
      
    - **Purpose:** Installs a locally downloaded `.deb` package.
        
          
        
- `sudo apt install -f`
    
      
    - **Purpose:** Fixes broken dependencies created after running a `dpkg -i` command.
        
          
        
- `dpkg -l | grep <package>`
    
      
    - **Purpose:** Searches locally registered packages installed on the system.
        
          
        
- `dpkg -L <package>`
    
      
    - **Purpose:** Lists every file and directory path installed by a specific package.
        
          
        

## Critical Files & Locations

- `/etc/apt/sources.list`
    
      
    - **Purpose:** Primary configuration file containing mirror URLs, release channels (`kali-rolling`), and component branches (`main`, `contrib`, `non-free`).
        
          
        
- `/etc/apt/sources.list.d/`
    
      
    - **Purpose:** Directory reserved for adding third-party repository configuration files.