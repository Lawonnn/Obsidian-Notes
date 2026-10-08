# X11 Forwarding via SSH
### Core Concept
**X11 Forwarding** allows you to run a graphical Linux app on a **remote server** while rendering its window, controls, and UI on your **local display**.
Instead of streaming raw screen pixels like VNC or RDP, X11 sends **lightweight drawing instructions** over the network.
### Key Components
- **X Server (Local):** Controls your physical screen, mouse, and keyboard. Runs locally on your workstation (`DISPLAY=:0`).
- **X Client (Remote):** The application program itself (e.g., `wireshark` or `firefox`) running on the target machine.
- **`$DISPLAY` Variable:** Directs the remote app where to send drawing commands. When connected via SSH, it points to a local proxy (e.g., `localhost:10.0`) mapped to TCP port **6010**.   
- **SSH Tunnel (Port 22):** Encrypts and forwards the X11 network protocol back to your local system, bypassing the need to open extra firewall ports.
### How the Data Flows
1. You run `ssh -XC user@target`.
2. The remote SSH server sets `$DISPLAY` to `localhost:10.0`.
3. You start a GUI app in the SSH terminal.
4. The app sends drawing instructions to `localhost:10.0`.
5. SSH intercepts the traffic, encrypts it over **Port 22**, forwards it to your local machine, and paints the window on your local screen.