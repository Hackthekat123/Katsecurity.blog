# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌──(kali㉿kali)-[~/HTB/Eureka]
└─$ nmap -p- 10.129.140.129
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-28 19:09 CEST
Nmap scan report for 10.129.140.129
Host is up (0.030s latency).
Not shown: 65532 closed tcp ports (reset)
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
8761/tcp open  unknown

```

### Detailed port scan

At the detailed port scan go to get more information from the services which are open on the ip address.

```
┌──(kali㉿kali)-[~/HTB/Eureka]
└─$ nmap -p22,80,8761 -sCV 10.129.140.129 
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.12 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 d6:b2:10:42:32:35:4d:c9:ae:bd:3f:1f:58:65:ce:49 (RSA)
|   256 90:11:9d:67:b6:f6:64:d4:df:7f:ed:4a:90:2e:6d:7b (ECDSA)
|_  256 94:37:d3:42:95:5d:ad:f7:79:73:a6:37:94:45:ad:47 (ED25519)
80/tcp   open  http    nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://furni.htb/
8761/tcp open  http    Apache Tomcat (language: en)
| http-auth: 
| HTTP/1.1 401 \x0D
|_  Basic realm=Realm
|_http-title: Site doesn't have a title.
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Nu zal ik het domeinnaam furni.htb in de hosts file gaan zetten. Nu dat de domeinnaam gekent is in de hosts file, kunnen we gaan kijken wat er te zien is op de webserver dat wordt gedraaid op poort 80. Ik ben gaan zien op de website en ik heb gezien dat je een eigen account kunt gaan registreren. 

![[Pasted image 20250828192032.png]]
Nu zal ik mij gaan inloggen door gebruik te maken van het zojuist geregistreerde account. maar er was niets te vinden. Ik zal gaan scannen of dat er vulnerabilities zijn. Dit zal ik gaan doen door gebruik te maken van nuclei.

```
┌──(kali㉿kali)-[~/HTB/Eureka]
└─$ nuclei -u http://furni.htb

[INF] nuclei-templates are not installed, installing...
[INF] Successfully installed nuclei-templates at /home/kali/.local/nuclei-templates
[WRN] Found 1 templates with syntax error (use -validate flag for further examination)
[INF] Current nuclei version: v3.4.10 (latest)
[INF] Current nuclei-templates version: v10.2.7 (latest)
[INF] New templates added in latest release: 55
[INF] Templates loaded for current scan: 8263
[INF] Executing 8060 signed templates from projectdiscovery/nuclei-templates
[WRN] Loading 203 unsigned templates for scan. Use with caution.
[INF] Targets loaded for current scan: 1
[INF] Templates clustered: 1777 (Reduced 1671 Requests)
[INF] Using Interactsh Server: oast.online
[external-service-interaction] [http] [info] http://furni.htb
[missing-sri] [http] [info] http://furni.htb ["https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css"]
[springboot-heapdump] [http] [critical] http://furni.htb/actuator/heapdump
[waf-detect:nginxgeneric] [http] [info] http://furni.htb
[ssh-sha1-hmac-algo] [javascript] [info] furni.htb:22
[ssh-auth-methods] [javascript] [info] furni.htb:22 ["["publickey","password"]"]
[ssh-password-auth] [javascript] [info] furni.htb:22
[ssh-server-enumeration] [javascript] [info] furni.htb:22 ["SSH-2.0-OpenSSH_8.2p1 Ubuntu-4ubuntu0.12"]
[openssh-detect] [tcp] [info] furni.htb:22 ["SSH-2.0-OpenSSH_8.2p1 Ubuntu-4ubuntu0.12"]
[composer-config:composer.lock] [http] [info] http://furni.htb/composer.json
[composer-config:composer.lock] [http] [info] http://furni.htb/composer.lock
[composer-config:composer.lock] [http] [info] http://furni.htb/.composer/composer.json
[composer-config:composer.lock] [http] [info] http://furni.htb/vendor/composer/installed.json
[springboot-env] [http] [low] http://furni.htb/actuator/env
[form-detection] [http] [info] http://furni.htb
[springboot-beans] [http] [low] http://furni.htb/actuator/beans
[springboot-conditions] [http] [low] http://furni.htb/actuator/conditions
[springboot-threaddump] [http] [low] http://furni.htb/actuator/threaddump
[springboot-configprops] [http] [low] http://furni.htb/actuator/configprops
[springboot-scheduledtasks] [http] [info] http://furni.htb/actuator/scheduledtasks
[springboot-caches] [http] [low] http://furni.htb/actuator/caches
[options-method] [http] [info] http://furni.htb ["GET,HEAD,OPTIONS"]
[springboot-loggers] [http] [low] http://furni.htb/actuator/loggers
[springboot-features] [http] [low] http://furni.htb/actuator/features
[fingerprinthub-web-fingerprints:openfire] [http] [info] http://furni.htb
[tech-detect:font-awesome] [http] [info] http://furni.htb
[tech-detect:bootstrap] [http] [info] http://furni.htb
[tech-detect:nginx] [http] [info] http://furni.htb
[http-missing-security-headers:cross-origin-opener-policy] [http] [info] http://furni.htb
[http-missing-security-headers:cross-origin-resource-policy] [http] [info] http://furni.htb
[http-missing-security-headers:strict-transport-security] [http] [info] http://furni.htb
[http-missing-security-headers:content-security-policy] [http] [info] http://furni.htb
[http-missing-security-headers:referrer-policy] [http] [info] http://furni.htb
[http-missing-security-headers:cross-origin-embedder-policy] [http] [info] http://furni.htb
[http-missing-security-headers:permissions-policy] [http] [info] http://furni.htb
[http-missing-security-headers:x-permitted-cross-domain-policies] [http] [info] http://furni.htb
[http-missing-security-headers:clear-site-data] [http] [info] http://furni.htb
[INF] Scan completed in 1m. 37 matches found.
```

Hier kan je zien dat er een critical vulnerability is. De critical vuln is heapdump. De exploit dat ik gevonden heb is de volgende url. https://github.com/whwlsfb/JDumpSpider

```
┌──(kali㉿kali)-[~/HTB/Eureka/JDumpSpider]
└─$ jmap -dump:live,format=b,file=myheap.hprof 113376
Dumping heap to /home/kali/HTB/Eureka/JDumpSpider/myheap.hprof ...
Heap dump file created [4513278 bytes in 0.013 secs]

```