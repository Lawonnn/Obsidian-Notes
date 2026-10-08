# GUI
Unlike Windows or macOS, where the graphical user interface (GUI) is a single, locked-in system deeply integrated into the operating system, the Linux GUI is entirely modular. It is built in distinct layers, meaning you can rip out and replace any piece of the puzzle without breaking the core system.
## The Boot-to-GUI Pipeline

- **Step 1: The Display Manager (DM)** runs first. When the Linux kernel finishes booting, it starts a service (usually **LightDM** in Kali). This provides the graphical login prompt. It runs as the `root` user because it needs to verify your password against the system's security files.
    
- **Step 2: The Display Server** is launched. Once you log in, the DM spawns the display server (**X11** or **Wayland**) for your specific user session. This claims control of your graphics card and sets up the blank canvas.
    
- **Step 3: The Window Manager (WM)** and **Desktop Environment (DE)** start simultaneously. The display server reads your configuration files and launches your DE (like **XFCE**). The DE loads its taskbars and icons, while its built-in Window Manager (like **Xfwm4**) starts drawing borders around your applications.
    

If any one of these components crashes, you can often restart it without rebooting the whole computer—something that is generally impossible in monolithic systems like Windows where the GUI is baked directly into the operating system core.