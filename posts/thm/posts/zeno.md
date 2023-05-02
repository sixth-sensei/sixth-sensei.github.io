<h3> Zeno TryHackMe </h3>

### Difficulty = Medium

Let's start with scanning for open ports 

I use rustscan to get the ports then nmap to scan specific ports
![image](https://user-images.githubusercontent.com/127159644/235651240-ef4fb2ce-0553-4eac-9937-62d857ed6d70.png)

We can see there's a web service running on port *12340*

Navigating to it shows this
![image](https://user-images.githubusercontent.com/127159644/235651114-1235e03f-726e-4054-a47e-f3c92e385e6c.png)

Nothing really interesting there so I ran gobuster to fuzz for directories and luckily I got this
![image](https://user-images.githubusercontent.com/127159644/235651381-e1fe94c2-d810-4b2f-98e6-2133c07cf6e6.png)

```
Wordlist Used: /usr/share/seclists/Discovery/Web-Content/big.txt
```

Going over the directory on the web server shows this
![image](https://user-images.githubusercontent.com/127159644/235651543-746a7790-a2dc-405c-9165-f0908b4e6119.png)

The name of the web service got me thinking *Pathfinder Hotel Restaurant* 

So I searched for [exploit](https://www.exploit-db.com/exploits/47520) and saw this RCE
![image](https://user-images.githubusercontent.com/127159644/235651729-ebc6e2c3-d51f-4a1a-b100-3e6af2ffb4e2.png)

Trying it works 
![image](https://user-images.githubusercontent.com/127159644/235652887-11bd754a-ae08-429e-95f3-5aa7db33e047.png)

We can now confirm it by accessing it and executing a command
![image](https://user-images.githubusercontent.com/127159644/235652964-e9250e2e-783c-4472-a97f-dbcdf294e60c.png)

Now lets get a reverse shell :D