First, we download some packets:
```
apt install build-essential git vim libxcb1-dev libxcb-util0-dev libxcb-ewmh-dev libxcb-randr0-dev libxcb-icccm4-dev libxcb-keysyms1-dev libxcb-xinerama0-dev libxcb-xkb-dev libasound2-dev libxcb-xtest0-dev libxcb-shape0-dev
```

Then, we go to `Downloads/` directory to install the projects `bspwm` and `sxhkd`. First we clone those project from GitHub:
- **[https://github.com/baskerville/bspwm.git](https://github.com/baskerville/bspwm.git)**
- **[https://github.com/baskerville/sxhkd.git](https://github.com/baskerville/sxhkd.git)**
Now, we install each of these executing the following commands in their respective directories:
```
make
```

```
sudo make install
```

# Bspwm (Binary Space Partitioning Window Manager)
Bspwm is a windows manager that use binary space partition to organice the desktop windows. It is known for its simplicity and efficiency
Bspwm don't handle keyboards or others input devices by itself, it just delegate the task to other tools, making a better personalization and flexibility

# Sxhkd (Simple X Hotkey Daemon)
Sxhkd is a hotkey daemon for X Window system. It work with windows manager like Bspwm and allow user to allocate actions to a key combinations and mouse buttons