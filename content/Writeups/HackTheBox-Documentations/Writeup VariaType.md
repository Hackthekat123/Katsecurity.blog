# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌─[eu-dedivip-2]─[10.10.15.139]─[hackthekat123@htb-siv2baq7sw]─[~]
└──╼ [★]$ nmap 10.129.11.200
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-03-31 12:42 CDT
Nmap scan report for 10.129.11.200
Host is up (0.0084s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```
### Detailed port scan

At the detailed port scan go to get more information from the services which are open on the ip address.

```
┌─[eu-dedivip-2]─[10.10.15.139]─[hackthekat123@htb-siv2baq7sw]─[~]
└──╼ [★]$ nmap -p22,80 -sCV 10.129.11.200
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-03-31 12:42 CDT
Nmap scan report for 10.129.11.200
Host is up (0.0076s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.2p1 Debian 2+deb12u7 (protocol 2.0)
| ssh-hostkey: 
|   256 e0:b2:eb:88:e3:6a:dd:4c:db:c1:38:65:46:b5:3a:1e (ECDSA)
|_  256 ee:d2:bb:81:4d:a2:8f:df:1c:50:bc:e1:0e:0a:d1:22 (ED25519)
80/tcp open  http    nginx 1.22.1
|_http-server-header: nginx/1.22.1
|_http-title: Did not follow redirect to http://variatype.htb/
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Ik ben de domain gaan toevoegen aan de hosts file en ben gaan kijken naar de webpagina.

Zoals je kan zien kan je een bestand uploaden. Mss kan dit bruikbaar zijn, voor exploiten van de webpagina.

![[Pasted image 20260331194818.png]]
### Fuzzing Subdomains

Hier kan je zien dat er een portal subdomain bestaat. We gaan deze toevoegen in de hosts file en gaan dan gaan kijken naar de portal.

```
┌─[eu-dedivip-2]─[10.10.15.139]─[hackthekat123@htb-siv2baq7sw]─[~]
└──╼ [★]$ ffuf -u http://variatype.htb -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt -H "HOST:FUZZ.variatype.htb" -ac

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://variatype.htb
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt
 :: Header           : Host: FUZZ.variatype.htb
 :: Follow redirects : false
 :: Calibration      : true
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

portal                  [Status: 200, Size: 2494, Words: 445, Lines: 59, Duration: 11ms]
```

Als we gaan kijken naar de portal, kan je zien dat er een versie gekent is. De versie waarop de portal gedraait wordt is VT-VALID-2.1.4

![[Pasted image 20260331195029.png]]

Hier zijn we vrij weinig mee voor het moment. ik ben eens gaan kijken welke directories en files er waren voor de portal en daar kan je zien dat The portal subdomain has an exposed .git directory:

```
┌─[eu-dedivip-2]─[10.10.15.139]─[hackthekat123@htb-siv2baq7sw]─[~]
└──╼ [★]$ ffuf -u http://portal.variatype.htb/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-files.txt -ac

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://portal.variatype.htb/FUZZ
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/raft-medium-files.txt
 :: Follow redirects : false
 :: Calibration      : true
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

index.php               [Status: 200, Size: 2494, Words: 445, Lines: 59, Duration: 9ms]
download.php            [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 10ms]
auth.php                [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 8ms]
view.php                [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 9ms]
.                       [Status: 200, Size: 2494, Words: 445, Lines: 59, Duration: 8ms]
styles.css              [Status: 200, Size: 8789, Words: 1020, Lines: 370, Duration: 7ms]
dashboard.php           [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 13ms]
.git 
```

Ik zal dus nu aan de hand van githacker alle gegevens vanuit de .git folder gaan halen en deze gaan downloaden naar de eigen machine

```
┌─[eu-dedivip-2]─[10.10.15.139]─[hackthekat123@htb-siv2baq7sw]─[~]
└──╼ [★]$ githacker --url http://portal.variatype.htb/.git/ --output-folder variatype
```

Hier kan je zien dat er een login gevonden is. Ik zal hiermee proberen inloggen op de portal

```
─[eu-dedivip-2]─[10.10.15.139]─[hackthekat123@htb-siv2baq7sw]─[~/variatype/fe084969f47205bd4b423de40c823802]
└──╼ [★]$ cat auth.php 
<?php
session_start();
$USERS = [
    'gitbot' => 'G1tB0t_Acc3ss_2025!'
];

```

Zoals je kunt zien ben ik daarmee kunnen inloggen

![[Pasted image 20260331203155.png]]

