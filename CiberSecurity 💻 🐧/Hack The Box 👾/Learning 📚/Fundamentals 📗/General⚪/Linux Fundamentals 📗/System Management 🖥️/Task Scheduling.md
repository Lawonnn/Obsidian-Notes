Task scheduling is a critical feature in Linux systems that allows users and administrators to automate tasks by running them at specific times or regular intervals, eliminating the need for manual initiation.
Task scheduling in general is like setting a coffee or tea maker to brew automatically each morning. Once programmed, it prepares coffee or tea at the desired time without further intervention, ensuring a fresh cup is ready when you need it.

# Systemd
Systemd is a service used in Linux systems such as Ubuntu, Redhat Linux, and Solaris to start processes and scripts at a specific time. With it, we can set up processes and scripts to run at a specific time or time interval and can also specify specific events and triggers that will trigger a specific task. To do this, we need to take some steps:

1. Create a timer (schedules when your `mytimer.service` should run)
2. Create a service (executes the commands or script)
3. Activate the timer

## Create .timer
Timer configuration files must be created in the same directory as your service file: **`/etc/systemd/system/`**.

> **Important HTB Rule:** The base name of the timer file **must match** the base name of the service file.
> 
> - Service file: `target_monitor.service`
>     
> - Timer file: `target_monitor.timer`

```
sudo mkdir /etc/systemd/system/mytimer.timer.d
sudo nano /etc/systemd/system/mytimer.timer
```

### Mytimer.timer
```
[Unit]
Description=My Timer

[Timer]
OnBootSec=3min
OnUnitActiveSec=1hour

[Install]
WantedBy=timers.target
```

#### **`[Unit]`**

- **`Description`**: A free-form human-readable string identifying what this timer is for in log outputs and `systemctl` listings.

#### **`[Timer]`**

This section defines the execution schedule logic.

- **`OnBootSec=3min`**: A **monotonic timer** setting. Tells `systemd` to fire the timer **3 minutes after the operating system boots up**.
    
- **`OnUnitActiveSec=1hour`**: Tells `systemd` to re-trigger the task **1 hour after the associated service was last activated**.
    
- _(Implicit default)_ **`Unit=`**: By default, `systemd` looks for a service file with the exact same base name. `mytimer.timer` will automatically look for and trigger `mytimer.service` in the same directory. If your service has a different name (e.g., `backup.service`), you would explicitly define `Unit=backup.service` inside the `[Timer]` section.

#### **`[Install]`**

- **`WantedBy=timers.target`**: Tells the system to hook this timer into the system-wide timer manager when you run `sudo systemctl enable mytimer.timer`. This ensures the timer automatically starts watching the clock on boot.

### Mytimer.service
#### 1. Breakdown of the Service Unit File

When you edit **`/etc/systemd/system/mytimer.service`**, you configure three distinct configuration sections:

Ini, TOML

```
[Unit]
Description=My Service

[Service]
ExecStart=/full/path/to/my/script.sh

[Install]
WantedBy=multi-user.target
```

- **`[Unit]`**: Contains general metadata.
    
    - **`Description=My Service`**: Sets an arbitrary text string used in logging (`journalctl`) and status queries (`systemctl status`).
        
- **`[Service]`**: Defines execution rules.
    
    - **`ExecStart=/full/path/to/my/script.sh`**: Specifies the exact binary or script to execute. **`systemd`** requires an absolute path here (e.g., `/usr/local/bin/script.sh`). Relative paths like `./script.sh` will cause the service to fail.
        
    - **Note on `Type=`**: In this basic example, `Type=` is omitted, so **`systemd`** defaults to `Type=simple`. For tasks controlled strictly by timers, adding `Type=oneshot` is standard practice because it tells **`systemd`** the process is expected to run, finish, and exit.
        
- **`[Install]`**: Configures boot behavior when enabled directly.
    
    - **`WantedBy=multi-user.target`**: Defines the system runlevel/target. **`multi-user.target`** corresponds to standard multi-user non-graphical mode (similar to traditional Runlevel 3).
        

#### Registering Changes: `daemon-reload`

Bash

```
# Reloads all unit files so systemd registers new or modified configurations
sudo systemctl daemon-reload
```

Unlike standard scripts that execute on demand, **`systemd`** keeps all unit files cached in memory for fast execution. Running **`daemon-reload`** forces **`systemd`** to rescan system directories (`/etc/systemd/system/` and `/lib/systemd/system/`) and update its internal dependency tree with your new `mytimer.service`.

#### Activation: Starting the Timer vs. Service

Bash

```
# Triggers the timer immediately according to its schedule configuration
sudo systemctl start mytimer.timer

# Enables the timer to automatically activate across system reboots
sudo systemctl enable mytimer.timer
```

- When using scheduled execution, **you manage the `.timer` unit, not the `.service` unit directly**.
    
- Starting the timer activates the schedule trigger in memory.
    
- When the schedule condition defined inside `mytimer.timer` is met (e.g., `OnCalendar=` or `OnBootSec=`), the timer unit automatically triggers `mytimer.service` on your behalf.
    
- Enabling `mytimer.timer` ensures the schedule itself persists after a system reboot.

# Cron
**Cron** is a time-based job scheduling daemon in Linux and Unix-like operating systems. It runs silently in the background (**`crond`**) and executes commands or scripts automatically at specified intervals, dates, or times.

## 1. Core Architecture: How Cron Works

The cron service continuously checks specific configuration files known as **crontabs** (cron tables) every minute to see if any scheduled tasks match the current system time.

- **Daemon**: **`crond`** (runs continuously in the system background).
    
- **Configuration Files**: **`crontab`** files (where schedule rules are stored).
    
- **Log Location**: Typically stored in **`/var/log/syslog`** or **`/var/log/cron`**.
    

## 2. Crontab Syntax (The 5 Time Fields)

Every line in a crontab file consists of **5 time/date fields** followed by the command or script path to execute.

Plaintext

```
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12)
# |  |  |  |  .---- day of week (0 - 6) (Sunday = 0 or 7)
# |  |  |  |  |
  *  *  *  *  *  /path/to/command_or_script.sh
```

### Syntax Special Characters

- **`*` (Wildcard)**: Represents "every" interval (e.g., `*` in the hour field means every hour).
    
- **`,` (Value List)**: Separates explicit values (e.g., `1,15,30` in the minute field).
    
- **`-` (Range)**: Specifies an inclusive range (e.g., `1-5` in the day-of-week field for Monday through Friday).
    
- **`/` (Step Value)**: Defines step intervals (e.g., `*/15` in the minute field means every 15 minutes).
    

## 3. Common Schedule Examples

|**Expression**|**Schedule Description**|
|---|---|
|**`0 * * * *`**|Runs at the top of every hour (e.g., 1:00, 2:00, 3:00).|
|**`*/15 * * * *`**|Runs every 15 minutes.|
|**`0 0 * * *`**|Runs every day at midnight (00:00).|
|**`0 2 * * 1`**|Runs every Monday at 2:00 AM.|
|**`30 8 1 * *`**|Runs on the 1st day of every month at 8:30 AM.|
|**`0 0 1,15 * *`**|Runs at midnight on the 1st and 15th of every month.|

## 4. Short-hand Macros

Cron supports predefined shortcuts for common time settings:

- **`@reboot`**: Run once when the system boots up.
    
- **`@hourly`**: Run once an hour (`0 * * * *`).
    
- **`@daily`**: Run once a day (`0 0 * * *`).
    
- **`@weekly`**: Run once a week (`0 0 * * 0`).
    
- **`@monthly`**: Run once a month (`0 0 1 * *`).
    

## 5. Crontab Commands

You manage user-specific cron jobs using the **`crontab`** command-line utility.

Bash

```
# Edit the current user's crontab file
crontab **-e**

# List all active cron jobs for the current user
crontab **-l**

# Remove all cron jobs for the current user
crontab **-r**

# Edit the crontab for a specific user (requires root/sudo)
sudo crontab **-u** username **-e**
```

## 6. System-Wide Cron vs. User Cron

1. **User Crontabs**: Stored in **`/var/spool/cron/crontabs/`**. Created and managed via `crontab -e`.
    
2. **System-Wide Crontab**: Located at **`/etc/crontab`**. This file includes an additional **`user`** field to specify which system account runs the command:
    
    Plaintext
    
    ```
    # minute hour day month dayofweek user command
    0 3 * * * root /usr/bin/apt-get update
    ```
    
3. **Cron Directories**: System scripts can be placed directly into predefined folders executed by system cron:
    
    - **`/etc/cron.hourly/`**
        
    - **`/etc/cron.daily/`**
        
    - **`/etc/cron.weekly/`**
        
    - **`/etc/cron.monthly/`**
        

## 7. Crucial Best Practices & Gotchas

- **Use Absolute Paths**: Cron runs with a bare-minimum environment and lacks standard user PATH variables. Always use full paths like `/usr/bin/python3` instead of `python3`, and `/home/user/script.sh` instead of `./script.sh`.
    
- **Redirect Output**: Cron attempts to send an email via local MTA (`postfix`/`sendmail`) if a job produces stdout or stderr. To suppress or log output:
    
    Bash
    
    ```
    # Log both stdout and stderr to a file
    0 2 * * * /home/user/backup.sh > /home/user/backup.log **2>&1**
    ```
    
- **Script Execution Permissions**: Always verify the script has the execute bit enabled (**`chmod +x /path/to/script.sh`**).