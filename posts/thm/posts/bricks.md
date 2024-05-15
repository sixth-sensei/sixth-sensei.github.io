# TryHack3M: Bricks Heist

***
![Bricks](https://tryhackme-images.s3.amazonaws.com/room-icons/0a46e92e8a9255f7dde294569e05dae1.png)

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

![http](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/924aa23a-1ce8-43a9-ad4d-f377301196e2)

Met with an error, so we'll proceed with the secure version

### Port 443
Navigating to `https://bricks.thm`, it is observed that the SSL certificate is invalid but we proceeded anyway

![https_service](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/a1d0a5b4-07fa-4444-939a-477b25b16b54)

Nothing much except a static page with bricks, checking the page source

![page_source](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/1a9b0d4e-912a-43f6-b089-896ca67fb6a7)

We got a hint that this is a wordpress site, so we'd be using the `wpscan` tool but before that; I love being exhaustive with enumeration so i decided to run an nmap vuln scan first

![https_vulnscan](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/a1927d9a-884f-4269-987f-3ed13b164e4e)

This further ascertain the originality of the site being built on Wordpress and got some leads as well such as login page `/wp-login.php`, possible username `administrator`, Database login `/phpmyadmin` etc.

![wp-login](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/af81c515-27cb-4582-a7b4-489c73cbca4d)

![phpmyadmin](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/bc647c18-94a1-4698-ba11-5da25b4f83d4)

Proceeding with `wpscan` 

```bash
┌──(sixth-sensei㉿senseisec)-[~/THM/Bricks]
└─$ wpscan --url https://bricks.thm --disable-tls-checks
```
_NB:_ I added `--disable-tls-checks` switch so it doesn't verify the SSL certificate because it gave an error when i tried it at first without the switch

![bricks_theme-vulnerable](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/92b30230-6146-4b34-b7e5-22987946e9ba)

The scan highlighted the wordpress theme in use as `bricks` and its version `1.9.5` which appears vulnerable.

Performing a quick google search on "Bricks wordpress theme vulnerability"

![vulnerability_search](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/f6360679-d922-4062-937e-bf63562a299e)

I found an exploit that it's vulnerable to - `CVE-2024-25600`.

![bricks_exploit](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/e7a16cd7-f949-4ab5-9794-c48aef674e4a)

Cloning this to our machine using `git clone https://github.com/Tornad0007/CVE-2024-25600-Bricks-Builder-plugin-for-WordPress.git` and installing the requirements specified in the `README.md` file.

## Foothold
Launching the exploit

![shell](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/cd647cb5-a074-4cb3-8391-6763207bf7c8)

Boom 🤯! we've regained access to the server which completes 50% of the task. The current shell we got doesn't allow us to change directory, so we have to spawn a bash shell and catch it with netcat

![standard_shell](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/f6cc3193-c40b-4576-93e9-3468b11a2df7)

As you can see, we're in the `www/default` directory. Listing the directory contents

![dir_listing](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/54e2bfd5-eb8d-4bd0-8dce-65bcce7ce042)

We can see a lot of the web files but we're particular about the txt file in the web folder and the only unusual file is `650c844110baced87e1606453b93f22a.txt`; cat-ing into this file answers the first question.

![first_flag](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/e09bc52b-f72b-4c62-95a2-b30625c39d54)

There's also a file of interest here `wp-config.php`, looking at its contents

![phpmyadmin_creds](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/08ce4ac4-e7bb-4614-b150-c520704e6173)

We got the database admin details but since our main focus is evidence of attack and not to escalate as root, we'd proceed.

## Linux Forensics
![detective mode](https://pbs.twimg.com/media/DXQJUpZXUAI680W.jpg)

We're to find a suspicious process affiliated with a running service, listing all active services running on the server `systemctl list-units --type=service --state=running`

![suspicious_service](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/33ed7802-ae87-48f3-b61f-95eed25b5108)

While scrolling through the services list, found the suspicious one `ubuntu.service` with name TRYHACK3M; but how do we get the process associated? 🤔

If you guessed looking at the service status then you're right

![service_status](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/bbb88c5c-3197-44d5-91e8-e1785607191b)

Voila! we can see the suspicious process associated with the service as `nm-inet-dialog` and also its location.

Navigating to its location

![service_dir](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/6153ed4a-8016-4aaf-a3b3-2758398abd95)

We see lots of files including a configuration file for the `inet` process; let's check it out

![service_logfile](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/729bdd8b-5ca9-43cb-9c8a-dfdc0b1a9723)

_NB:_ I used `head` command to view this file because using `cat` occupied the whole terminal and missing the important information.

From the contents of this file, we can conclude its a log dump for a bitcoin miner being masked as `ubuntu.service` 

- The remaining part of the task asked us to fetch the wallet address, we see an ID at the beginning of the log file and from my little knowledge of crypto; a wallet address can't be that lenghty. Fed the ID to `cyberchef` and it automatically detect the encoded content

![cyberchef](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/6470cfc7-d7eb-47a7-9c22-54f5cf2a347a)

Carefully looking at this output, I noticed a repetition after the highlighted parts below

![wallet_address](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/b72d9ad0-9ee7-40eb-8b2a-07ceef4e8e2d)

Supplying this as an answer to the wallet address question gave a green ✅

- What's left is to determine the APT group associated with this wallet; starting with a blockchain explorer search

![blockcahin_explorer](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/600fe642-9009-4a82-811b-f62eba7b7b0f)

Looking at the transaction history and researching the address that has sent to and received from this address one after the other

![blockchain_transactions](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/4fbc2c36-9d5e-4688-8bad-73acd642d5a2)

The address `bc1q5jqgm7nvrhaw2rh2vk0dk8e4gg5g373g0vz07r` from the list proved to be a lead

![lockbit](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/ad627b67-c50f-48a3-9121-248b5a4a5541)


Now we know this address is associated with the **Lockbit group**

That's it, its a wrap ⭐

***
- I had an amazing time with this challenge and learnt more on linux forensics, I hope you enjoyed it as much as i did 🤓.
***

<br>

**See you again 👋🏽**


<button onclick="window.location.href='https://sixth-sensei.github.io';">Back To Home 🏠</button>









