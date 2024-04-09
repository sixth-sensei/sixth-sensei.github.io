# Opacity

***
![Opacity](https://tryhackme-images.s3.amazonaws.com/room-icons/328c078f7c5695439a46ba90ae48aaa0.png)

### Difficulty: Easy

***

## Nmap Scan Result

Running nmap scan on the machine, we have

```bash                                                                                 
┌──(sixth-sensei㉿kali)-[~/THM/Opacity]
└─$ #sudo nmap -sS -sCV 10.10.50.116
Starting Nmap 7.94 ( https://nmap.org ) at 2024-04-06 13:17 WAT
Nmap scan report for 10.10.50.116
Host is up (0.15s latency).
Not shown: 996 closed tcp ports (reset)
PORT    STATE SERVICE     VERSION
22/tcp  open  ssh         OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 0f:ee:29:10:d9:8e:8c:53:e6:4d:e3:67:0c:6e:be:e3 (RSA)
|   256 95:42:cd:fc:71:27:99:39:2d:00:49:ad:1b:e4:cf:0e (ECDSA)
|_  256 ed:fe:9c:94:ca:9c:08:6f:f2:5c:a6:cf:4d:3c:8e:5b (ED25519)
80/tcp  open  http        Apache httpd 2.4.41 ((Ubuntu))
| http-title: Login
|_Requested resource was login.php
|_http-server-header: Apache/2.4.41 (Ubuntu)
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
139/tcp open  netbios-ssn Samba smbd 4.6.2
445/tcp open  netbios-ssn Samba smbd 4.6.2
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: -3s
|_nbstat: NetBIOS name: OPACITY, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb2-time: 
|   date: 2024-04-06T12:17:37
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 35.71 seconds

```

## Enumeration
we have 4 open ports, starting with the web service
**Port 80**
We're greeted with a login page and from the page source, not much information is gotten except for the Apache verion running discovered from nmpa earlier.

![login_page](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/5957a736-269e-4da9-9694-4656a8f69d9b)

Let's fuzz for hidden directories

![gobuster_start](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/5a0af5ad-81fb-4b00-b816-c61c15eaee74)

![gobuster_end](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/49ea6f31-1efa-4cae-9cd9-51fe8b397f2d)

Found some hidden gems and checking them one after the other, i stumbled upon a file upload page in the `/cloud` endpoint

![cloud](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/0ed029d4-67cd-42af-a8e4-722c089f25f1)

It provided a URL to upload an image, let's see if we can poison with a reverse shell code. Tried serving the reverse shell code with `python http.server` but the form was rejecting it until i added a `#.png` to the end of the URL and that worked since it accepts only image files

_NB:_ other options that worked are `#.jpg`, ` .png` and ` .jpg`. so you can try them

![shell_image_upload](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/d466356d-22c7-4e34-a00d-d6180847115a)

![upload_successful](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/68dbd4b8-0fac-4f6c-a29f-6ddb9a450e39)

As we can from the terminal, the `http 200` code indicating the file was downloaded successfully from the local server.

## Foothold

After a successful upload, we immediately got an shell. yaay 

![shell](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/f8c5d0b4-0dbd-49c5-aeef-00054c554330)

navigating the file system to see what we can find, found a `dataset.kdbx` file in the `/opt` directory. A quick search of `.kdbx` extension shows that it is a KeePass database.

![opt](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/e933fc75-877d-4055-93b6-6154b63db2d5)

Let's try to get this file on our machine using netcat, learnt this trick from [@DeusX](https://twitter.com/deusx_45) on twitter sometime ago.

![dataset_transfer](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/05c01de4-7bfe-43e8-acd8-0fc3aa84e8da)

_a little explanation_

- `nc -lnvp [port] < file_to_transfer` from sender machine, initiates an open connection on specified port and serve a certain file
- `nc [sender IP] [port] > file_to_receive` from receiver's machine, connects to senders machine and downloads the file.

Couldn't find a way to open the file, so i had to download the KeePass app. `apt install keepassx` if you need to install it too.

Looking into the database file, it is encrypted and we need a password to access it. 

![database_locked](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/a149eab9-c0ff-4498-a5a1-5e3fbe350957)

There is a `KeePass2john` tool that can be used to convert it to a crackable john file.

![keepass2john](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/b5e31a96-72ef-4b82-97d0-c76fc60c85eb)

cracking this, we got the password to the dataset

![cracked_dataset](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/9d9b0872-61fa-4661-810d-a7aab77e22d8)

Accessing the file with the cracked password, we have this

![dataset_unlocked](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/adc160c5-4975-4763-b757-7fd7e4d4c35d)

## Privilege Escalation

Using the credentials gotten from the dataset, we can ssh into the machine and elevate our privilege horizontally from `www-data` to `sysadmin`

![ssh_sysadmin](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/bf011dec-1ecf-4459-a7cf-e0214063e4f8)

Listing the directory contents and right there we have our first flag `local.txt`

![first_flag](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/54ba9d64-5a32-4caf-bd87-f3ab02e995f0)

Let's find a path to escalate our privilege vertically and gain root, tried running `sudo -l` but `sysadmin` has no sudo privileges

![sudo_l](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/a7317620-dabc-4c08-a8de-af127a6a3d7f)

***
![moments later](https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Fi.pinimg.com%2F736x%2Fe8%2F02%2Fa0%2Fe802a0d5ac1839d9f84c5fc82e9fd00c--song-quotes-pins.jpg&f=1&nofb=1&ipt=f665d4564c02b457faa10d52caec1bd1ea0c090aee800ccfa23576b1c583d87f&ipo=images)

After exhausting all priv esc techniques, decided to look meticulously in the `sysadmin` folder and saw a scripts directory containing `script.php` belonging to root.

![script_folder](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/69177ea7-7001-466d-8f8b-bcf744ddd0c0)

looking into the script, it is a backup of the sysadmin folder and it requires a `backup.inc.php` file located in lib

![scriptphp](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/255b1d5a-cd8b-4e7b-92a0-f3782ad37fc9)

The `sysadmin` user owns this lib folder but belonging to root group; let's take full ownership and then we can create a reverse shell that we can name `backup.inc.php` 😉

![chown_lib](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/fde685a2-2fe6-4145-abe8-b62510024f87)

Creating the revrse shell and renaming it as `backup.in.php`; since this file is run and required by `root`. Next time the backup script is executed, it'll drop us into a root shell by listening with `netcat` on our chosen port

![root_escalation](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/33404b0c-2bf5-4df1-846b-f80ffa2857a9)

Boom!! we're root; let get our final flag `proof.txt`

![rootflag](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/21e60876-0fc4-44d2-ad57-3ae1f6f977fc)

This was interesting and i learnt new things as well, i hope you enjoyed every bit of it too 🤠


## Questions

1. What is local.txt flag? - 66**********************e2
2. What is proof.txt flag? - ac*********************0e

<br>

**See you again 👋🏽**

![salute](https://external-content.duckduckgo.com/iu/?u=http%3A%2F%2Fmedia.tumblr.com%2F9ab4e593f1efbf5c73a734cbd8ea77fb%2Ftumblr_inline_mxg3ikkpCz1qgctoq.gif&f=1&nofb=1&ipt=59ac8f5dce51a776cc0f9712c01c6ab5a1ea3e7f3b2977146601dc885b45fdb0&ipo=images)


<button onclick="window.location.href='https://sixth-sensei.github.io';">Back To Home 🏠</button>

