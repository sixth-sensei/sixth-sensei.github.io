# TryHack3M: Bricks Heist

***
![Bricks](https://tryhackme-images.s3.amazonaws.com/room-icons/aedc6b66c222e15ff740c282a0c3f44e.png)

### Difficulty: Easy

***
### Brief
Brick Press Media Co. was working on creating a brand-new web theme that represents a renowned wall using three million byte bricks. Agent Murphy comes with a streak of bad luck. And here we go again: the server is compromised, and they've lost access.

Can you hack back the server and identify what happened there?

***

## Nmap Scan

Running nmap scan on the machine, we have

```bash
┌──(sixth-sensei㉿senseisec)-[~/THM/Bricks]
└─$ sudo nmap -sS -sV 10.10.41.113
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-05-14 15:36 WAT
Nmap scan report for bricks.thm (10.10.41.113)
Host is up (0.30s latency).
Not shown: 996 closed tcp ports (reset)
PORT     STATE SERVICE  VERSION
22/tcp   open  ssh      OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http     WebSockify Python/3.8.10
443/tcp  open  ssl/http Apache httpd
3306/tcp open  mysql    MySQL (unauthorized)

Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 95.75 seconds
```
There are 3 open ports `ssh`, `http`, `https` and `mysql`

## Enumeration
From the brief, the task is of two parts: Hack back into the system and find evidence of breach (Forensics). Let's jump in

### Port 80
Navigating to `http://bricks.thm` since we've added it to our hosts file

[screenshot http]

Met with an error, so we'll proceed with the secure version

### Port 443
Navigating to `https://bricks.thm`, it is observed that the SSL certificate is invalid but we proceeded anyway

[screenshot https_service]

Nothing much except a static page with bricks, checking the page source

[screenshot https_pagesource]

We got a hint that this is a wordpress site, so we'd be using the `wpscan` tool but before that; I love being exhaustive with enumeration so i decided to run a nmap vuln scan first

[screenshot https_vulnscan]

This further ascertain the originality of the site being built on Wordpress and got some leads as well such as login page `/wp-login.php`, possible usernname `administrator`, Database login `/phpmyadmin` etc.

[screenshot wp-login]

[screenshot phpmyadmin]

Proceeding with `wpscan` 

```bash
┌──(sixth-sensei㉿senseisec)-[~/THM/Bricks]
└─$ wpscan --url https://bricks.thm --disable-tls-checks
```
_NB:_ I added `--disable-tls-checks` flag so it doesn't verify the SSL certificate because it gave an error when i tried it at first without the flag

[screenshot bricks_theme-vulnerable]

The scan highlighted the wordpress theme in use as `bricks` and its version `1.9.5` which appears vulnerable.

Performing a quick google search on "Bricks wordpress theme vulnerability"

[screenshot vulnerability_search]

We found an exploit that it's vulnerable to `CVE-2024-25600`, cloning this to our machine using `git clone https://github.com/Tornad0007/CVE-2024-25600-Bricks-Builder-plugin-for-WordPress.git` and installing the requirements specified in the `README.md` file.

[screenshot bricks_exploit]

## Foothold
Launching the exploit

[screenshot shell]

Boom(blown emoji)! we've regained access to the system which completes 50% of the task. The current shell we got doesn't allow us change directory, so we have to spawn a bash shell and catch it with netcat

[screenshot standard_shell]

As you can see, we're in the `www` directory. Listing the directory contents

[screenshot dir_listing]

We can see a lot of the web files but we're particular about the txt file in the web folder and the only unusual file is `650c844110baced87e1606453b93f22a.txt`; cat-ing into this file answers the first question.

[screenshot first_flag]

There's also a file of interest here `wp-config.php`, looking at its contents

[screenshot phpmyadmin_creds]

We got the database admin details but since our main focus is evidence of attack and not to escalate as root, we'd proceed.

## Linux Forensics
We're to find a suspicious process affiliated with a running service, listing all active services running on the server `systemctl list-units --type=service --state=running`

[screenshot suspicious_service]

While scrolling through the services list, found the suspicious one `ubuntu.service` but how do we get the process associated? (thinking emoji)

If you guessed looking at the service status then you're right

[screenshot service_status]

Voila! we can see the suspicious process associated with the service and also its location.

Navigating to its location

[screenshot service_dir]

We see lots of files including a configuration file for the `inet-manager` process; let's check it out

[screenshot service_logfile]

_NB:_ I used `head` command to view this file because using `cat` occupied the whole terminal and missing the important information.

From the contents of this file, we can conclude its a log dump for a bitcoin miner being masked as `ubuntu.service` 

- The remaining part of the task asked us to fetch the wallet address, we see an ID at the beginning of the log file and from my little knowledge of crypto; a wallet address can't be that lenghty. Fed the ID to `cyberchef` and it automatically detect the encoded content

[screenshot cyberchef]

Carefully looking at this output, I noticed a repetition after the highlighted parts below

[screenshot wallet_address]

Supplying this as an answer to the wallet address question gave a success.

- What's left is to determine the APT group associated with this wallet; starting with a blockchain explorer search

[screenshot blockchain_explorer]

Looking at the transaction history and researching the address that has sent to and received from this address one after the other

[screenshot blockchain_transactions]

The address `bc1q5jqgm7nvrhaw2rh2vk0dk8e4gg5g373g0vz07r` from the list proved to be a lead

[screenshot lockbit]

Now we know this address is associated with the **Lockbit group**

That's it, its a wrap (rocket emoji)

***
I had an amazing time with this challenge and learnt more on linux forensics, I hope you enjoyed it as much as i did.
***

<br>

**See you again 👋🏽**


<button onclick="window.location.href='https://sixth-sensei.github.io';">Back To Home 🏠</button>









