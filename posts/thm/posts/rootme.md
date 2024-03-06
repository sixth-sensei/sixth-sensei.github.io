# RootMe

***
![RootMe](https://tryhackme-images.s3.amazonaws.com/room-icons/11d59cb34397e986062eb515f4d32421.png)

### Difficulty: Easy

***

## Nmap Scan Result

Running nmap scan on the machine, we have

```bash
┌──(sixth-sensei㉿kali)-[~/THM/RootMe]
└─$ #sudo nmap -sS -sV 10.10.69.57
#Starting Nmap 7.94 ( https://nmap.org ) at 2024-03-06 14:38 WAT
Nmap scan report for 10.10.69.57
Host is up (0.57s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.98 seconds

```

We have two open ports; Navigating to the web server, we're greeted with a static page

![rootme_static](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/b1db2954-ea22-488e-951b-a2392bf45f54)

Viewing the page source, nothing comes up as well; something must be hidden right?

![pagesource_rootme](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/dd8873ec-b1dc-4318-826a-72997bb4ab26)

## Enumeration

Ran gobuster against the web server and found few hidden directories

![gobuster](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/d5b09084-65bb-40e8-93be-59f5852f27d3)

Navigated them one after the other and saw an upload page in `/panel`, interesting 😉.

![upload_dir](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/425ae9f8-5f82-400d-84d9-e2927e011bf5)

## Privilege Escalation
Uploaded a python reverse shell but couldn't gain shell with it, my guess is python wasn't supported so i tried php but `.php` was also restricted then i tried `.phtml` which uploaded successfully.

![upload](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/808dd4d8-fea7-4546-92b2-5fabf719bc14)

![reverse_shell](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/e2b2d371-c177-4864-af3b-16736802f632)

listened with netcat on my specified port 8888, navigated to the uploaded php shell filepath and it gave me a shell as `www-data`.

![standard_shell](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/ae9ab780-41c5-444e-83fb-3ef76c1a5f58)

Navigated around the file system to see a way i could find the `user.txt` flag but didn't find it 🫠, so i decided to use `find / -type f -name user.txt 2> /dev/null` and we have

![usertxt](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/cbb1f12c-6c53-4d1f-a93f-3a9ca02e7ab5)

_a little explanation_

- -type f: asking find command to look for files only
- -name user.txt: asking the find command to search for a file with name "user.txt"
- 2> /dev/null: for error messages to not show as part of the results

cat-ing `/var/www/user.txt` to get the user flag and boom, we have it!

![userflag](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/6f1e2052-05a9-4b0b-8214-6600330942f5)

To get root, i searched for files with SUID permission that can be used to escalate privilege as root

![findSUID](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/2ce5fbc5-671e-41ba-b8e4-773d34d499b7)

From my little knowledge of file system; it is unusual to see the python binary `/usr/bin/python` have an SUID bit, so this is a bit weird but to our advantage as well. Enumerated for the binary on `gtfobins` and I have this

![gtfobins](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/89a49dff-fdc5-4740-849a-dc3c82e4c2b4)

Executing the second command as seen in the screenshot in our shell without the `./` and we're root!!

![rootshell](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/3027f30b-4ad3-4493-904e-abdf9d7b87e3)

Repeating the same find command used earlier for the user flag to find the root flag

![find_rootflag](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/5dd4eb82-912f-409d-82eb-8ca6c46d9896)

yaay! got the flag 🚀

![rootflag](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/5e2acbd2-f7ac-49d5-99fd-fc57a2f39448)


## Questions

1. Scan the machine, how many ports are open? - **2**
2. What version of Apache is running? - **2.4.29**
3. What service is running on port 22? - **ssh**
4. What is the hidden directory? - **/panel/**
5. user.txt - **THM{y0u_g0t_a_sh3ll}**
6. Search for files with SUID permission, which file is weird? - **/usr/bin/python**
7. root.txt - **THM{pr1v1l3g3_3sc4l4t10n}**

<br>

**See you again 👋🏽**


<button onclick="window.location.href='https://sixth-sensei.github.io';">Back To Home 🏠</button>
