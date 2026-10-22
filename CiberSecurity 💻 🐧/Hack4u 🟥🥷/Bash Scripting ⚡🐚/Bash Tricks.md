# Redirections:
```bash
find / -perm 4000 -executable |& grep "pattern" 
# the |& is the same of 2>&1 | (redirect stderr to stdout and pass it to the other command)
```

```bash
diff <(cat /etc/passwd) <(cat /etc/passwd)
# the <(<command>) convert the output of the command as a fake file to be treated by the terminal
```