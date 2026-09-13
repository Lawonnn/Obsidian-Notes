# What is Linux?

Linux is a kernel—the component that manages communication between hardware and software—although the term is generally used to refer to all operating systems that use Linux.

At its core, Linux is a Unix-like OS, meaning it shares fundamental characteristics and concepts with Unix-based operating systems.

# Philosophy

- **Everything is a file:** All system resources (hardware, software) are represented and treated as files, allowing them to be read and modified just like any other file.
    
- **The use of the graphical user interface is limited:** Linux systems are designed to be used from a shell or terminal.
    

# Components

- **Bootloader:** A piece of code executed to guide the system's boot process. In most Linux distros, the bootloader is **GRUB**.
    
- **OS Kernel:** The most important part of the system. It handles I/O interactions with the hardware.
    
- **Daemons:** Background services essential for allowing functions like media playback, scheduling, or printing text to work properly. These load after the system boots.
    
- **OS Shell:** The language interpreter between the user and the OS that allows the user to tell the OS what to do. The most well-known shells are **Bash**, **Zsh**, and **Fish**.
    
- **GUI:** The graphical user interface. See [[CiberSecurity 💻 🐧/Hack The Box 👾/Learning 📚/Fundamentals 📗/General⚪/Linux Fundamentals 📗/Introduction 💩/Glosary|Glosary]] for more info.
    

# Filesystem Hierarchy Standard (FHS)
Linux systems follow a [Filesystem Hierarchy Standard](https://refspecs.linuxfoundation.org/FHS_3.0/fhs-3.0.pdf) that dictates how OS files and directories are organized.

![[FHS.png]]