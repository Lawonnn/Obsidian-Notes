In Linux, there are many help commands that allow the user to learn what a specific command does. Among them, we can mention:

# **`man`**
Shows detailed information (manual pages) about a specific command.

# **`--help`** or **`-h`**
These are not standalone commands; they are flags/options. Depending on the command, you use one or the other. Similar to **`man`**, but provides a more summarized set of information.

# **`apropos`**
Acts like a search engine: it allows you to search for a specific word or phrase and returns commands whose descriptions match your query.

# **`whatis`**
Displays a very brief, one-line description of a command or program.

# **`which` or (`command -v`, `type`)** / **`whereis`** / **`locate`**
Displays the path where the binary, command, or program is located on the system.

|**Feature**|**which**|**whereis**|**locate**|
|---|---|---|---|
|**Primary Target**|Executables in `$PATH`|Binaries, Man pages, Source code|Any file matching a pattern|
|**Search Method**|Traverses `$PATH` live|Live scan of standard system directories|Queries indexing database|
|**Speed**|Instant|Extremely fast|Extremely fast|
|**Finds New Files Immediately?**|Yes|Yes|No (requires `updatedb`)|
|**Primary Use Case**|Identify which binary executes when called|Find manual pages and binary paths|Quick system-wide file search|

For more info go: https://explainshell.com/