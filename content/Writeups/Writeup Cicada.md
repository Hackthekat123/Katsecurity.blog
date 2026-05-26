
Als eerst heb ik de poorten gescanned en heb ik gezien dat er 13 poorten open waren

![image-20250105-165359.png](77168662.png)

Aan de volgende foto kan je zien dat dit een Active Directory box is met een domain controller.

![[Pasted image 20250122150554.png]]

We gaan gaan enumeraten op de smb server. Dit zullen we gaan doen aan de hand van het smbmap commando. Hiermee kunnen we gaan zien op welke map we met de user kunnen gaan connecteren of niet en welke permissions we op deze map hebben.

Dit heb ik gedaan aan de hand van het volgende commando:

```
smbmap -H 10.129.6.130 -u ananymous
```

![image-20250105-170029.png](77168673.png)

We zullen nu gaan connecteren met de smb server. Dit zullen we gaan doen aan de hand van het volkgende commando

```
smbclient \\\\10.129.6.130\\HR -U "anonmyous"
```

Als we gaan kijken naar wat er nu in de subdirectory HR zit zullen we gaan zien dat er een document in deze directory is. We zullen dit document naar onze eigen machine gaan downloaden aan de hand van het get commando:

```
get "Notice from HR.txt"
```

![image-20250105-170239.png](77135912.png)

Als we in het zonet bekeken document gaan kijken, zien we een password. Dit is het default password van een user die we straks zullen gaan achterhalen.

![image-20250105-170918.png](77168682.png)

We zullen nu gaan enumeraten op de users. De enumeraten van de users gaat als volgt:

```
nxc smb 10.129.6.130 -u 'anonymous' -p '' --rid-brute
```

![image-20250105-171049.png](76873758.png)![image-20250105-171119.png](77070367.png)

Voor een mooi overzicht te hebben van alle users heb ik deze in een aparte file users.txt gezet.

![image-20250105-171223.png](76873764.png)

Nu gaan we zoeken achter welke user het password is van defile die we zonet in de Notice \ from\ HR.txt file hebben gevonden. Dit zullen we gaan doen aan de hand van het volgende commando.

```
nxc smb 10.129.6.130 -u users.txt -p 'Cicada$M6Corpb*@Lp#nZp!8'
```

Deze code heb ik gevonden door samen te werken met chatgpt.

![image-20250105-171601.png](77168693.png)

Zoals je hierboven kunt zien is het de user michael dat overeenkomt met het zonet gevonden wachtwoord. Als we nu is gaan zoeken naar de gebruikerslijsten die de user kan gaan zien op de SMB server. Waarmee we dit zullen gaan doen door het volgende commando te gaan gebruiken.

```
nxc smb 10.129.6.130 -u 'michael.wrightson' -p 'Cicada$M6Corpb*@Lp#nZp!8' --users
```

We krijgen hierbij dus het password van een user te zien die dat we daarstraks hadden genumerate van de SMB server.

![image-20250105-172136.png](77135922.png)

Ik ben nu aan de hand van de gegevens dat ik nu wist van david is gaan kijken op welke subdirectories deze user kon gaan readen of writen van de SMB Server.

Hierbij kan je zien dat de user tot 5 disk read rechten heeft.

![image-20250105-172310.png](76873774.png)

Ik zal als eerst een keer een smbclient gaan starten naar de DEV disk. Dit is gewoon omdat ik van boven naar benedan zal te werk gaan. Zoals je op de foto hieronder zult kunnen zien is er een powershell script gevonden. Dit script zal ik gaan downloaden naar mijn eigen machine. Dit zal ik ook weer gaan doen aan de hand van het get commando.

![image-20250105-172503.png](77135929.png)

Als we deze powershell file zullen gaan bekijken met het cat commando zullen we daar een username en een password zullen zien staan die dat we weer kunnen gaan gebruiken voor een evil-winrm te kunnen gaan opstarten naar de machine van emily de gevonden user van het powershell script file.

![image-20250105-172853.png](77070378.png)

Met het volgende commando maak je een verbinding met een Windows-systeem via de Windows Remote Management (WinRM)-service, een standaard Windows-service die beheer op afstand mogelijk maakt.

```
evil-winrm -i 10.129.6.130 -u 'emily.oscars' -p 'Q!3@Lp#M6b*7t*Vt'
```

![image-20250105-173146.png](77070384.png)

Als ik nu zal gaan navigeren naar de Desktop, dan zal je daar de eerste flag hebben.

user flag: 13745a6d63034d45218865ca910600cb

![image-20250105-173249.png](77168709.png)

Nu moeten we onze privileges escaleren. De eerste gebruikelijke manier om dit te controleren is door het commando whoami /priv te gebruiken om te kijken of er privileges zijn die we kunnen misbruiken.

![image-20250105-173425.png](76873785.png)

Ik ben vanaf daar gaan zoeken naar de SeBackupPrivilege Of dat er hiervoor geen methode of exploit te vinden is.

Dan heb ik deze link gevonden:

[https://www.hackingarticles.in/windows-privilege-escalation-sebackupprivilege/](https://www.hackingarticles.in/windows-privilege-escalation-sebackupprivilege/)

Vanaf hier volg je de exploit en dan kom je uiteindelijk in de administrator zijn shell en hebje de root user behaald.

root flag: 02c3aec52605511c1f84009dba8060ea

![image-20250105-173813.png](77168719.png)

![image-20250105-173840.png](77135940.png)
