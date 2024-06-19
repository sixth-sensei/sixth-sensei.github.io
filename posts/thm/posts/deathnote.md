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

![http_service](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/befb4cb6-7b01-40ce-b44e-8186363d729b)

From the webpage, we see a upload dialog and the first thing that comes to mind is uploading a reverse shell

![http_fileupload](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/31ffe8ad-e276-4d95-9ba6-90854da608cb)

- tldr; Download php reverse shell file from [pentestmonkey](http://pentestmonkey.net/tools/web-shells/php-reverse-shell) if you don't already have, then set your IP and preferred port

![reverse_shell_settings](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/f2434785-eb62-4453-ae97-e15889fda9d4)

Uploading our reverse shell script, we got this error

![hex_magicNumbers](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/6f025dec-b42c-4359-9e60-0eced59e8343)

The site only allows uploads of files with the hex signature `FF D8 FF E0` which is used for JPEG/JPG files.

We can easily edit the hex signature of our php script and add the allowed signature to its beginning in order to bypass this restriction; to do that we'll use [hexed.it](https://hexed.it)

![hexed_it](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/d63cd2b6-9d22-4224-80fa-92e808eb452e)

Select `Open File` to open the PHP script, once opened; right click on the first bytes highlighted

![insert_bytes_edited](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/0121d274-7a89-40f7-a790-4ee5f6745762)

Choose `Insert bytes` 

![bytes_filling](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/309dc9b1-222f-434f-821e-92f5b5d238a5)

Specify number of bytes to fill, 4 in our case and the fill pattern which is the actual signature we want to add `FF D8 FF E0` then click `apply`

![jpeg_bytes](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/8faedfcc-f177-41f0-80a2-f9f0216260d7)

We can see the bytes has been added and in the far right there are appended characters before the PHP tag.

Click export to save our modified script and try the upload again; you'd notice its successful.

![GIF thumbs up](https://media1.tenor.com/m/dP9z7VKeJJ8AAAAC/thumbs-up.gif)

Now we can spawn a reverse shell and catch it on our machine/attackbox, but we don't know the upload directory for the site yet in order to do this.

Firing up gobuster to discover hidden directories

![gobuster_start](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/92293f72-a8d7-453c-adb8-1f31bc926846)

Found a `/notes` directory almost immediately

![gobuster_end](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/1b9c65e3-73bf-4a5f-9e70-91dc5a6cea50)

Navigating to this endpoint

![revshell_location](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/4a53523a-6bd5-44eb-ba10-d276ddd4a0be)

We see our script sitting pretty 😉

## Initial Access
Listening on our specified port and running the script

![www-data_shell](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/ac3a2c22-3e9f-4c20-bc12-358e1356f0b2)

We got access as `www-data`; navigating to the home directory

![home_dir](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/7590b867-3b06-4bcc-8304-a1addd8e9898)

Saw there are two standard users present and from the permissions, `ryuk`'s folder has read and execute permission for others. Let's check that out

![ryuk_folder](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/7a5e3d5a-1a88-44a1-9d87-24cdbfd3f6e8)

We see two files including the user flag but unfortunately can't read any as `www-data`.

Combing the filesystem further, found something interesting in `opt` which contained an `id_rsa` file

![opt_dir](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/7d651d66-bb21-46ea-a145-5e0e98140361)

This has to belong to one of the users but first let's get it on our machine using netcat

![nc_idrsa_victim](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/be124063-1986-4de0-9b76-a524afb1bc89)

![nc_idrsa_attacker](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/990e762b-bfe3-4f47-982b-3c9055bb219b)

### Pivot #1

First try, authenticating as `ryuk` (set the right permissions for the identity file `chmod 600 id_rsa`)

![auth_as_ryuk](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/fed7b0ce-7f2d-48a3-ae90-9f9f3af3091b)

Voila! the id file belongs to ryuk and we've now pivoted from `www-data` to `ryuk`

![ryuk_homedir](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/0844d5a3-11f5-4297-9ff3-3e9a1a419986)

We still can only read one of the files according to the permissions, let's check that out

![rotedcreds](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/fd977c3a-07b8-4317-92da-bb609a1af087)

Looks like a shadow file entry but there's no user `yvtug` present 🤔

If you guessed encoding then you're right, parsed it to `dcode.fr` to determine the algorithm used

![dcode_rotedcreds](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/a92d2e1c-080f-4ded-97ae-a1b74c0e8820)

Wasn't familiar with any of the results but i know ROT-13, feeding into [cyberchef](https://gchq.github.io/CyberChef/)

![light_creds](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/0048a15d-20a2-4f77-b952-c66073110b1a)

Happens to be the credentials for user `light`

### Pivot #2

Using the found credentials to authenticate

![auth_as_light](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/d9860bc6-ef5d-419e-b848-dc4638b526d3)

We have now successfully pivoted from `www-data` to `ryuk` and now `light`; also should be able to read the user flag now

![userflag](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/3c3d3048-c1aa-487e-aabb-e47fa4c54918)

There it is!

## Privilege Escalation
Found out `light` has no sudo privileges after running `sudo -l`; Then ran linpeas script on the machine and saw `docker` appears to be a 95% privilege escalation vector.

Checked if light had any access

![id_light](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/a97fcf0e-99dd-469a-8167-a7677cc46ecb)

We can see he's part of `docker` group, did a qucik google search to see how that can be useful

![docker_privesc](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/39cabae6-4bc2-40f6-a502-d05d8db64ee6)

The `GTFOBins` entry looked like a lead

![gtfobins_docker](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/86780a37-da58-4657-a34f-60bf3cce7fce)

Then i discovered we can use it to break out of our current shell and gain root 🤠

Executing the command `docker run -v /:/mnt --rm -it alpine chroot /mnt sh` on the machine

![root_shell](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/17c06dd7-5995-4fde-bc69-a8f5d70951f3)

...and we got root!! listing the root directory

![root_dir](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/123747e1-9ad6-4193-aedc-4dd014dd1e74)

We can see our root flag and also successfully retrieved the **death note** 🗒️

![root_flag](https://github.com/sixth-sensei/sixth-sensei.github.io/assets/31647166/7835b7eb-69a4-4e10-be60-cddd353acdd5)

The end, I hope you enjoyed it as much as i do

![well done](https://media1.tenor.com/m/sIzMTGPxIeMAAAAd/well-done.gif)

<br>

**See you again 👋🏽**


<button onclick="window.location.href='https://sixth-sensei.github.io';">Back To Home 🏠</button>
