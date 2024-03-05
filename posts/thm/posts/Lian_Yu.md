# Lian_YU

***
![Lian_Yu](https://tryhackme-images.s3.amazonaws.com/room-icons/c72d580db69a726dfb8da8aa6eaa2f5a.jpeg)

### Difficulty: Easy

***

## Nmap Scan Result

```bash
#nmap -A -T 4 -p- 10.10.70.36 > nmap_scan: Starting Nmap 7.94 ( https://nmap.org ) at 2024-03-03 07:14 WAT
Nmap scan report for 10.10.70.36
Host is up (0.17s latency).
Not shown: 65530 closed tcp ports (reset)
PORT      STATE SERVICE VERSION
21/tcp    open  ftp     vsftpd 3.0.2
22/tcp    open  ssh     OpenSSH 6.7p1 Debian 5+deb8u8 (protocol 2.0)
| ssh-hostkey: 
|   1024 56:50:bd:11:ef:d4:ac:56:32:c3:ee:73:3e:de:87:f4 (DSA)
|   2048 39:6f:3a:9c:b6:2d:ad:0c:d8:6d:be:77:13:07:25:d6 (RSA)
|   256 a6:69:96:d7:6d:61:27:96:7e:bb:9f:83:60:1b:52:12 (ECDSA)
|_  256 3f:43:76:75:a8:5a:a6:cd:33:b0:66:42:04:91:fe:a0 (ED25519)
80/tcp    open  http    Apache httpd
|_http-server-header: Apache
|_http-title: Purgatory
111/tcp   open  rpcbind 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100024  1          36562/udp6  status
|   100024  1          48790/tcp   status
|   100024  1          56203/tcp6  status
|_  100024  1          56797/udp   status
48790/tcp open  status  1 (RPC #100024)
Device type: general purpose
Running: Linux 5.X
OS CPE: cpe:/o:linux:linux_kernel:5.4
OS details: Linux 5.4
Network Distance: 2 hops
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 554/tcp)
HOP RTT       ADDRESS
1   172.50 ms 10.8.0.1
2   172.77 ms 10.10.70.36

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 1247.99 seconds

```

## Enumeration

Using gobuster to enumerate the http server for hidden directories, found `/island` but the question hint specifies this hidden directory is in numbers so i'll have to enumerate further.

![island_dir](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/bb79f44e-63d6-4205-abd8-79a706c5d758)

I saw what might be a possible username **vigilante** in this page-source. Decided to run gobuster against this new discovered endpoint. Found another hidden directory `2100` which looks like our answer.

![gobuster_island](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/54640870-1934-40e2-8db9-a0cd47192d0c)


![2100_dir](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/4f29efa2-eb0b-4882-b25e-32207da4a382)

There's a `.ticket` clue in the page source, enumerating this endpoint again specifying `ticket` as file extension.

![ticket_start](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/d1e0acd0-de7c-44c5-9b44-f8a389deeb68)

Found the ticket to reaching Lian Yu `green_arrow.ticket` which doubles as the filename requested in the question 2.

![ticket_extension](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/7a415139-9f97-4e3d-857d-ac5693adfd66)

I have the token `RTy8yhBQdscX` that looks like a password to the username found earlier which should be a combination to accessing the ftp server found earlier in nmap. The password is encoded because i got an error after supplying it in the question, so i used `decode.fr` to know what format it is; found it to be a `base58` encoding then used cyberchef to crack it and got `!#th3h00d`.

![cyberchef](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/f5d2e16c-9d9f-43c4-a6b4-d7f16eb32a94)

Decided to access the services we enumerated earlier in nmap with the username and password we have. First on the list is `ftp`

![ftp_login](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/1cf967cd-4ac1-40a9-b8c0-38c486d83868)

voila 🤓! the first service gave in, walked around the ftp directory to see what i can find. Downloaded all the files in the directory and found possible ssh username **slade** in the `.other_user` file. Found one of the images in the directory to have an embedded file; extracting with stegseek, I got a zip file `ss.zip` and in it a file named shado containing `M3tahuman` which seems like the ssh password.

![stegseek](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/377039b4-6d6c-47fe-b7cb-e3da4ec3c387)

using the credential gotten, tried to login via ssh and it's successful 😉. Finally found my way to Lian_Yu!!

![Lian_Yu](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/2bd3224c-7444-4e22-a5fd-a95737dc35fe) 

Navigating the directory, i found the user flag `user.txt`

![userflag](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/63f2986a-7356-4d32-b59c-80e092fa6c12)

## Privilege Escalation

Ran `sudo -l` to know what sudo privileges **slade** has and i could see pkexec; 

![slade_sudo](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/64292f46-da06-45db-8598-d4e4539cce6a)

ran `sudo pkexec /bin/sh` gotten from enumerating for pkexec binary on `gtfobins` and i got root!

![gtfobins](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/2f82cd7f-51a3-43ce-81f6-c6107cc5de1f)

![root shell](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/0c1c3da0-f904-4c22-b87e-895211745d08)

checked the root directory to catch the root flag and boom, Done!!

![rootflag](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/68a8f22b-0c58-4c3f-98d1-3eaffab94642)



## Questions
1. What is the Web Directory you found? Ans: 2100
2. What is the file name you found? Ans: green_arrow.ticket
3. What is the FTP Password? Ans: !#th3h00d
4. What is the file name with SSH password? Ans: shado
5. user.txt? Ans: THM{P30P7E_K33P_53CRET5__C0MPUT3R5_D0N'T}
6. root.txt? Ans: THM{MY_W0RD_I5_MY_B0ND_IF_I_ACC3PT_YOUR_CONTRACT_THEN_IT_WILL_BE_COMPL3TED_OR_I'LL_BE_D34D}

<br>

**See you again 👋🏽**

<button onclick="window.location.href='https://sixth-sensei.github.io';">Back To Home 🏠</button>
