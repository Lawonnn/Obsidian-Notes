Attributes operate at a deeper level than permissions within the filesystem architecture itself (such as on ext4 partitions). These attribute define how the filesystem interacts with the file, regardless of the user's privilege level

## lsattr
List attributes

Syntax
```
lsattr <options> <file/directory>
```

Example
```
❯ lsattr
---------------------- ./Desktop
---------------------- ./Documents
---------------------- ./Music
---------------------- ./Pictures
---------------------- ./Templates
---------------------- ./Videos
---------------------- ./Downloads
---------------------- ./powerlevel10k
```

## chattr
Change attributes. Work similar as chmod, the syntax is pretty the same

Syntax
```
chattr <operator (+,-,=)><attribute> <directory/file>
```

You can see all available attributes in `man chattr`

Example
```
❯ sudo chattr +i testing
❯ lsattr testing
----i----------------- testing
```
- `i`: `Inmutable`, can't be modified, even by the root