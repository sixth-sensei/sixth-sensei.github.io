# Mr. Blue

***
![mrblue](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/fb9e9ef6-9491-46f0-bfe4-f5d7f1eb79fa)

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

![port_80](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/dfc4d972-14f3-482e-bb0c-0b43eb91a9f5)

checking the page source, we can see an hint of the CVE this service is vulnerable to `SMB Remote Code Execution`

![pagesource](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/fb6a1446-e4a9-466f-9b03-72bb15c826ff)

`MS17-010` popularly known as `Mr Blue`. At this point, if you're familiar with metasploit; `Eternal Blue` exploit should automatically come to your mind but I won't be using metasploit, let's go manually 😉. You're can use metasploit if you want

_Short note on MS17-010:_ EternalBlue is a computer exploit software developed by the U.S. National Security Agency. It is based on a vulnerability in Microsoft Windows that, at the time, could allow remote code execution if an attacker sends specially crafted messages to a Microsoft Server Message Block 1.0 (SMBv1) server

**Port 445 (SMB)**

Looking `MS17-010` up on exploitdb, we have numerous options

![exploit-db](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/2be0259d-2857-4227-97c9-8f7b510f30e1)

Tried the manual exploits from exploitdb but they were giving one error or the other, decided to search the web and found `Win7Blue` exploit on github, let's clone it to our machine

![Win7Blue_clone](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/034b3ae0-57ee-449a-9d80-cbd3d3111599)

![dir_win7blue](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/9e6a7c71-a59b-46d1-bc68-1cf354ee8ad5)

Let's make the Win7Blue script executable 

![chmod](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/4df2cf31-e996-4f98-82f1-4f95368a4fef)

![ren](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/6ac4b811-7020-4081-b4ed-6f8e78504c00)

_NB:_ I had to rename the script and add the `.sh` extension


## Foothold

Running the exploit `bash Win7Blue.sh`

![exploit](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/db03bdee-f740-47a4-86b4-85c9bc2cd862)

There is a need to determine the architecture of our target system and we can do that using `crackmapexec`

- crackmapexec is a Python script that can enumerate users, shares, hashes, and more in Windows/Active Directory environments.

```bash
┌──(sixth-sensei㉿kali)-[~]
└─$ crackmapexec smb 10.150.150.242
SMB         10.150.150.242  445    MRBLUE           [*] Windows 6.1 Build 7601 x64 (name:MRBLUE) (domain:MrBlue) (signing:False) (SMBv1:False)

```
As we can see, our target is running `x64` architecture, let's carry on with our exploit

![host_info](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/08c5b371-97d9-428f-bcf8-dbcbbcf011a4)

_a little clearance_

- RHOST: The IP address of our target machine
- LHOST: The IP address of attacking (our) machine, we're using the tun0 address gotten from PwnTillDawn server
- LPORT: The port we will like to listen on

Let's set up a listener to catch the connection

```bash
┌──(sixth-sensei㉿kali)-[~]
└─$ nc -lnvp 7979
```
![Exploiting](https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Fmedia.tenor.co%2Fimages%2Fd5da666a1dabd19856f4e911b2ed613e%2Fraw&f=1&nofb=1&ipt=eb294506ccdf34252d616437578006783a84e806cfc5052bbf291384b238de36&ipo=images)

And.............we're in!!!

![shell](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/09492d63-3386-4675-b875-dec8f5811f96)

Let's find the flag 🏁

![flag](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/5b33eba3-7625-48c6-a460-2a455366d9e8)

Yaay, that's it 🤓.


<br>

**See you again 👋🏽**


<button onclick="window.location.href='https://sixth-sensei.github.io';">Back To Home 🏠</button>


