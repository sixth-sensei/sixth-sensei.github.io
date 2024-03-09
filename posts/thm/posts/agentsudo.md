# AgentSudo

***
![AgentSudo](https://tryhackme-images.s3.amazonaws.com/room-icons/aedc6b66c222e15ff740c282a0c3f44e.png)

### Difficulty: Easy

***

## Nmap Scan

Running nmap scan on the machine, we have

```bash
┌──(sixth-sensei㉿kali)-[~/THM/AgentSudo]
└─$ #sudo nmap -sS -sV 10.10.155.31
Starting Nmap 7.94 ( https://nmap.org ) at 2024-03-07 15:29 WAT
Nmap scan report for 10.10.155.31
Host is up (0.20s latency).
Not shown: 997 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 20.56 seconds

```
we have 3 open ports `ftp`, `http` and `ssh`. let's dive in!

## Enumeration

#### Port 80

Navigating to the http server, we're greeted with this page

[screenshot static site]

From webpage, we need a codename to access the site and there's a hint `user-agent` which is an http header. Firing up burpsuite, let's try R as agent; we have this

[screenshot agentR]

[screnshot agentR_error]

From the response gotten, it is evident the other 25 employees are alphabets; we have to keep switching the agents till we have the right one but doing this one by one is streesful right? That's where burp intruder comes in, setting alphabets A to Z as payload and attacking; we have

[screenshot intruder_start]

[screenshot intruder]

Only `C` returns a 302 reditect code, setting it as user-agent; we got redirected to `agent_C_attention.php` and agent name is **chris**

[screenshot agentC]

#### Port 21

From the page, agent R pointed out agent C's weak password needing change; trying hydra to bruteforce the ftp service

[screenshot agentC_ftp]

yaay! we have the ftp password. Let's access the service

[screenshot ftp_login]

Listing the directory content, we see the message to agent J that agent R mentioned earlier; let's download all the files in the directory using `mget *`

[screenshot mget]

reading the message to agent J

[screenshot agentJ]

we got hint that agent J's password is embedded in one of the pictures in C's directory. Running `stegseek` on the two pictures `cutie.png` and `cutie-alien.jpg`

[screenshot stegseek]

voila, we found the image with embedded file to be `cutie-alien.jpg` and extracted it's content. let's look into that

[screenshot agentJ]

we can see agent J's name as `james` and his password as `hackerrules!`, let's access his account

[screenshot agentJ_ftp]

Looks like agent J's credentials isn't for ftp but for ssh since it's the onle service left.

***

From the questions, there should be a zip file; checking `cutie.png` again since we got nothing from it earlier but this time with `exiftool`

[screenshot exiftool]

There's a warning _Trailer data after PNG_ which means it has something in it, running `strings` on it

[screenshot strings_start]

[screenshot strings_end]

Nice, we see a `To_agentR.txt` file embedded. we can also do this using `xxd` to dump the file in hex format. But we still can't find the zip file, there's one last tool we can use, `binwalk`

_a little explanation_

- Binwalk is a tool that allows you to search binary images for embedded files and executable code.

let's try it out

[screenshot binwalk]

Yes!! there is our zip file, let's extract it using `binwalk -e cutie.png`

[screenshot zipfile]

Was unable to read the message to agent R because the zip file is passworded. Don't panic, we can use `zip2john` to make it a john compatible hash and crack it using the `johntheripper`

[screenshot zip2john]

[screenshot john]

Nice! we have the zip file password as `alien`, let's see the content of the message to agent R

[screenshot agentR_message]

we see an encoded message `QXJlYTUx`, using `dcode.fr` found it to be a ROT cipher and cracking it with cyber chef; i got `")y=*%&I`. Don't know if we'll need it later but let's keep it.

[screenshot cyberchef]

#### Port 22

Logging in via ssh, we got access to agent J's account

[screenshot ssh_james]

Listing the directory contents, we see our user flag

[screenshot dir_james]

[screenshot userflag]

We can see another image file `Alien_autopsy.jpg` in this directory, let's download it on our machine by serving the directory with `http.server`

[screenshot alien_image]

Opening the image

[screenshot alien]

There's nothing we can get visually and from the question we need to know the incident that happened, performing reverse image search according to the hint; we have

[screenshot reverse_image]

clicking on one of the link in the results and we have the incident name as **Roswell alien autopsy**

[screenshot alien_incident]

## Privilege Escalation

Now let's get root, running `sudo -l` to see the sudo privileges agent J has

[screenshot james_sudo]

Interesting 🤠!! agent J has `(ALL, !root) /bin/bash` privilege, looking this up; we got an exploit with CVE entry **CVE-2019-1487** on exploitdb

[screenshot exploitdb]

checking through the exploit code, we can run `sudo -u#-1 /bin/bash` to get root

[screenshot root]

boom!! we got root; listing the directory, we have the root flag and agent R's name

[screenshot rootflag]

This was fun, i hope you enjoyed every bit of it too 🦾



## Questions

1.  How many open ports? - **2**
2.  How you redirect yourself to a secret page? - **user-agent**
3.  What is the agent name? - **chris**
4.  FTP password - **crystal**
5.  Zip file password - **alien**
6.  steg password - **Area51**
7.  Who is the other agent (in full name)? - **james**
8.  SSH password - **hackerrules!**
9.  What is the user flag? - **b03d975e8c92a7c04146cfa7a5a313c7**
10. What is the incident of the photo called? - **Roswell alien autopsy**
11. CVE number for the escalation - **CVE-2019-14287**
12. What is the root flag? - **b53a02f55b57d4439e3341834d70c062**
13. Who is Agent R? - **DesKel**

<br>

**See you again 👋🏽**


<button onclick="window.location.href='https://sixth-sensei.github.io';">Back To Home 🏠</button>
