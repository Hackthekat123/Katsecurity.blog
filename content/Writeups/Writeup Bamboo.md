# Initial Enumeration
## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌──(kali㉿kali)-[~]
└─$ nmap bamboo.htb                
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-17 20:08 CET
Nmap scan report for bamboo.htb (10.129.238.16)
Host is up (0.019s latency).
Not shown: 998 filtered tcp ports (no-response)
PORT     STATE SERVICE
22/tcp   open  ssh
3128/tcp open  squid-http
```
### Detailed port scan

At the detailed port scan go to get more information from the host. 

```
┌──(kali㉿kali)-[~]
└─$ nmap -p22,80 -sCV 10.129.14.246
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-15 12:37 CET
Nmap scan report for soulmate.htb (10.129.14.246)
Host is up (0.024s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 3e:ea:45:4b:c5:d1:6d:6f:e2:d4:d1:3b:0a:3d:a9:4f (ECDSA)
|_  256 64:cc:75:de:4a:e6:a5:b4:73:eb:3f:1b:cf:b4:e3:94 (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-title: Soulmate - Find Your Perfect Match
|_http-server-header: nginx/1.18.0 (Ubuntu)
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Ik zal als eerst de url gaan toevoegen aan de proxychain config file. Dit kan je doen door het onderstaande te doen.

```
┌──(kali㉿kali)-[~]
└─$ sudo nano /etc/proxychains4.conf 
# this might not work and/or cause crashes.
proxy_dns
http 10.129.238.16 3128
```

Ik ben de tool squidscan gaan installeren, dit kan je ook gaan installeren door naar de volgende link te gaan. https://github.com/aancw/spose


```
python3 spose.py --proxy 10.129.238.16:3128 --target 10.129.238.16 --full --random --threads 25 --delay 10

[+] Summary of accessible ports:  
    - Port 22: HTTP 200  
    - Port 3128: HTTP 400  
    - Port 9173: HTTP 404  
    - Port 9174: HTTP 400  
    - Port 9191: HTTP 302  
    - Port 9192: HTTP 200  
    - Port 9193: HTTP 502  
    - Port 9195: HTTP 200
```

Voor dat we zullen gaan kijken naar de webserver, zal je als eerst de proxy moeten aanpassen zodat je naar de webpagina kunt gaan. Anders zal de pagina niet weergeven worden.


Zoals je nu zult kunnen zien, zal je kunnen zien dat we op de webpagina van bamboo terecht komen.