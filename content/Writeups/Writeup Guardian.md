# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌──(kali㉿kali)-[~/HTB/Guardian]
└─$ nmap 10.129.138.156                  
Starting Nmap 7.95 ( https://nmap.org ) at 2025-09-01 09:47 CEST
Nmap scan report for 10.129.138.156
Host is up (0.020s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```

### Detailed port scan

At the detailed port scan go to get more information from the services which are open on the ip address.

```
┌──(kali㉿kali)-[~/HTB/Guardian]
└─$ nmap -p22,80 -sCV 10.129.138.156
Starting Nmap 7.95 ( https://nmap.org ) at 2025-09-01 09:48 CEST
Nmap scan report for 10.129.138.156
Host is up (0.018s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 9c:69:53:e1:38:3b:de:cd:42:0a:c8:6b:f8:95:b3:62 (ECDSA)
|_  256 3c:aa:b9:be:17:2d:5e:99:cc:ff:e1:91:90:38:b7:39 (ED25519)
80/tcp open  http    Apache httpd 2.4.52
|_http-title: Did not follow redirect to http://guardian.htb/
|_http-server-header: Apache/2.4.52 (Ubuntu)
Service Info: Host: _default_; OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

We zulllen deze gaan toevoegen aan de host file en daarna ben ik eens gaan kijken wat er op de webpagina te zien valt.

![[Pasted image 20250901095335.png]]

Voordat ik naar de student portal zal gaan, ben ik eerst gebruik gaan maken van de tool whatweb. Hierbij kan je de volgende belangrijke informatie vinden.

```
┌──(kali㉿kali)-[~/HTB/Guardian]
└─$ whatweb http://guardian.htb                                               
http://guardian.htb [200 OK] Apache[2.4.52], Country[RESERVED][ZZ], Email[GU0142023@guardian.htb,GU0702025@guardian.htb,GU6262023@guardian.htb,admissions@guardian.htb], HTML5, HTTPServer[Ubuntu Linux][Apache/2.4.52 (Ubuntu)], IP[10.129.138.156], Script, Title[Guardian University - Empowering Future Leaders]
```

Ik ben ook gaan kijken of dat er een subdomain gekent is en zoals je kan zien heb ik het portal subdomain gevonden heb.

```
┌─[eu-dedivip-2]─[10.10.14.106]─[hackthekat123@htb-fiuawcwald]─[~]
└──╼ [★]$ ffuf -u http://guardian.htb -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt -H "HOST:FUZZ.guardian.htb" -ac

:: Progress: [1/19966] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: 0 portal   
```

Nu zal ik naar de Student portal gaan, maar zoals je kunt zien zal je deze eerst ook nog moeten toevoegen aan de hosts file. eens je dit gedaan hebt kom je op de volgende pagina terecht.

![[Pasted image 20250901095805.png]]

Maar zoals je kunt zien kunnen we niet inloggen doordat we geen password van de mails hebben. Ik heb op de help link geklikt en daar kom je op de volgende pagina waar dat er een default password gekent is.

![[Pasted image 20250901100432.png]]

Wat als we dit password nu gaan gebruiken voor in te loggen op de student portal. Ik ben elke student id's gaan testen en voor het inloggen op de student portal kan je de volgende user credentials gebruiken.

| Username  | Password |
| --------- | -------- |
| GU0142023 | GU1234   |
Ik ben op de portal gaan kijken en daar ben ik de volgende tab gekomen. http://portal.guardian.htb/student/chat.php?chat_users[0]=2&chat_users[1]=1. Hier kan je zien dat er een gitea user en password gekent is. Ik zal het subdomain gitea.guardian.htb gaan toevoegen aan de host file en naar de webpagina surfen.

| Username | Password    |
| -------- | ----------- |
| jamil    | DHsNnk3V503 |


![[Pasted image 20260211210603.png]]

Daar ben ik eens gaan kijken of ik niets speciaals te vinden is. Ik ben naar de commitments geweest en daar heb ik naar een password gezocht. Hieronder kan je zien dat ik de volgende user creds gevonden heb.

| Username | Password                 |
| -------- | ------------------------ |
| root     | Gu4rd14n_un1_1s_th3_b3st |
Je kan hieronder ook zien dat er een database name gevonden is. 'guardiandb'

![[Pasted image 20260211212738.png]]