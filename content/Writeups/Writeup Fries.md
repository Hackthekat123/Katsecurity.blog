# Machine information
Please allow up to 7 minutes for services to load. As is common in real life Windows penetration tests, you will start the Fries box with credentials for the following account : [d.cooper@fries.htb](mailto:d.cooper@fries.htb) / D4LE11maan!!
# Initial Enumeration
## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌──(kali㉿kali)-[~/HTB/Fries]
└─$ nmap 10.129.66.168             
Starting Nmap 7.95 ( https://nmap.org ) at 2026-02-02 13:52 CET
Nmap scan report for 10.129.66.168
Host is up (0.032s latency).
Not shown: 984 filtered tcp ports (no-response)
PORT     STATE SERVICE
22/tcp   open  ssh
53/tcp   open  domain
80/tcp   open  http
88/tcp   open  kerberos-sec
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
389/tcp  open  ldap
443/tcp  open  https
445/tcp  open  microsoft-ds
464/tcp  open  kpasswd5
593/tcp  open  http-rpc-epmap
636/tcp  open  ldapssl
2179/tcp open  vmrdp
3268/tcp open  globalcatLDAP
3269/tcp open  globalcatLDAPssl
5985/tcp open  wsman
```
### Detailed port scan

At the detailed port scan go to get more information from the host. 

```
┌──(kali㉿kali)-[~/HTB/Fries]
└─$ nmap -p22,53,80,88,135,139,389,443,445,464,593,636,2179,3268,3269,5985 -sCV  10.129.66.168
Starting Nmap 7.95 ( https://nmap.org ) at 2026-02-02 13:54 CET
Nmap scan report for 10.129.66.168
Host is up (0.042s latency).

PORT     STATE SERVICE       VERSION
22/tcp   open  ssh           OpenSSH 8.9p1 Ubuntu 3ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 b3:a8:f7:5d:60:e8:66:16:ca:92:f6:76:ba:b8:33:c2 (ECDSA)
|_  256 07:ef:11:a6:a0:7d:2b:4d:e8:68:79:1a:7b:a7:a9:cd (ED25519)
53/tcp   open  domain        Simple DNS Plus
80/tcp   open  http          nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://fries.htb/
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-02-02 19:54:39Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: fries.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:DC01.fries.htb, DNS:fries.htb, DNS:FRIES
| Not valid before: 2025-11-18T05:39:19
|_Not valid after:  2105-11-18T05:39:19
|_ssl-date: 2026-02-02T19:56:01+00:00; +7h00m02s from scanner time.
443/tcp  open  ssl/http      nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
| tls-alpn: 
|_  http/1.1
| ssl-cert: Subject: commonName=pwm.fries.htb/organizationName=Fries Foods LTD/stateOrProvinceName=Madrid/countryName=SP
| Not valid before: 2025-06-01T22:06:09
|_Not valid after:  2026-06-01T22:06:09
| tls-nextprotoneg: 
|_  http/1.1
|_ssl-date: TLS randomness does not represent time
|_http-title: Site doesn't have a title (text/html;charset=ISO-8859-1).
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: fries.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:DC01.fries.htb, DNS:fries.htb, DNS:FRIES
| Not valid before: 2025-11-18T05:39:19
|_Not valid after:  2105-11-18T05:39:19
|_ssl-date: 2026-02-02T19:56:00+00:00; +7h00m01s from scanner time.
2179/tcp open  vmrdp?
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: fries.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:DC01.fries.htb, DNS:fries.htb, DNS:FRIES
| Not valid before: 2025-11-18T05:39:19
|_Not valid after:  2105-11-18T05:39:19
|_ssl-date: 2026-02-02T19:56:01+00:00; +7h00m02s from scanner time.
3269/tcp open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: fries.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:DC01.fries.htb, DNS:fries.htb, DNS:FRIES
| Not valid before: 2025-11-18T05:39:19
|_Not valid after:  2105-11-18T05:39:19
|_ssl-date: 2026-02-02T19:56:00+00:00; +7h00m01s from scanner time.
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
Service Info: Host: DC01; OSs: Linux, Windows; CPE: cpe:/o:linux:linux_kernel, cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2026-02-02T19:55:24
|_  start_date: N/A
|_clock-skew: mean: 7h00m01s, deviation: 0s, median: 7h00m01s

```

Zoals je kan zien hebben we usercredentials gekregen van in het begin. Maar ik ben deze gaan testen of dat ik deze kan gebruiken voor een connectie met de smb server, windows machine, voor het uitlezen van het domain, ... maar de credentials werkte op geen enkel van deze manier. Ik heb dus ook gezien dat er een http poort openstaat, Ik zal dus eerst gaan kijken of er geen subdir bestaat waarnaar ik kan gaan kijken. 

```
┌──(kali㉿kali)-[~/HTB/Fries]
└─$ ffuf -u http://fries.htb -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt  -H "HOST:FUZZ.fries.htb" -ac
code                    [Status: 200, Size: 13591, Words: 1048, Lines: 272, Duration: 62ms]
Code                    [Status: 200, Size: 13591, Words: 1048, Lines: 272, Duration: 28ms]
```

Ik zal deze nu gaan toevoegen aan de hosts file en eens gaan kijken wat er daarop te vinden is. Als ik nu naar de webpagina van code.fries.htb ga kijken, zal je kunnen zien dat we naar een gitea pagina geleid worden. Ik zal eens proberen inloggen met de user credentials die we al hadden.

![[Pasted image 20260202140548.png]]

Zoals je zelf wel zult kunnen zien hebben we met die user credentials kunnen inloggen. Eens als je ingelogd bent, kan je zien dat er een repo gevonden wordt met een Main website die kan worden opgezet door gebruik te maken van een docker container. Ik zal de repo dus nu eerst gaan downloaden naar mijn eigen machine. Eens dat dit gedownload is volg je de stappen die je ziet in de READme file. Daarmee zal je de management pagina gaan opzetten. Als je naar de mgmt pagina gaat kijken zal je weer kunnen inloggen met dezelfde usercredentials.

![[Pasted image 20260202143702.png]]

Ik ben gaan kijken naar wat er allemaal te vinden is binnen deze pagina maar er is niets te vinden. Ik heb de fries.htb server gevonden maar daarmee moet je inloggen met de root user credentials die ik niet heb. Ik ben dus weer gebruik gaan maken van de ffuf tool en daar heb ik gezien dat er een dir `change_password` bestaat. Ik ben daarnaar eens gaan kijken en daar heb ik een csrf token gevonden.

```
csrf_token	"IjNhYzcxZTUzM2ZhZTZhZWUzZTA5NjY1OGM5NTgxMmJiM2VjNzJmMjgi.aYEIqw.6eHZdPwVlhTrAZTKJFE_IiiYQ0A"
```

Maar hiermee kan je niets doen. Ik ben weer gaan kijken in de git pagina en daar kan je op de volgende url de root user credentials vinden. http://code.fries.htb/dale/fries.htb/commit/be59cceb54b56f00778822395bdf656216ab4b9f

```
DATABASE_URL=postgresql://root:PsqLR00tpaSS11@172.18.0.3:5432/ps_db
SECRET_KEY=y0st528wn1idjk3b9a
```

Zoals je nu kunt zien, zal je wel kunnen connecteren naar de server.

![[Pasted image 20260202150719.png]]


Nu dat we de admin user credentials hebben zal ik een revshell met de server gaan maken.
https://www.rapid7.com/db/modules/exploit/multi/http/pgadmin_query_tool_authenticated/