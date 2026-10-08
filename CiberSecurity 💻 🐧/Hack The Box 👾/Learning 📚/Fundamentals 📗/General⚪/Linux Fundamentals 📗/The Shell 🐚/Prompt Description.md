# What Is It?
It is the prompt displayed by the Shell. It shows useful information such as the username, hostname, current directory, etc.
![[Shell Prompt.png]]

# Modifying It
For the Shell Prompt, there is a variable that allows you to customize the information displayed there. Depending on the Shell being used, this variable and its parameters will change. In **Bash**, the variable is **`PS1`**. Since this variable is local, it will not be displayed when using the **`env`** command, which only lists environment variables. To view it, use the **`set`** command, which displays all existing variables.

The syntax of **`PS1`** is a bit tricky to understand, but an example would be:
`\u@\h:\w\$` 
Which result is:
`usuario@mipc:~/documentos$`

Where `\u`, `\h`, `\w`, and `\$` are special characters that display specific information. There are many special escape sequences, but here are a few:
![[Special Characters PS1.png]]

Additionally, you can add color using ANSI color codes:
`\[\e[31m\] a \[\e[0m\]`

Where:
- **`\[\e[31m\]`**: Marks the beginning of where the color styling starts (red in this case).
- **`\[\e[0m\]`**: Resets the text formatting back to default.
- **`a`**: Represents the character or special escape sequence you want to display inside the Shell Prompt.