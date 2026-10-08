The objective of this section is to learn how to filter content and manage the output of the previous commands.

# less
Displays information in a "paged" format. It is the modern and advanced version of the older `more` command.
# head
Displays the first 10 lines by default of whatever we pass to it.
# tail
Displays the last 10 lines by default of whatever we pass to it.
# sort
It is a widely used command since it serves to sort a collection of elements. Some of the most frequently used parameters are:
- `-u`: Short for **unique**. Prevents repeated elements.
- `-n`: Short for **numeric**. Sorts numbers correctly. For example, without this parameter, it would put 100 before 2. 
- `-o`: Short for **output**. Instead of using redirections, this parameter is used to send the sorted result to a file.
- `-r`: Short for **reverse**. Reverses the sorting order.
- `-k`: Short for **key**. Serves to sort according to the specified column if there is a delimiter, such as a `:`.
- `-t`: Short for **field-terminator**. Generally used with the `-k` parameter, where `-t` indicates the delimiter and `-k` the column.
# grep
It is one of the most widely used commands when you want to search for a specific pattern. It has several parameters, although the most used are:
- `-o`: Short for **only-matching**. Shows only the exact matched pattern you specified.
- `-v`: Short for **invert-match**. Shows everything except the pattern you specified.
- `-i`: Short for **ignore-case**. Disables case sensitivity.
- `-r`: Short for **recursive**. Performs the grep search recursively across directories.
- `-c`: Short for **count**. Shows the total number of matches for the requested pattern.
- `-A`, `-B`, `-C`: Short for **After**, **Before**, and **Context** respectively. Shows the requested pattern along with $n$ lines after, before, or both.
- `-n`: Short for **line-number**. Numbers the matching lines.
- `-w`: Short for **word-regexp**. Matches the exact word only.
- `-e`: Short for **regexp**. Used to prevent a leading `-` from being interpreted as a command flag (e.g., searching for "-p").
# cut
Another useful command used to "slice" lines of text and keep only what we need. Some parameters are:
- `-d`: Short for **delimiter**. Allows us to set the delimiter we want. Used together with the `-f` parameter (e.g., `cut -d ":" -f2`).
- `-f`: Short for **field**. Allows us to select the field we need; it can be a single number, a range, or a set (e.g., `cut -d ":" -f2,3` or `-f2-5` or `-f2`).
- `-c`: Short for **characters**. Allows us to cut based on a specific character position range (e.g., `cut -c 1-10` shows the first 10 characters).
# tr
A very useful command that allows you to translate or replace specific characters with others. To use it, it must be combined using a pipe (`|`).
**Syntax:**

```
| tr "(character/s to replace)" "(character/s to replace with)"
```

Some useful parameters include:
- `-d`: Short for **delete**. Deletes specified characters.
- `-s`: Short for **squeeze-repeats**. Replaces repeated instances of a character with a single character (useful for consecutive spaces or hyphens).
- `-c`: Short for **complement**. Reverses the character selection; keeps what you pass and replaces everything else.
Here is the English translation of the text:
# column
It is a less known command, but it serves to organize information a bit better by displaying it in column format. It has quite interesting parameters:
- `-t`: Short for **table**. Used alongside the `-s` parameter to present output in aligned columns.
- `-s`: Short for **separator**. Specifies the input field delimiter.
- `-N`: Short for **name**. Allows you to specify column header names.
- `-H`: Short for **hide**. Hides specific column(s) from the output.
# awk
**What is it?** It is an advanced text processor. Unlike `cut`, `awk` is intelligent: it automatically ignores consecutive whitespace and accurately understands where each column starts and ends. It is ideal for extracting data from network scans, log files, or configuration files.
**Basic Syntax:**
```
awk 'condition { action }' file.txt
```

_(If you omit the condition, the action is applied to every single line)._

### Magic Variables (Most Frequently Used)
`awk` automatically labels everything it reads. These are the variables you should memorize:
- `$0`: The entire line as a whole.
- `$1`, `$2`, `$3`...: Represents column 1, column 2, column 3, etc.
- `NF` (**Number of Fields**): The total number of columns contained in the current line.
- `$NF`: Super useful! Represents the very last column of the current line, regardless of how many columns exist.
- `NR` (**Number of Records**): The current line number (e.g., line 1, line 2).
### Handling Delimiters (-F)
By default, `awk` separates fields by spaces or tabs. If the target file uses another delimiter (such as `:`, `,`, or `|`), use the **`-F`** flag (uppercase).
Bash
```
# Extract the first field (username) using a colon as the delimiter
awk -F':' '{print $1}' /etc/passwd

# Extract columns from a CSV file using a comma as the delimiter
awk -F',' '{print $1, $3}' users.csv
```
### Grep-Style Filtering (Pattern Matching)
You can tell `awk` to execute an action only if a line matches a specific pattern. Patterns are placed inside forward slashes `/text/`.
Bash
```
# Search for "80/tcp" and display column 3 (the service name)
awk '/80\/tcp/ {print $3}' nmap_scan.txt

# Search for the word "root" and print the entire matching line
awk '/root/ {print $0}' /etc/passwd
```
### Logical Operations (Conditionals)
You can filter data using arithmetic or precise logical comparisons: `==` (Equal), `!=` (Not Equal), `>` (Greater than), `<` (Less than).

Bash
```
# Print lines only if column 1 is greater than 100
awk '$1 > 100 {print $0}' file.txt

# Print lines only if the user (column 1) is exactly equal to "admin"
awk -F':' '$1 == "admin" {print $0}' /etc/passwd
```

### BEGIN and END Blocks (Report Generation)
Used to execute specific actions _before_ parsing starts and _after_ all input processing completes. Excellent for printing table headers, summaries, or element counts.
Bash
```
awk 'BEGIN {print "--- STARTING EXTRACTION ---"} {print $1} END {print "Total lines processed
```
# sed
**What is it?** It is a stream editor without a GUI. It modifies data on the fly as it passes through the terminal. It is the definitive tool for changing IP addresses in scripts, modifying configuration files (`.conf`), or cleaning massive data lists in a single second.
**Basic Syntax (Substitution):**
```
sed 's/search/replace/' file.txt
```

_(The `s` at the beginning stands for "substitute")._
### Basic and Global Substitution (`g`)
By default, `sed` only replaces the first match it finds on each line. If you want it to replace every occurrence across the entire line, you must append `g` (**global**) at the end.
Bash
```
# Replaces only the first occurrence of "admin" with "root" on each line
sed 's/admin/root/' users.txt

# Replaces ALL occurrences of "admin" with "root"
sed 's/admin/root/g' users.txt
```
### In Place Modification (The dangerous `-i`)
Normally, `sed` prints the modified output to the screen without altering the original file. To save changes directly to the target file, use the **`-i`** (**in-place**) flag.
Bash
```
# WARNING: This overwrites the original file directly.
sed -i 's/127.0.0.1/192.168.1.50/g' exploit.py
```

_Survival Tip:_ If you use `sed -i.bak ...`, `sed` will automatically create a backup copy named `file.bak` before making changes to the original file.

### Deleting Lines (The `d` command)
`sed` is excellent for removing whole lines based on their line number or because they contain a specific string pattern. Use the **`d`** (**delete**) command.
Bash
```
# Delete line 1 (very useful for stripping CSV headers)
sed '1d' scan.csv

# Delete lines 1 through 5
sed '1,5d' file.txt

# Delete all lines containing the word "DEBUG"
sed '/DEBUG/d' server.log
```
### 4. The "Delimiter Swap" Trick
When replacing file paths containing forward slashes (e.g., `/var/www/html`), standard `/` delimiters confuse `sed` because it mistakes path separators for command structure. The solution: replace `sed`'s `/` delimiter with alternative symbols like `|` or `#`.
Bash
```
# Difficult and confusing way (escaping slashes with \):
sed 's/\/var\/www\/html/\/opt\/lampp/g' config.php

# PRO way (using the | symbol as delimiter):
sed 's|/var/www/html|/opt/lampp|g' config.php
```

