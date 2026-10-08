# Catching signals
Simple syntax
```bash
trap <function_name> <signal>
```

We use it to catch operation system signals sent to our script, such as `Ctrl + C`
# Arrays
To declare a array variable we can use:
``` bash
# Explicitly declare an indexed array named 'targets'
declare -a targets=("10.10.10.1" "10.10.10.5" "10.10.10.12")

# Implicit declaration without using 'declare'
ports=(21 22 80 443 8080)
```

Referencing an array variable without index syntax (`$targets`) returns only the first element (`index 0`). Curly braces `${}` are mandatory for accessing array mechanics.

| **Operation**      | **Syntax**             | **Description**                           |
| ------------------ | ---------------------- | ----------------------------------------- |
| **Single Element** | **`${targets[0]}`**    | Retrieves the item at index `0`           |
| **All Elements**   | **`${targets[@]}`**    | Expands to all elements as distinct words |
| **Array Length**   | **`${#targets[@]}`**   | Returns the total element count           |
