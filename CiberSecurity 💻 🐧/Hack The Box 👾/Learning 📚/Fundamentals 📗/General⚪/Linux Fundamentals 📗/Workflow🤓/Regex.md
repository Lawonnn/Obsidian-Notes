## Expanded Metacharacters & Anchors

Anchors match **positions** between characters rather than the characters themselves.

  

Plaintext

```
^          Matches the start of a line/string
$          Matches the end of a line/string
\b         Word boundary: Matches a position where a word character (\w) transitions
           to a non-word character (\W) or the start/end of a line
\B         Non-word boundary: Matches any position where \b does NOT match
.          Matches any single character (except newline)
|          Logical OR operator (e.g., cat|dog matches "cat" or "dog")
```

### Understanding Word Boundaries (`\b` vs `\B`)

A word character is defined as any letter, digit, or underscore (**`[a-zA-Z0-9_]`**).

  

- **`\b`** checks the border between a word character and a non-word character (like spaces, punctuation, or string start/end).
    
      
    - **`\bcat\b`** matches `"cat"` in `"the cat sat"`, but ignores `"catch"` or `"scat"`.
        
          
        
    - **`\badmin`** matches `"admin"` and `"administrator"`, but ignores `"sysadmin"`.
        
          
        
- **`\B`** matches inside words where no boundary exists.
    
      
    - **`\Bcat\B`** matches `"cat"` inside `"location"` or `"educate"`, but ignores `"cat"`.
        
          
        

## Character Classes & Shortcuts

Specify groups of characters to match at a single position.

  

Plaintext

```
[abc]      Matches 'a', 'b', or 'c'
[a-z]      Matches any lowercase letter from 'a' to 'z'
[0-9]      Matches any digit from 0 to 9
[^abc]     Negated set: Matches any single character EXCEPT 'a', 'b', or 'c'

Shortcuts (PCRE / ERE):
\d         Digit: Equivalent to [0-9]
\D         Non-digit: Equivalent to [^0-9]
\w         Word character: Equivalent to [a-zA-Z0-9_]
\W         Non-word character: Equivalent to [^a-zA-Z0-9_]
\s         Whitespace: Matches spaces, tabs, line breaks (\r, \n)
\S         Non-whitespace: Matches anything except spaces, tabs, line breaks
\n         Newline (Line feed)
\r         Carriage return
\t         Horizontal tab
```

## Quantifiers (Matching Repetition)

Quantifiers dictate how many times the preceding character or group should repeat.

  

Plaintext

```
*          Matches 0 or more times (Greedy)
+          Matches 1 or more times (Greedy)
?          Matches 0 or 1 time (Optional)
{n}        Matches exactly n times
{n,}       Matches n or more times
{n,m}      Matches between n and m times
```

- **Greedy vs. Lazy**: Quantifiers are greedy by default (matching as much text as possible). Append **`?`** to make them lazy (e.g., **`.*?`** stops at the first possible match).
    
      
    

## Capture Groups, Backreferences & Modifiers

Parentheses **`()`** group patterns together into memory slots numbered sequentially from 1.

  

Plaintext

```
(abc)      Captures "abc" into Group 1
\1         Backreference: Matches the EXACT string captured in Group 1
\2         Backreference: Matches the EXACT string captured in Group 2
(?:abc)    Non-capturing group: Groups "abc" without saving it to a \N variable
(?P<name>) Named capture group: Saves match under a custom name instead of a number
```

### Pattern Modifiers / Flags

Flags modify how the entire expression behaves:

  

Plaintext

```
i          Case-insensitive matching (e.g., /admin/i matches "Admin", "ADMIN")
g          Global match: Finds all matches rather than stopping at the first
m          Multiline mode: ^ and $ match the start/end of each line, not just the whole string
s          Dotall mode: Allows the dot (.) to match newlines (\n) as well
```

## Advanced Lookarounds (PCRE Only)

Assert conditions without including those characters in the match result.

  

Plaintext

```
(?=...)    Positive Lookahead: Asserts that '...' follows the current position
(?!...)    Negative Lookahead: Asserts that '...' does NOT follow
(?<=...)   Positive Lookbehind: Asserts that '...' precedes the current position
(?<!...)   Negative Lookbehind: Asserts that '...' does NOT precede
```

- **Example**: **`(?<=USER=)\w+`** $\rightarrow$ Extracts the username after `USER=` without including `USER=` in the output.
    

## Cheat Sheet Reference Table

| **Objective**              | **RegEx Pattern**                               | **Example Match**   |
| -------------------------- | ----------------------------------------------- | ------------------- |
| Match an IP address        | **`\b\d{1,3}(\.\d{1,3}){3}\b`**                 | `192.168.1.1`       |
| Match an email address     | **`[\w.-]+@[\w.-]+\.[a-zA-Z]{2,}`**             | `admin@target.com`  |
| Find quoted content safely | **`"[^"]+"`**                                   | `"config_value"`    |
| Find duplicate words       | **`\b(\w+)\s+\1\b`**                            | `the the`           |
| Match strict MAC address   | **`^([0-9A-Fa-f]{2}[:-]){5}([0-9A-Fa-f]{2})$`** | `00:1A:2B:3C:4D:5E` |