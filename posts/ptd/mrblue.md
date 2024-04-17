# Mr. Blue

***


### Difficulty: Easy

***

## Nmap Scan Result

Running nmap scan on the machine, we have

```bash
                                                                             ┌──(sixth-sensei㉿kali)-[~/PwnTillDawn/10.150.150.242]
└─$ sudo nmap -sS -sV 10.150.150.242
Starting Nmap 7.94 ( https://nmap.org ) at 2024-04-15 17:45 WAT
Nmap scan report for 10.150.150.242
Host is up (0.22s latency).
Not shown: 985 closed tcp ports (reset)
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Microsoft DNS 6.1.7601 (1DB1446A) (Windows Server 2008 R2 SP1)
80/tcp    open  http          Microsoft IIS httpd 7.5
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds  Microsoft Windows Server 2008 R2 - 2012 microsoft-ds (workgroup: WORKGROUP)
1433/tcp  open  ms-sql-s      Microsoft SQL Server 2012 11.00.2100; RTM
3389/tcp  open  ms-wbt-server Microsoft Terminal Service
8089/tcp  open  ssl/http      Splunkd httpd
49152/tcp open  msrpc         Microsoft Windows RPC
49153/tcp open  msrpc         Microsoft Windows RPC
49154/tcp open  msrpc         Microsoft Windows RPC
49155/tcp open  msrpc         Microsoft Windows RPC
49156/tcp open  msrpc         Microsoft Windows RPC
49157/tcp open  msrpc         Microsoft Windows RPC
49158/tcp open  msrpc         Microsoft Windows RPC
Service Info: Host: MRBLUE; OS: Windows; CPE: cpe:/o:microsoft:windows_server_2008:r2:sp1, cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 80.47 seconds    

```

## Enumeration

This is a windows machine as we can see and there are lots of open ports, starting with port 80 which is the web service
**Port 80**
We're greeted with this page

[screnshot port_80]

checking the page source, we can see an hint of the CVE this service is vulnerable to `SMB Remote Code Execution`

[screenshot page_source]

`MS17-010` popularly known as `Mr Blue`. At this point, if you're familiar with metasploit; `Eternal Blue` exploit should automatically come to your mind but I won't be using metasploit, let's go manually [wink]

**Port 445**
Looking `MS17-010` up on exploitdb, we have numerous options

[screenshot exploitdb]

Tried the manual exploits from exploitdb but they were giving one error or the other, decided to search the web and found `Win7Blue` exploit on github, let's clone it to our machine

[screenshot Win7Blue]

[screenshot dir_win7blue]

Let's make the script executable 

[screenshot chmod]

[screenshot ren]

_NB:_ I had to rename the script and add the `.sh` extension


## Foothold

Running the exploit `bash Win7Blue.sh`

[sreenshot exploit]

There is a need to determine the architecture of our target system and we can do that using `crackmapexec`

```bash
┌──(sixth-sensei㉿kali)-[~]
└─$ crackmapexec smb 10.150.150.242
SMB         10.150.150.242  445    MRBLUE           [*] Windows 6.1 Build 7601 x64 (name:MRBLUE) (domain:MrBlue) (signing:False) (SMBv1:False)

```
As we can see, our target is running `x64` architecture, let's carry on with our exploit

[screenshot host_info]

_a little clearance_

- RHOST: The IP address of our target machine
- LHOST: The IP address of attacking (our) machine, we're using the tun0 address gotten from PwnTillDawn server
- LPORT: The port we will like to listen on

Let's set up a listener to catch the connection

```bash
┌──(sixth-sensei㉿kali)-[~]
└─$ nc -lnvp 7979
```
Exploiting [meme]

we're in!!!

[screenshot shell]

Let's find the flag [flag emoji]

[screenshot flag]

Yaay, that's it.


<br>

**See you again 👋🏽**


<button onclick="window.location.href='https://sixth-sensei.github.io';">Back To Home 🏠</button>

