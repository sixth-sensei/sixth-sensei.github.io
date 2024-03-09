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

![staticsite](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/9761a4b5-ee71-4b56-86ab-9bbaed45e074)

From webpage, we need a codename to access the site and there's a hint `user-agent` which is an http header. Firing up burpsuite, let's try R as agent; we have this

![agentR](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/70f68919-438e-498d-9d78-d95ea65942a4)


![agentR_error](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/c1e6a681-e390-40f8-a059-f46ad88e1561)

From the response gotten, it is evident the other 25 employees are alphabets; we have to keep switching the agents till we have the right one but doing this one by one is streesful right? That's where burp intruder comes in, setting alphabets A to Z as payload and attacking; we have

![intruder_start](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/8d429d95-dd6c-4435-9e3e-43cb887e451d)


![intruder](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/ad910b99-5157-403a-908f-e28b0a477c24)

Only `C` returns a 302 reditect code, setting it as user-agent; we got redirected to `agent_C_attention.php` and agent name is **chris**

![agentC](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/c43d23d8-3a3b-46a2-9502-d9dae1769d62)

#### Port 21

From the page, agent R pointed out agent C's weak password needing change; trying hydra to bruteforce the ftp service

![agentC_ftp](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/12f96ac6-78da-4824-9a8c-826721b4a9a0)

yaay! we have the ftp password. Let's access the service

![ftp_login](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/71feadb1-b8d3-4f37-a4ed-b006dabd421b)

Listing the directory content, we see the message to agent J that agent R mentioned earlier; let's download all the files in the directory using `mget *`

![mget](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/27e31cec-26f3-4801-b680-df1a210ed43b)

reading the message to agent J

![agenJ_message](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/0dbaa897-58b1-4d6d-8ab8-a8579ac292e3)

we got hint that agent J's password is embedded in one of the pictures in C's directory. Running `stegseek` on the two pictures `cutie.png` and `cutie-alien.jpg`

![stegseek](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/38f74ffe-dbb4-4581-8154-e0c10554b271)

voila, we found the image with embedded file to be `cutie-alien.jpg` and extracted it's content. let's look into that

![agentJ](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/a75a0784-6765-4f73-bc0b-65b6ef3a870b)

we can see agent J's name as `james` and his password as `hackerrules!`, let's access his account

![agentJ_ftp](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/b3ee3d68-4ab8-4167-b2a5-cba107c8e640)

Looks like agent J's credentials isn't for ftp but for ssh since it's the onle service left.

***

From the questions, there should be a zip file; checking `cutie.png` again since we got nothing from it earlier but this time with `exiftool`

![exiftool](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/871f6bdd-587d-4fdd-875a-239d2f0e69cc)

There's a warning _Trailer data after PNG_ which means it has something in it, running `strings` on it

![strings_start](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/f3812821-a322-4c24-a6b9-d3a50110fbf0)


![strings_end](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/a4046409-d0ff-435f-ac5f-f49a72f77ca7)

Nice, we see a `To_agentR.txt` file embedded. we can also do this using `xxd` to dump the file in hex format. But we still can't find the zip file, there's one last tool we can use, `binwalk`

_a little explanation_

- Binwalk is a tool that allows you to search binary images for embedded files and executable code.

let's try it out

![binwalk](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/384a344c-0c24-4c21-983a-c3b3c9f1ef4c)

Yes!! there is our zip file, let's extract it using `binwalk -e cutie.png`

![zipfile](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/4baf4be0-9955-48a0-8135-dd08acc5ef5b)

Was unable to read the message to agent R because the zip file is passworded. Don't panic, we can use `zip2john` to make it a john compatible hash and crack it using the `johntheripper`

![zip2john](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/23990d4c-c4f2-49ff-82f1-2361db2304d7)


![john](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/074fbbaf-0bce-468c-a612-83ab29ed475c)

Nice! we have the zip file password as `alien`, let's see the content of the message to agent R

![agenR_message](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/545dfa99-7fd8-45ae-8520-10e7a44a96ac)

we see an encoded message `QXJlYTUx`, using `dcode.fr` found it to be a ROT cipher and cracking it with cyber chef; i got `")y=*%&I`. Don't know if we'll need it later but let's keep it.

![cyberchef](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/9b0b45e8-ce37-45ec-98f5-197e1650919e)

#### Port 22

Logging in via ssh, we got access to agent J's account

![ssh_james](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/cee330ca-e56e-49d8-aa7c-772f79ccad2c)

Listing the directory contents, we see our user flag

![dir_james](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/9bcb332a-4234-4500-9075-db2e5cde718c)


![userflag](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/4c6d734e-7fc8-4ef0-88ae-f816b5446990)

We can see another image file `Alien_autopsy.jpg` in this directory, let's download it on our machine by serving the directory with `http.server`

![alien_image](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/eb73ee11-1e6a-4011-b616-44970e8a802d)

Opening the image

![alien](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/436ec9da-0f58-410b-a42d-ae472b0facbc)

There's nothing we can get visually and from the question we need to know the incident that happened, performing reverse image search according to the hint; we have

![reverse_image](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/f7a73927-385d-4ef8-a549-81ca3c5d96b4)

clicking on one of the link in the results and we have the incident name as **Roswell alien autopsy**

![alien_incident](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/dd8cceed-1117-4272-adef-110d0940acde)

## Privilege Escalation

Now let's get root, running `sudo -l` to see the sudo privileges agent J has

![sudo_james](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/4aaad30f-2e08-484b-85a5-320254053e8f)

Interesting 🤠!! agent J has `(ALL, !root) /bin/bash` privilege, looking this up; we got an exploit with CVE entry **CVE-2019-1487** on exploitdb

![Uploading exploitdb.png…]()
[screenshot exploitdb]

checking through the exploit code, we can run `sudo -u#-1 /bin/bash` to get root

[screenshot root]
![Uploading root.png…]()

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
