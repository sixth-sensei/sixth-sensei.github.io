# Death Note

***
![DeathNote](https://tryhackme-images.s3.amazonaws.com/room-icons/65502078fb62b5529c5bd957-1718426866099)

### Difficulty: Easy

***

## Nmap Scan

Running nmap scan on the machine, we have

```bash
┌──(sixth-sensei㉿senseisec)-[~/THM/DeathNote]
└─$ nmap -sS -sV -p- 10.10.225.243 --min-rate=1000
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-06-17 06:55 WAT
Nmap scan report for 10.10.225.243
Host is up (0.46s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 125.53 seconds

```
we have 2 open ports `ssh` and `http`; Let's get started

## Enumeration

### Port 80

Navigating to the web service, we're greeted with this page

[screenshot http_service]

From the webpage, we see a upload dialog and the first thing that comes to mind is uploading a reverse shell

[screenshot http_fileupload]

- tldr; Download php reverse shell file from [pentestmokey](http://pentestmonkey.net/tools/web-shells/php-reverse-shell) if you don't already have, then set your IP and preferred port

[screenshot reverse_shell_settings]

Uploading our reverse shell script, we got this error

[screenshot hex_magicNumbers]

The site only allows uploads of files with the hex signature `FF D8 FF E0` which is used for JPEG/JPG files.

We can easily edit the hex signature of our php script and add the allowed signature to its beginning in order to bypass this restriction; to do that we'll use [hexed.it](https://hexed.it)

[screenshot hexed_it]

Select `Open File` to open the PHP script, once opened; right click on the first bytes highlighted

[screenshot insert_bytes]

Choose `Insert bytes` 

[screenshot bytes_filling]

Specify number of bytes to fill, 4 in our case and the fill pattern which is the actual signature we want to add `FF D8 FF E0`

[screenshot jpeg_bytes]

We can see the bytes has been added and in the far right there are appended characters before the PHP tag.

Click export to save our modified script and try the upload again; you'd notice its successful.

[GIF thumbs up]

Now we can spawn a reverse shell and catch it on our machine/attackbox, but we don't know the upload directory for the site yet in order to do this.

Firing up gobuster to discover hidden directories

[screenshot gobuster_start]

Found a `/notes` directory almost immediately

[screenshot gobuster_end]

Navigating to this endpoint

[screenshot revshell_location]

We see our script sitting pretty (wink emoji)

## Initial Access
Listening on our specified port and running the script

[screenshot www-data_shell]

We got access as `www-data`; navigating to the home directory

[screenshot home_dir]

Saw there are two standard users present and from the permissions, `ryuk`'s folder has read and execute permission for others. Let's check that out

[screenshot ryuk_folder]

We see two files including the user flag but unfortunately can't read any as `www-data`.

Combing the filesystem further, found something interesting in `opt` which contained an `id_rsa` file

[screenshot opt_dir]

This has to belong to one of the users but first let's get it on our machine using netcat

[screenshot nc_idrsa_victim]

[screenshot nc_idrsa_attacker]

### Pivot #1

First try, authenticating as `ryuk` (set the right permissions for the identity file `chmod 600 id_rsa`)

[screenshot auth_as_ryuk]

Voila! the id file belongs to ryuk and we've now pivoted from `www-data` to `ryuk`

[screenshot ryuk_homedir]

We still can only read one of the files according to the permissions, let's check that out

[screenshot rotedcreds]

Looks like a shadow file entry but there's no user `yvtug` present (thinking emoji)

If you guessed encoding then you're right, parsed it to `dcode.fr` to determine the algorithm used

[screenshot dcode_rotedcreds]

Wasn't familiar with any of the results but i know ROT-13, feeding into cyberchef

[screenshot light_creds]

Happens to be the credentials for user `light`

### Pivot #2

Using the found credentials to authenticate

[screenshot auth_as_light]

We have now successfully pivoted from `www-data` to `ryuk` and now `light`; also should be able to read the user flag now

[screenshot userflag]

There it is!

## Privilege Escalation
Found out `light` has no sudo privileges after running `sudo -l`; Then ran linpeas script on the machine and saw `docker` appears to be a 95% privilege escalation vector.

Checked if light had any access

[screenshot id_light]

We can see he's part of `docker` group, did a qucik google search to see how that can be useful

[screenshot docker_privesc]

The `GTFOBins` entry looked like a lead

[screenshot gtfobins_docker]

Then i discovered we can use it to break out of our current shell and gain root (happy dance emoji)

Executing the command `docker run -v /:/mnt --rm -it alpine chroot /mnt sh` on the machine

[screenshot root_shell]

...and we got root!! listing the root directory

[screenshot root_dir]

We can see our root flag and also successfully retrieved the **deathnote**

[screenshot root_flag]

The end, I hope you enjoyed it as much as i do

<br>

**See you again 👋🏽**


<button onclick="window.location.href='https://sixth-sensei.github.io';">Back To Home 🏠</button>