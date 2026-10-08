Instead of manually positional parameters like `$1`, `$2`, `$3` ... using complex if statements, `getopts` automatically iterates through command-line options passed to your script (the well-known flags)
# Syntax
``` Bash
getopts optstring variable [args]
```
- **`optstring`**: A string containing all the flag characters your script accepts (e.g., `"u:vh"`).

- **`variable`**: A shell variable (commonly named **`opt`** or **`flag`**) that stores the flag character being evaluated in the current iteration of the loop.

Example:
```
while getopts "m:h" arg; do
  case $arg in 
    h) help_panel;;
    m) echo "Hello";;
  esac
done
```

When getopts executes, Bash automatically maintains 2 special variables:
- `$OPTARG`: holds the argument valued passed to a flag. For example, `./script.sh -u admin`
- **`OPTIND`**: Stores the index number of the _next_ command-line argument to be processed. Starts at `1` and increments each time **`getopts`** evaluates an option.