# SUID
When a file have SUID permission, you can execute this file as the owner of such file
To assign SUID
 ```
 chmod 4000 <file>
 ```
 Or
 ```
 chmod u+s <file>
 ```

> [!IMPORTANT]
> It's normal to have files with SUID permission in our system. Some of then can be risky and others no. A recently risky file with SUID was the binary `pkexec`, that abusing with that permission you can do a Privilegie Scalation very fast. The vulnerability is called `Pwnkit`

To search SUID file:
```
find /  -perm -4000
```

# SGID
It's the same history but with group
```
chmod 2000 <file>
```