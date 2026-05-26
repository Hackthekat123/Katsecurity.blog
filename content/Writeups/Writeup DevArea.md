# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌─[eu-dedivip-2]─[10.10.15.32]─[hackthekat123@htb-sdm7jazhke]─[~]
└──╼ [★]$ nmap -p- 10.129.244.208
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-05-18 13:38 CDT
Nmap scan report for 10.129.244.208
Host is up (0.0100s latency).
Not shown: 65529 closed tcp ports (reset)
PORT     STATE SERVICE
21/tcp   open  ftp
22/tcp   open  ssh
80/tcp   open  http
8080/tcp open  http-proxy
8500/tcp open  fmtp
8888/tcp open  sun-answerbook
```
### Detailed port scan

At the detailed port scan go to get more information from the services which are open on the IP address.

```
┌─[eu-dedivip-2]─[10.10.15.32]─[hackthekat123@htb-sdm7jazhke]─[~]
└──╼ [★]$ nmap -p21,22,80,8080,8500,8888 -sCV 10.129.244.208
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-05-18 13:40 CDT
Nmap scan report for 10.129.244.208
Host is up (0.0080s latency).

PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 3.0.5
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.10.15.32
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 4
|      vsFTPd 3.0.5 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxr-xr-x    2 ftp      ftp          4096 Sep 22  2025 pub
22/tcp   open  ssh     OpenSSH 9.6p1 Ubuntu 3ubuntu13.15 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 83:13:6b:a1:9b:28:fd:bd:5d:2b:ee:03:be:9c:8d:82 (ECDSA)
|_  256 0a:86:fa:65:d1:20:b4:3a:57:13:d1:1a:c2:de:52:78 (ED25519)
80/tcp   open  http    Apache httpd 2.4.58
|_http-title: Did not follow redirect to http://devarea.htb/
|_http-server-header: Apache/2.4.58 (Ubuntu)
8080/tcp open  http    Jetty 9.4.27.v20200227
|_http-title: Error 404 Not Found
|_http-server-header: Jetty(9.4.27.v20200227)
8500/tcp open  fmtp?
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.0 500 Internal Server Error
|     Content-Type: text/plain; charset=utf-8
|     X-Content-Type-Options: nosniff
|     Date: Mon, 18 May 2026 18:41:03 GMT
|     Content-Length: 64
|     This is a proxy server. Does not respond to non-proxy requests.
|   GenericLines, Help, Kerberos, RTSPRequest, SSLSessionReq, TLSSessionReq, TerminalServerCookie: 
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|     Request
|   GetRequest, HTTPOptions: 
|     HTTP/1.0 500 Internal Server Error
|     Content-Type: text/plain; charset=utf-8
|     X-Content-Type-Options: nosniff
|     Date: Mon, 18 May 2026 18:40:38 GMT
|     Content-Length: 64
|_    This is a proxy server. Does not respond to non-proxy requests.
8888/tcp open  http    Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
|_http-title: Hoverfly Dashboard
```

## Login Anonymous on FTP server

Ik heb gezien dat je kon inloggen als anonymous user op de FTP Server. Eens als ik ingeloged was op de FTP server, kan je zien dat er een `Pub` Directory bestaat. Binnen deze directory kan je de file `employee-service.jar` vinden. Ik ben deze gaan Downloaden naar de eigen machine door het `Get` command te gebruiken.

```
┌─[eu-dedivip-2]─[10.10.15.32]─[hackthekat123@htb-sdm7jazhke]─[~]
└──╼ [★]$ ftp 10.129.244.208
Connected to 10.129.244.208.
220 (vsFTPd 3.0.5)
Name (10.129.244.208:root): anonymous
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||44136|)
150 Here comes the directory listing.
drwxr-xr-x    2 ftp      ftp          4096 Sep 22  2025 pub
226 Directory send OK.
ftp> cd pub
250 Directory successfully changed.
ftp> ls -la
229 Entering Extended Passive Mode (|||45922|)
150 Here comes the directory listing.
drwxr-xr-x    2 ftp      ftp          4096 Sep 22  2025 .
drwxr-xr-x    3 ftp      ftp          4096 Sep 22  2025 ..
-rw-r--r--    1 ftp      ftp       6445030 Sep 22  2025 employee-service.jar
226 Directory send OK.
ftp> get employee-service.jar
local: employee-service.jar remote: employee-service.jar
229 Entering Extended Passive Mode (|||44262|)
150 Opening BINARY mode data connection for employee-service.jar (6445030 bytes).
100% |*************************************************|  6293 KiB   15.10 MiB/s    00:00 ETA
226 Transfer complete.
```

### Extract JAR File

Ik ben de `employee-service.jar` file gaan extracten. Dit kan je doen door het volgende commando te gebruiken.

```
┌─[eu-dedivip-2]─[10.10.15.32]─[hackthekat123@htb-sdm7jazhke]─[~/DevArea]
└──╼ [★]$ ls -la
total 6304
drwxr-xr-x  2 hackthekat123 hackthekat123    4096 May 18 13:45 .
drwx------ 25 hackthekat123 hackthekat123    4096 May 18 13:45 ..
-rw-r--r--  1 hackthekat123 hackthekat123 6445030 May 18 13:45 employee-service.jar
┌─[eu-dedivip-2]─[10.10.15.32]─[hackthekat123@htb-sdm7jazhke]─[~/DevArea]
└──╼ [★]$ jar -xvf employee-service.jar
```

