# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌──(kali㉿kali)-[~]
└─$ nmap 10.129.234.46
Starting Nmap 7.95 ( https://nmap.org ) at 2026-02-16 15:23 EST
Nmap scan report for 10.129.234.46
Host is up (0.027s latency).
Not shown: 997 closed tcp ports (reset)
PORT     STATE SERVICE
22/tcp   open  ssh
8080/tcp open  http-proxy
9000/tcp open  cslistener
```
### Detailed port scan

At the detailed port scan go to get more information from the services which are open on the ip address.

```
┌──(kali㉿kali)-[~]
└─$ nmap -p22,8080,9000 -sCV 10.129.234.46
Starting Nmap 7.95 ( https://nmap.org ) at 2026-02-16 15:24 EST
Nmap scan report for 10.129.234.46
Host is up (0.020s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|_  3072 f3:6c:aa:fe:2c:20:f6:55:a0:5b:61:54:cf:39:17:d0 (RSA)
8080/tcp open  http    Apache Tomcat
|_http-open-proxy: Proxy might be redirecting requests
|_http-title: Apache Tomcat
9000/tcp open  http    Golang net/http server
| http-robots.txt: 1 disallowed entry 
|_/
| http-title: authentik
|_Requested resource was /if/flow/default-authentication-flow/?next=%2F
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.0 404 Not Found
|     Content-Length: 3556
|     Content-Type: text/html; charset=utf-8
|     Date: Mon, 16 Feb 2026 20:24:46 GMT
|     Referrer-Policy: same-origin
|     Vary: Accept-Encoding
|     Vary: Cookie
|     X-Authentik-Id: 12a5ab3a9b6041c2a57d74043e862549
|     X-Content-Type-Options: nosniff
|     X-Frame-Options: DENY
|     X-Powered-By: authentik
|     <!DOCTYPE html>
|     <html>
|     <head>
|     <meta charset="UTF-8">
|     <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1">
|     <title>
|     authentik
|     </title>
|     <link rel="icon" href="/static/dist/assets/icons/icon.png">
|     <link rel="shortcut icon" href="/static/dist/assets/icons/icon.png">
|     <link rel="prefetch" href="/static/dist/assets/images/flow_background.jpg" />
|     <link rel="stylesheet" type="text/css" href="/static/dist/patternfly.min.css">
|     <link rel="stylesheet" type="text/css" href="/static/dist/theme-dar
|   GenericLines, Help, RTSPRequest, SSLSessionReq: 
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|     Request
|   GetRequest: 
|     HTTP/1.0 302 Found
|     Content-Length: 0
|     Content-Type: text/html; charset=utf-8
|     Date: Mon, 16 Feb 2026 20:24:28 GMT
|     Location: /flows/-/default/authentication/?next=/
|     Referrer-Policy: same-origin
|     Vary: Accept-Encoding
|     Vary: Cookie
|     X-Authentik-Id: 4d27cade1d91498198ecce2140483a41
|     X-Content-Type-Options: nosniff
|     X-Frame-Options: DENY
|     X-Powered-By: authentik
|   HTTPOptions: 
|     HTTP/1.0 302 Found
|     Content-Length: 0
|     Content-Type: text/html; charset=utf-8
|     Date: Mon, 16 Feb 2026 20:24:28 GMT
|     Location: /flows/-/default/authentication/?next=/
|     Referrer-Policy: same-origin
|     Vary: Accept-Encoding
|     Vary: Cookie
|     X-Authentik-Id: a31d65c0890e41a098d8dfdb2c66a135
|     X-Content-Type-Options: nosniff
|     X-Frame-Options: DENY
|_    X-Powered-By: authentik
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port9000-TCP:V=7.95%I=7%D=2/16%Time=69937CFB%P=x86_64-pc-linux-gnu%r(Ge
SF:nericLines,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nContent-Type:\x20t
SF:ext/plain;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\n400\x20Bad\x
SF:20Request")%r(GetRequest,16F,"HTTP/1\.0\x20302\x20Found\r\nContent-Leng
SF:th:\x200\r\nContent-Type:\x20text/html;\x20charset=utf-8\r\nDate:\x20Mo
SF:n,\x2016\x20Feb\x202026\x2020:24:28\x20GMT\r\nLocation:\x20/flows/-/def
SF:ault/authentication/\?next=/\r\nReferrer-Policy:\x20same-origin\r\nVary
SF::\x20Accept-Encoding\r\nVary:\x20Cookie\r\nX-Authentik-Id:\x204d27cade1
SF:d91498198ecce2140483a41\r\nX-Content-Type-Options:\x20nosniff\r\nX-Fram
SF:e-Options:\x20DENY\r\nX-Powered-By:\x20authentik\r\n\r\n")%r(HTTPOption
SF:s,16F,"HTTP/1\.0\x20302\x20Found\r\nContent-Length:\x200\r\nContent-Typ
SF:e:\x20text/html;\x20charset=utf-8\r\nDate:\x20Mon,\x2016\x20Feb\x202026
SF:\x2020:24:28\x20GMT\r\nLocation:\x20/flows/-/default/authentication/\?n
SF:ext=/\r\nReferrer-Policy:\x20same-origin\r\nVary:\x20Accept-Encoding\r\
SF:nVary:\x20Cookie\r\nX-Authentik-Id:\x20a31d65c0890e41a098d8dfdb2c66a135
SF:\r\nX-Content-Type-Options:\x20nosniff\r\nX-Frame-Options:\x20DENY\r\nX
SF:-Powered-By:\x20authentik\r\n\r\n")%r(RTSPRequest,67,"HTTP/1\.1\x20400\
SF:x20Bad\x20Request\r\nContent-Type:\x20text/plain;\x20charset=utf-8\r\nC
SF:onnection:\x20close\r\n\r\n400\x20Bad\x20Request")%r(Help,67,"HTTP/1\.1
SF:\x20400\x20Bad\x20Request\r\nContent-Type:\x20text/plain;\x20charset=ut
SF:f-8\r\nConnection:\x20close\r\n\r\n400\x20Bad\x20Request")%r(SSLSession
SF:Req,67,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nContent-Type:\x20text/pla
SF:in;\x20charset=utf-8\r\nConnection:\x20close\r\n\r\n400\x20Bad\x20Reque
SF:st")%r(FourOhFourRequest,F27,"HTTP/1\.0\x20404\x20Not\x20Found\r\nConte
SF:nt-Length:\x203556\r\nContent-Type:\x20text/html;\x20charset=utf-8\r\nD
SF:ate:\x20Mon,\x2016\x20Feb\x202026\x2020:24:46\x20GMT\r\nReferrer-Policy
SF::\x20same-origin\r\nVary:\x20Accept-Encoding\r\nVary:\x20Cookie\r\nX-Au
SF:thentik-Id:\x2012a5ab3a9b6041c2a57d74043e862549\r\nX-Content-Type-Optio
SF:ns:\x20nosniff\r\nX-Frame-Options:\x20DENY\r\nX-Powered-By:\x20authenti
SF:k\r\n\r\n\n\n\n\n<!DOCTYPE\x20html>\n\n<html>\n\x20\x20\x20\x20<head>\n
SF:\x20\x20\x20\x20\x20\x20\x20\x20<meta\x20charset=\"UTF-8\">\n\x20\x20\x
SF:20\x20\x20\x20\x20\x20<meta\x20name=\"viewport\"\x20content=\"width=dev
SF:ice-width,\x20initial-scale=1,\x20maximum-scale=1\">\n\x20\x20\x20\x20\
SF:x20\x20\x20\x20<title>\nauthentik\n</title>\n\x20\x20\x20\x20\x20\x20\x
SF:20\x20<link\x20rel=\"icon\"\x20href=\"/static/dist/assets/icons/icon\.p
SF:ng\">\n\x20\x20\x20\x20\x20\x20\x20\x20<link\x20rel=\"shortcut\x20icon\
SF:"\x20href=\"/static/dist/assets/icons/icon\.png\">\n\x20\x20\x20\x20\x2
SF:0\x20\x20\x20\n<link\x20rel=\"prefetch\"\x20href=\"/static/dist/assets/
SF:images/flow_background\.jpg\"\x20/>\n<link\x20rel=\"stylesheet\"\x20typ
SF:e=\"text/css\"\x20href=\"/static/dist/patternfly\.min\.css\">\n<link\x2
SF:0rel=\"stylesheet\"\x20type=\"text/css\"\x20href=\"/static/dist/theme-d
SF:ar");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Ik ben eens gaan kijken naar de verschillende poorten en daar heb ik gezien dat er een authentik en apache page is. Maar deze informatie ben ik helemaal niets. Ik ben dus gaan kijken of dat er geen subdirectory is en daar heb ik gezien dat er een gitlab subdomain gevonden is. Ik zal deze gaan toevoegen aan de hosts file en dan eens gaan kijken wat ik op de gitlab page kan doen.

![[Pasted image 20260216221459.png]]

Zoals je zelf zult weten heb je geen login gevonden voor inteloggen op deze pagina. Hiermee ben ik naar de explore tab onderaan geweest want dan kan je wel bekijken welke projects er zijn en wat er in gesubmit is geweest. Daarin kan je zien dat er een `satoru/gitconnect` project is en dat deze 2 keer gecommit is geweest. Als je gaat kijken naar de eerst commitment zal je een username en een password vinden.

![[Pasted image 20260216221806.png]]

| Username | Password           |
| -------- | ------------------ |
| satoru   | `dGJ2V72SUEMsM3Ca` |
Ik zal nu aan de hand van deze login gaan inloggen op de volgende 3 verschillende url's:
- http://gitlab.barrier.vl --> SuccessFull
- http://barrier.vl:8080 --> Not Succesfull
- http://barrier.vl:9000 --> Succesfull

Je kan op de gitlab page geen andere informatie voor het moment vinden, dus ben ik gaan inloggen op de authentik page en daar kan je zien dat er een 2 applications bestaan. 
- Gitlab
- Guacamole
![[Pasted image 20260216222229.png]]
Ik ben eens gaan kijken op de guacamole application maar er was ook niets te vinden. Ik ben nog eens gaan kijken naar de web-admin login page van poort 8080 en als je daar op cancel drukt bij het vragen van de usercredentials, kom je op de volgende pagina terecht.

![[Pasted image 20260216225302.png]]
Hier kan je zien dat er login credentials te zien zijn. Ik zal deze gebruiken voor inteloggen op de pagina

| Username | Password |
| -------- | -------- |
| tomcat   | s3cret   |
