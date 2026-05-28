# Machine Information

As is common in real life pentests, you will start the Pirate box with credentials for the following account pentest / 'p3nt3st2025!&'
# Initial Enumeration
## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌─[eu-dedivip-2]─[10.10.14.2]─[hackthekat123@htb-f7c3w2euii]─[~]
└──╼ [★]$ nmap 10.129.14.87
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-03-02 14:08 CST
Nmap scan report for 10.129.14.87
Host is up (0.0077s latency).
Not shown: 989 filtered tcp ports (no-response)
PORT     STATE SERVICE
53/tcp   open  domain
80/tcp   open  http
88/tcp   open  kerberos-sec
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
593/tcp  open  http-rpc-epmap
636/tcp  open  ldapssl
2179/tcp open  vmrdp
3268/tcp open  globalcatLDAP
3269/tcp open  globalcatLDAPssl
```
### Detailed port scan

At the detailed port scan go to get more information from the host. 

```
┌─[eu-dedivip-2]─[10.10.14.2]─[hackthekat123@htb-f7c3w2euii]─[~]
└──╼ [★]$ nmap -p53,80,88,135,139,445,593,636,2179,3268,3269 -sCV 10.129.14.87
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-03-02 14:10 CST
Nmap scan report for 10.129.14.87
Host is up (0.0076s latency).

PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
80/tcp   open  http          Microsoft IIS httpd 10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-03-03 03:10:32Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: pirate.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC01.pirate.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:DC01.pirate.htb
| Not valid before: 2025-06-09T14:05:15
|_Not valid after:  2026-06-09T14:05:15
|_ssl-date: 2026-03-03T03:11:53+00:00; +7h00m00s from scanner time.
2179/tcp open  vmrdp?
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: pirate.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC01.pirate.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:DC01.pirate.htb
| Not valid before: 2025-06-09T14:05:15
|_Not valid after:  2026-06-09T14:05:15
|_ssl-date: 2026-03-03T03:11:53+00:00; +7h00m00s from scanner time.
3269/tcp open  ssl/ldap      Microsoft Windows Active Directory LDAP (Domain: pirate.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=DC01.pirate.htb
| Subject Alternative Name: othername: 1.3.6.1.4.1.311.25.1::<unsupported>, DNS:DC01.pirate.htb
| Not valid before: 2025-06-09T14:05:15
|_Not valid after:  2026-06-09T14:05:15
|_ssl-date: 2026-03-03T03:11:53+00:00; +7h00m00s from scanner time.
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 6h59m59s, deviation: 0s, median: 6h59m59s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2026-03-03T03:11:16
|_  start_date: N/A
```

Gegevens van de ldap server gaan halen:

```
┌─[eu-dedivip-2]─[10.10.14.2]─[hackthekat123@htb-f7c3w2euii]─[~]
└──╼ [★]$ nxc ldap 10.129.14.87 -u pentest -p 'p3nt3st2025!&' --bloodhound --collection All --dns-server 10.129.14.87
SMB         10.129.14.87    445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:pirate.htb) (signing:True) (SMBv1:False)
LDAP        10.129.14.87    389    DC01             [+] pirate.htb\pentest:p3nt3st2025!& 
LDAP        10.129.14.87    389    DC01             Resolved collection methods: group, localadmin, container, session, rdp, acl, objectprops, trusts, dcom, psremote
LDAP        10.129.14.87    389    DC01             Done in 00M 04S
LDAP        10.129.14.87    389    DC01             Compressing output into /home/hackthekat123/.nxc/logs/DC01_10.129.14.87_2026-03-02_142428_bloodhound.zip
```

## Interessante information voor straks

```
[*] Trying port 445/tcp
[+] Found policy:
Domain password information:
  Password history length: 24
  Minimum password length: 7
  Maximum password age: 41 days 23 hours 53 minutes
  Password properties:
  - DOMAIN_PASSWORD_COMPLEX: true
  - DOMAIN_PASSWORD_NO_ANON_CHANGE: false
  - DOMAIN_PASSWORD_NO_CLEAR_CHANGE: false
  - DOMAIN_PASSWORD_LOCKOUT_ADMINS: false
  - DOMAIN_PASSWORD_PASSWORD_STORE_CLEARTEXT: false
  - DOMAIN_PASSWORD_REFUSE_PASSWORD_CHANGE: false
Domain lockout information:
  Lockout observation window: 10 minutes
  Lockout duration: 10 minutes
  Lockout threshold: None
Domain logoff information:
  Force logoff time: not set
  
   ===================================
|    Users via RPC on pirate.htb    |
 ===================================
[*] Enumerating users via 'querydispinfo'
[+] Found 7 user(s) via 'querydispinfo'
[*] Enumerating users via 'enumdomusers'
[+] Found 7 user(s) via 'enumdomusers'
[+] After merging user results we have 7 user(s) total:
'1104':
  username: a.white_adm
  name: Angela White
  acb: '0x00040210'
  description: (null)
'3101':
  username: a.white
  name: Angela White
  acb: '0x00000210'
  description: (null)
'4106':
  username: pentest
  name: pentest
  acb: '0x00000210'
  description: (null)
'4110':
  username: j.sparrow
  name: Jack Sparrow
  acb: '0x00000210'
  description: (null)
'500':
  username: Administrator
  name: (null)
  acb: '0x00000210'
  description: Built-in account for administering the computer/domain
'501':
  username: Guest
  name: (null)
  acb: '0x00000215'
  description: Built-in account for guest access to the computer/domain
'502':
  username: krbtgt
  name: (null)
  acb: '0x00020011'
  description: Key Distribution Center Service Account

```