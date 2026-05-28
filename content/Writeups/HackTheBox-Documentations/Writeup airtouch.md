# Initial Enumeration
## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address. Maar zoals je hieronder zult kunnen zien is er geen enkel tcp poort buiten de ssh poort open. Hiervoor zal ik eens een test gaan doen op udp poorten.

### TCP

```
┌──(kali㉿kali)-[~]
└─$ nmap 10.129.45.143                                                                  
Starting Nmap 7.95 ( https://nmap.org ) at 2026-01-18 15:50 CET
Nmap scan report for 10.129.45.143
Host is up (0.25s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
```

### UDP

Zoals je kan zien zijn er 2 udp poorten open.

```
┌──(kali㉿kali)-[~]
└─$ nmap -sU 10.129.45.143                                                    
Nmap scan report for airtouch.htb (10.129.45.143)
Host is up (0.022s latency).
Not shown: 998 closed udp ports (port-unreach)
PORT    STATE         SERVICE
68/udp  open|filtered dhcpc
161/udp open          snmp

```

### Detailed port scan

At the detailed port scan go to get more information from the host.

```
┌──(kali㉿kali)-[~]
└─$ nmap -p68,161 -sU -sCV 10.129.45.143
Starting Nmap 7.95 ( https://nmap.org ) at 2026-01-18 16:34 CET
Nmap scan report for airtouch.htb (10.129.45.143)
Host is up (0.017s latency).

PORT    STATE         SERVICE VERSION
68/udp  open|filtered dhcpc
161/udp open          snmp    SNMPv1 server; net-snmp SNMPv3 server (public)
| snmp-info: 
|   enterprise: net-snmp
|   engineIDFormat: unknown
|   engineIDData: 2dbb4477d9f26c6900000000
|   snmpEngineBoots: 1
|_  snmpEngineTime: 47m11s
| snmp-sysdescr: "The default consultant password is: RxBlZhLmOkacNWScmZ6D (change it after use it)"
|_  System uptime: 47m11.03s (283103 timeticks)
Service Info: Host: Consultant
```

Hierboven kan je zien dat er usercredentials gedeeld worden. Ik ben door gebruik te maken van het nxc tool gaan zien of dat ik deze user niet kan gebruiken voor inteloggen op de ssh server.

```
┌──(kali㉿kali)-[~]
└─$ sudo nxc ssh AirTouch.htb -u consultant -p 'RxBlZhLmOkacNWScmZ6D'
SSH         10.129.45.143   22     AirTouch.htb     [*] SSH-2.0-OpenSSH_8.2p1 Ubuntu-4ubuntu0.11
SSH         10.129.45.143   22     AirTouch.htb     [+] consultant:RxBlZhLmOkacNWScmZ6D (Pwn3d!) Linux - Shell access!
```

Ik zal dus nu de connectie met de ssh server gaan opzetten.

```
┌──(kali㉿kali)-[~/HTB/airtouch]
└─$ ssh consultant@airtouch.htb                                      
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
consultant@airtouch.htb's password: 
Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.4.0-216-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.
Last login: Sun Jan 18 15:36:29 2026 from 10.10.16.154
consultant@AirTouch-Consultant:~$ 
```

Als je hier het ls commando gaat gebruiken zal je kunnen zien dat er 2 verschillende .png bestanden gevonden zijn. Ik zal deze eerst naar mijn eigen machine gaan downloaden door op de eigen machine het volgende commando gedaan te hebben.

```
┌──(kali㉿kali)-[~/HTB/airtouch]
└─$ scp consultant@airtouch.htb:/home/consultant/diagram-net.png .
┌──(kali㉿kali)-[~/HTB/airtouch]
└─$ scp consultant@airtouch.htb:/home/consultant/photo_2023-03-01_22-04-52.png .
```

Nu zal ik de bestanden gaan openen door het `open diagram-net.png` commando te gebruiken. Daar zal je de volgende images kunnen zien.

![[Pasted image 20260118192804.png]]

Wat we hieraan kunnen uitleiden is dat er 3 verschillende Vlans zijn.

- Consultant vlan (172.20.0.1/24)
	- Switch
	- Ingeloged door de consultant user credentials
- Tablets vlan
	- Tablet manager (192.168.3.0/24)
	- AirTouch-Internet AP
	- Tablets
- Corp vlan
	- Corporate computers (10.10.10.0/24)
	- AirTouch-Office

Maar voor de rest is er geen andere informatie dat hiervan gebruikt kan worden. Ik zal dus verder moeten zoeken op de ssh connectie. Ik ben gaan kijken welke poorten dat er openstaan, zodat ik deze kan gebruiken voor een port forwarding.

```
consultant@AirTouch-Consultant:~$ netstat -tulnp
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 127.0.0.1:8080          0.0.0.0:*               LISTEN      7878/ssh            
tcp        0      0 127.0.0.11:45011        0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:9090          0.0.0.0:*               LISTEN      7724/ssh            
tcp6       0      0 ::1:8080                :::*                    LISTEN      7878/ssh            
tcp6       0      0 :::22                   :::*                    LISTEN      -                   
tcp6       0      0 ::1:9090                :::*                    LISTEN      7724/ssh            
udp        0      0 127.0.0.11:60806        0.0.0.0:*                           -                   
udp        0      0 0.0.0.0:161             0.0.0.0:*                           -        
```

Ik zal nu port forwarding gaan doen, 