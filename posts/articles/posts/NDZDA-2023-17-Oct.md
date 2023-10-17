# **Newly Discovered Zero-Day Attacks As Of October-17-2023 In The Past Few Weeks**


![Image Credit from : https://appcheck-ng.com/zero-day-vulnerabilities-explained/](https://appcheck-ng.com/wp-content/uploads/Zero-Day-Timeline.png)

***

## **Privilege escalation zero-day in the Atlassian Confluence Data Center and Server**

Microsoft has exposed 'Storm-0062,' a Chinese-backed threat group, exploiting a critical zero-day vulnerability in Atlassian Confluence Data Center and Server since September 14, 2023. Storm-0062, linked to China's Ministry of State Security, is known for targeting diverse sectors. Rapid7 released a proof-of-concept exploit, adding urgency to upgrading Confluence versions. Older releases and Atlassian-hosted instances remain unaffected. Refer to Atlassian's security bulletin for detailed information.

**_Reference :_** [](https://www.bleepingcomputer.com/news/security/microsoft-state-hackers-exploiting-confluence-zero-day-since-september/)


## **New IOS XE zero-day actively exploited on Cisco**

Cisco has issued a critical warning about an authentication bypass zero-day vulnerability (CVE-2023-20198) in its IOS XE software. The flaw, which is still awaiting a patch, impacts devices with the Web User Interface (Web UI) and HTTP or HTTPS Server features enabled. Attackers, if successful, can create an account with full administrator privileges, potentially leading to unauthorized activity. Cisco identified active exploitation in late September and noted related malicious activity involving the creation of local user accounts and the deployment of a malicious implant. Cisco advises disabling the HTTP Server feature on internet-facing systems to mitigate the risk.


**_Reference :_** [](https://www.bleepingcomputer.com/news/security/cisco-warns-of-new-ios-xe-zero-day-actively-exploited-in-attacks/)


## Zero-Day 'HTTP/2 Rapid Reset' DDoS Attack Sets Alarming Records

A newly discovered DDoS attack technique, known as 'HTTP/2 Rapid Reset,' has been actively exploited as a zero-day vulnerability since August, reaching record-breaking levels of attack magnitude. Amazon Web Services, Cloudflare, and Google have jointly announced successful mitigation of these attacks, with Google reporting a peak of 398 million requests per second. The technique leverages a zero-day vulnerability, CVE-2023-44487, within the HTTP/2 protocol, exploiting its stream cancellation feature to continuously send and cancel requests, overwhelming target servers. As attackers continue to adopt this method and expand their botnets, HTTP/2 Rapid Reset attacks are expected to break even greater records, posing significant challenges for mitigation.


**_Reference :_** [](https://www.bleepingcomputer.com/news/security/new-http-2-rapid-reset-zero-day-attack-breaks-ddos-records/)


## Critical Zero-Day Vulnerability in Exim Puts Millions of Servers at Risk

A severe zero-day vulnerability, identified as CVE-2023-42115, has been discovered in Exim mail transfer agent (MTA) software, allowing unauthenticated attackers to gain remote code execution on internet-exposed servers. The vulnerability, an Out-of-bounds Write weakness in the SMTP service, enables attackers to execute code in the context of the service account. Exim is widely used, and millions of servers are exposed to attacks, making it an attractive target for threat actors. While a patch is not yet available, administrators are advised to restrict remote access to mitigate potential exploitation. Additional Exim zero-day vulnerabilities with lower severity ratings were also disclosed.

**_Reference :_** [](https://www.bleepingcomputer.com/news/security/millions-of-exim-mail-servers-exposed-to-zero-day-rce-attacks/)

   
    Operating Systems Under Fire: Multiple zero-days target Windows, macOS, and Linux systems, putting millions of users at risk. Make sure your OS is up-to-date!

    Web Browsers on the Radar: Chrome, Firefox, Edge, and Safari are in the crosshairs. Update your browser ASAP to stay protected.

    Office Suite Vulnerabilities: Microsoft Office and Adobe Acrobat users, beware! Attackers are exploiting new flaws to gain control.

    IoT Devices at Risk: Smart home gadgets and industrial control systems face increasing threats. Secure your IoT devices and networks.

    Data Breach Dangers: Social media, banking apps, and online services are being targeted. Change your passwords regularly and enable two-factor authentication.

    Emerging Threats: Blockchain, 5G, and cloud-native solutions are not immune. Stay vigilant in these rapidly evolving tech spaces.

We're in this together! Cybersecurity is a shared responsibility. Ensure you're taking the necessary steps to protect your digital life. Stay updated, share this post, and tag #Senselearner to join the conversation!

🛡️ Knowledge is the best defense against cyber threats. Let's empower each other! 🛡️

#CyberSecurity #ZeroDayAttacks #StaySafeOnline #Senselearner
