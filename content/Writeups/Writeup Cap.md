
Wat we als eerste zulllen gaan doen is een scan op het netwerk voor het vinden van alle open poorten. Dit zullen we gaan doen aan de hand van het volgende commando.

```
nmap -p- -sCV 10.129.103.235 -vvvv
```

![image-20241026-110348.png](50397185.png)

We krijgen te zien dat er 3 open poorten gevonden zijn. Dit zijn de volgende poorten die open staan en wat hun doen:

```
- Poort 22 → SSH connection → Kijken voor een exploit voor de versie OpenSSH 8.2p1
```
    

![image-20241026-110827.png](50298887.png)

```
- Poort 80 → Http connectie → We kunnen aan de hand van het IP address naar de webserver gaan.
    
```

![image-20241026-111130.png](50331668.png)

```
- Poort 21 → ftp connectie → We kunnen aan de hand van een user gaan inloggen op de ftp server.
```
    

![[Pasted image 20250122145613.png]]

Als we op onze webbrowser gaan, Zullen we daar het ip address gaan ingeven. We zullen zien dat we op een dashboard terecht komen.

![image-20241026-111326.png](50364430.png)

We zullen aan de hand van het FFUF commando eens gaan kijken welke subdirectories er allemaal beschikbaar zijn. Dit zullen we gaan doen aan de hand van het volgende commando:

```
ffuf -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://10.129.103.235:80/FUZZ -c
```

![image-20241026-120611.png](50364440.png)

Daarna ben ik een fuzz gaan doen op de subdirectory data. dit heb ik gedaan door het volgende commando:

```
ffuf -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -u http://10.129.103.235/data/FUZZ -c
```

![image-20241026-120522.png](50331684.png)

Hier uit heb ik gezien dat er verschillende sub subdirectories zijn onder de subdirectory data. Deze allemaal is gaan bekijken en ik heb gezien dat er in de sub subdirectory 6,7 en 8 data in zat. Ik zal nu eens gaan kijken wat er allemaal in deze files zit aan de hand van wireshark.

```
File 8 → geen gevoelige data in gevonden.

File 7 → geen gevoelige data in gevonden.

File 6 → geen gevoelige data in gevonden.
```

Dit is enorm raar dat ik in ze alle 3 niet heb gevoden van gevoelige data, Daarom heb ik nog eens FUZZ uitgevoerd en hierbij kreeg ik nog een extra sub subdirectory te zien. Deze sub subdirectory is 0.

**In de 0 hebben we gevoelige data kunnen vinden.**

Als we deze in de wireshark zullen we gaan zien dat er een succesvolle login is geweest naar de ftp server. Dit is gedaan geweest met de login credentials:

**name: nathan**

**password: Buck3tH4TF0RM3!**

![image-20241026-122330.png](50298913.png)

Als we nu gaan proberen om een verbinding te maken met de ftp server, zulllen we dit moeten gaan doen aan de hand van de volgende login credentials:

**name: nathan**

**password: Buck3tH4TF0RM3!**

![image-20241026-123145.png](50331698.png)

Als we hier het ls commando zullen gaan gebruiken zullen we gaan zien dat er een bestand user.txt bestaat.

![image-20241026-123224.png](50397225.png)

voor dit bestand te kunnen zien zullen we deze eerst moeten gaan downloaden naar onze eigen machine, Dit zullen we gaan doen aan de hand van het volgende commando:

```
get user.txt --> Dit betekend voor een specifieke file

of

mget * --> Dit betekend voor alle files 

```
eens deze file gedownload is dan kunnen we de eerste flag zien:

![image-20241026-131123.png](50331719.png)

Daarna ben ik eens gaan opzoeken hoe dat we via de getcap commando ons naar root kunnen brengen. Dit zullen we kunnen doen door het volgende commando uittegaan voeren.

```
getcap -r / 2>/dev/null
```

This output indicated that `python3.8` had the capability to change the user ID, which could potentially be exploited to gain root privileges.

![image-20241026-130854.png](50298935.png)

Voor het gainen van de root access ben ik dit gaan opzoeken op GTFObins. Door het commando van daar te gebruiken, heb ik root access gekregen op de ssh server.

[https://gtfobins.github.io/gtfobins/python/?source=post_page-----eb9c97f2259c--------------------------------#capabilities](https://gtfobins.github.io/gtfobins/python/?source=post_page-----eb9c97f2259c--------------------------------#capabilities)

Het commando hieronder heb ik gebruikt voor het access van de root shell:

```
python3.8 -c 'import os; os.setuid(0); os.system("/bin/bash")'
```

![image-20241026-130837.png](50331712.png)

Als we nu naar de root directory gaan dan zal je daar een file root.txt zien staan. Dit is onze 2 de flag

![[Pasted image 20250122145836.png]]

