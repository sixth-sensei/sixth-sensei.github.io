# RootMe

### Difficulty: Easy

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

[screenshot rootme_static]

Viewing the page source, nothing comes up as well; something must be hidden right?

[screenshot pagesource-rootme]

## Enumeration

Ran gobuster against the web server and found few hidden directories

[screenshot gobuster]

Navigated them one after the other and saw an upload page in `/panel`, interesting.


## Privilege Escalation
Uploaded a python reverse shell but couldn't gain shell with it, my guess is python wasn't supported so i tried php but `.php` was also restricted then i tried `.phtml` which uploaded successfully.

[screenshot upload]

[screenshot reverse_shell]

listened with netcat on my specified port 8888, navigated to the uploaded php shell filepath and it gave me a shell as `www-data`.

[screenshot standard_shell]



Navigated around the file system to see a way i could find the `user.txt` flag but didn't find it 🫠, so i decided to use `find / -type f -name user.txt 2> /dev/null` and we have

[screenshot usertxt]

_a little explanation_

- -type f: asking find command to look for files only
- -name user.txt: asking the find command to search for a file with name "user.txt"
- 2> /dev/null: for error messages to not show as part of the results



cat-ing `/var/www/user.txt` to get the user flag and boom, we have it!

[screenshot userflag]

To get root, i searched for files with SUID permission that can be used to escalate privilege as root

[screenshot findSUID]

From my little knowledge of file system; it is unusual to see the python binary `/usr/bin/python` have an SUID bit, so this is a bit weird but to our advantage as well. Enumerated for the binary on `gtfobins` and I have this

[screenshot gtfobins]

Executing the second command in our shell without the `./` and we're root!!

[screenshot rootshell]

Repeating the same find command used earlier for the user flag to find the root flag

[screenshot find_rootflag]

yaay! got the flag 🚀

[screenshot rootflag]

## Questions

1. Scan the machine, how many ports are open? - 2
2. What version of Apache is running? - 2.4.29
3. What service is running on port 22? - ssh
4. What is the hidden directory? - /panel/
5. user.txt - THM{y0u_g0t_a_sh3ll}
6. Search for files with SUID permission, which file is weird? - /usr/bin/python
7. root.txt - THM{pr1v1l3g3_3sc4l4t10n}

<br>

**See you again 👋🏽**

<button onclick="window.location.href='https://sixth-sensei.github.io';">Back To Home 🏠</button>
