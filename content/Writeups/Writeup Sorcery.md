
# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
nmap -p- 10.129.238.66                                                              
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-15 04:13 CEST
Nmap scan report for 10.129.238.66
Host is up (0.020s latency).
Not shown: 65533 closed tcp ports (reset)
PORT    STATE SERVICE
22/tcp  open  ssh
443/tcp open  https

Nmap done: 1 IP address (1 host up) scanned in 9.35 seconds
```

### Detailed port scan

At the detailed port scan go to get more information from the services who are open on the ip address.

```
nmap -p- -sCV 10.129.238.66 -vvvv
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-15 04:13 CEST
Host is up, received echo-reply ttl 63 (0.021s latency).
Scanned at 2025-06-15 04:13:56 CEST for 70s
Not shown: 65533 closed tcp ports (reset)
PORT    STATE SERVICE REASON         VERSION
22/tcp  open  ssh     syn-ack ttl 63 OpenSSH 9.6p1 Ubuntu 3ubuntu13.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 97:b6:72:9c:39:a9:6c:dc:01:ab:3e:aa:ff:cc:13:4a (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIPzwgWWL8qvTI4EzWXUX7/aGWcm8W4pTGnFiqfVbeOeh
443/tcp open  https   syn-ack ttl 62 nginx/1.27.1
|_http-server-header: nginx/1.27.1
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=sorcery.htb
| Issuer: commonName=Sorcery Root CA
| Public Key type: rsa
| Public Key bits: 4096
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-10-31T02:09:11
| Not valid after:  2052-03-18T02:09:11
| MD5:   c294:7d7a:2965:5c32:3dc9:b850:e2e5:0d9a
| SHA-1: 9d44:6d3d:5fb6:252c:da8b:3dd1:b5a2:aeb3:1e4b:5534
| -----BEGIN CERTIFICATE-----
| //
|_-----END CERTIFICATE-----
| tls-alpn: 
|   http/1.1
|   http/1.0
|_  http/0.9
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 04:15
Completed NSE at 04:15, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 04:15
Completed NSE at 04:15, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 04:15
Completed NSE at 04:15, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 70.23 seconds
           Raw packets sent: 68573 (3.017MB) | Rcvd: 66182 (2.647MB)
```

Als we gaan surfen naar de https://sorcery.htb site, dan komen we op een login page terecht. 

![[Pasted image 20250614212220.png]]

Als ik daar op de "our repo" link klik dan wordt ik geredirect naar het git.sorcery.htb subdomain. Als ik nu zal gaan kijken op deze pagina dan kan je zien dat er 1 issue is. Binnen de issue heeft de owner van deze gitpage gezet dat "The backend was exposed to database statement injection. Most calls have already been replaced with safe ones. We have to do the same for the remaining ones." We zullen dus aan SQL Injection kunnen gaan doen. Onderaan de pagina kan je zien dat deze powered is by Gitea Version 1.22.1

![[Pasted image 20250614213808.png]]

Ik ben ook gaan kijken in de commit state, hier staat de final commit van de Git user. Als we in de code base gaan kijken, heb ik de user tom_summers geidentificeerd.

```
All of our employees except one (looking at you, @tom_summers) have passed the test. \
        Unfortunately, Tom has entered their credentials, but our infosec team quickly revoked the access and changed the password. \
```

Wat ook interessant is, is dat ik in de docker-compose.yml file heb gezien dat de private keys in de ftp server worden gestored.

```
volumes:
      - "./ftp/pub:/var/ftp/pub"
      - "./certificates/generated/RootCA.crt:/var/ftp/pub/RootCA.crt"
      - "./certificates/generated/RootCA.key:/var/ftp/pub/RootCA.key"
```

Voor de rest heb ik niets anders meer gezien dat interessant kan zijn. Ik ben dus weer naar de registration page geweest en heb ik me proberen registreren. Je hebt een Regristration key nodig maar dit maakt niet uit wat de Registration Key is. Dan krijg je de volgende request code:

```
POST /auth/register HTTP/1.1

Host: sorcery.htb

Content-Length: 19

Sec-Ch-Ua-Platform: "Linux"

Next-Action: cc5a75671722b7fa3634cb7cc01d2022f9d19e5b

Accept-Language: en-US,en;q=0.9

Sec-Ch-Ua: "Not.A/Brand";v="99", "Chromium";v="136"

Sec-Ch-Ua-Mobile: ?0

Next-Router-State-Tree: %5B%22%22%2C%7B%22children%22%3A%5B%22auth%22%2C%7B%22children%22%3A%5B%22register%22%2C%7B%22children%22%3A%5B%22__PAGE__%22%2C%7B%7D%2C%22%2Fauth%2Fregister%22%2C%22refresh%22%5D%7D%5D%7D%5D%7D%2Cnull%2Cnull%2Ctrue%5D

User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/136.0.0.0 Safari/537.36

Accept: text/x-component

Content-Type: text/plain;charset=UTF-8

Origin: https://sorcery.htb

Sec-Fetch-Site: same-origin

Sec-Fetch-Mode: cors

Sec-Fetch-Dest: empty

Referer: https://sorcery.htb/auth/register

Accept-Encoding: gzip, deflate, br

Priority: u=1, i

Connection: keep-alive



["Test","test","1"]
```

Response code

```
HTTP/1.1 200 OK

Server: nginx/1.27.1

Date: Sat, 14 Jun 2025 20:35:05 GMT

Content-Type: text/x-component

Connection: keep-alive

Vary: RSC, Next-Router-State-Tree, Next-Router-Prefetch, Accept-Encoding

Cache-Control: s-maxage=1, stale-while-revalidate

x-action-revalidated: [[],0,0]

x-nextjs-cache: HIT

X-Powered-By: Next.js

Content-Length: 334



0:["$@1",["eMXTkHuLPViqV0QpNTSCV",null]]
1:{"result":{"token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpZCI6IjA3ZTdlMDIyLTI2YzgtNDkwOS1hZTdjLTJkYzA3OWE1YzZjNiIsInVzZXJuYW1lIjoidGVzdCIsInByaXZpbGVnZUxldmVsIjowLCJ3aXRoUGFzc2tleSI6ZmFsc2UsIm9ubHlGb3JQYXRocyI6bnVsbCwiZXhwIjoxNzUwMDE5NzA1fQ.deQk8zUMdVuvqUhDR04sY-RgvJylHvbg1i4Pio2cDxw"}}

```

Nu kan ik mij gaan inloggen op de pagina en kom je op het volgende dashboard terecht.

![[Pasted image 20250614223633.png]]

Maar dit valt op een dood spoor. Ik zal iets anders moeten proberen. We gaan aan JWT tampering moeten doen. (UITLEGGEN WAT JWT IS EN WAARVOOR HET GEBRUIKT WORDT)

We gaan dus als eerst de JWT pakken van de user waarmee we geregistreerd zijn. We zullen deze daarna naar de decoder in burpsuite sturen. Als eerst zullen we deze gaan decoderen als base64 en krijgen we dus te zien dat het de user test is. Als we hiervan de naam veranderen naar Admin en deze zullen gaan encoderen als de base64 dan zullen we een andere JWT hebben. 

![[Pasted image 20250614235728.png]]

Nu zal ik de JWT_tool gaan gebruiken voor deze key te achterhalen 