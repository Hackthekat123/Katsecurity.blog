
Als eerst ben ik gaan kijken welke poorten er allemaal open staan en welke informatie we eruit zouden kunnen halen. Dit heb ik gedaann aan de hand van het volgende commando:

```
nmap -p- -sCV 10.129.183.247 -vvvv
```

![image-20240807-163854.png](27131918.png)

Volgende poorten die openstonden:

![image-20240807-163834.png](27164676.png)

Op poort 80 zien we een FQDN staan. Deze zullen we moeten gaan toevoegen aan de host file. Dit zullen we gaan doen als volgt:

- We gaan aan de hand van het volgende commando de hosts file gaan editten.
    

```
sudo nano /etc/hosts
```

![image-20240807-164325.png](27197441.png)

- Eens dat we in de hosts file zitten zullen we het ip address en de FQDN eraan toevoegen, Zullen we het document opslagen en exiten.
    

![image-20240807-164405.png](27131926.png)

Eens dat deze gegevens toegevoegd zijn aan de hosts file kunnen we eensgaan kijken op de webserver of dat we kunnen surfen naar de webpagina.

Nu zien we dat we de webpagina nu wel kunnen bereiken.

![image-20240807-164708.png](27099141.png)

Als we gaan zoeken op de website maar op poort 3000, omdat deze daarop gehost wordt zullen we de volgende pagina gaan krijgen.

![image-20240807-175210.png](27328513.png)

Als we in deze pagina op explore gaan drukken zullen we een user greenadmin te zien krijgen.

![image-20240807-175246.png](27295747.png)

Binnen deze users krijgen we een aantal gegevens te zien waarbij de password te vinden is in het path hieronder:

```
data/setting/pass.php --> pass.php zal het ww daar staan
```

![image-20240807-175458.png](27328521.png)

Zoals we hieronder zullen zien, zien we het ww dat de gebruiker greenadmin gebruikt voor in de pagina in teloggen die je hieronder te zien krijgt.

![image-20240807-175604.png](27295755.png)![image-20240807-175724.png](27262979.png)

We zullen als eerst nu het password gaan omzetten naar leesbare data Dit zullen we gaan doen aan de hand van een online tool “CrackStation“. Zoals je hieronder zult kunnen zien, is het ww als volgt.

[https://crackstation.net/](https://crackstation.net/)

![image-20240807-175922.png](27328531.png)

Als we deze nu in de pagina gaan invullen zal je zien dat we hebben kunnen inloggen als administrator.

![image-20240807-175957.png](27230222.png)

We zullen later een module moeten gaan uploaden waarbij we een reversed sheel connectie kunnen gaan opstarten met de machine.

Hiervoor zullen we als eerst een script moeten downloaden waarmee we een reversed shell connection zouden kunnen configureren en daarna zullen gaan toevoegen aan de modules van onze webpagina.

Voor een reversed shell connectie heb ik de file van de volgende github pages gehaald.

[https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php?source=post_page-----e87e1cc07864--------------------------------](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php?source=post_page-----e87e1cc07864--------------------------------)

We zullen het ip address moeten veranderen ion het scriptje en dan kunnen we deze gaan zippen en daarna kunnen we de file gaan uploaden.

- Veranderen van de IP address
    

sudo nano xxx.php = De file waarin we de IP zullen moeten veranderen

![image-20240807-181705.png](27328541.png)

- Nu zullen we dit bestan moeten opzetten naar een zip bestand.
    

![image-20240807-181946.png](27230231.png)

- file dat we zonet hebben aangemaakt gaan uploaden op de website, daarna op een andere tablat naar de path gaan dat hieronder beschreven is:
    

http://greenhorn.htb/data/modules/(naam zip file die net aangemaakt is geweest)/(naam PhP file dat net aangemaakt is geweest)

![image-20240807-194639.png](27328560.png)

- connection starten met de poort die we zonet hebben meegegeven in de reversed shell file.
    

![image-20240807-194403.png](27295775.png)

Nu kunnen we binnen de user junior de eerste flag gaan openen.

![[Pasted image 20250122152947.png]]

We zien ook dat er een andere file te vinden is binnen de bestanden van deze user. Deze zullen we gaan downloaden aan de hand van volgende commando uittevoeren op de host machine en op de reversed shell.

- host machine
    

```
wget http://10.129.183.247:8000/'Using OpenVAS.pdf'
```

- reversed shell machine
    

```
pyhton3 -m http.server
```

als we deze file gaan openen op de host machine krijgen we volgende informatie te zien.

![image-20240807-195331.png](27328569.png)

Als je op het internet zal gaan zoeken naar een tool waarmee we blured informatie kunnen gaan omzetten naar zichtbare tekst, Zullen we gaan uitkomen op de tool depix. Hiermee kunnen we de data weer gaan omzetten naar zichtbare en leesbare tekst.

[https://github.com/spipm/Depix?source=post_page-----e87e1cc07864--------------------------------](https://github.com/spipm/Depix?source=post_page-----e87e1cc07864--------------------------------)

Als we de readme gaan checken van deze tool zullen we de volgende informatie te zien krijgen. Dit is hoe we de image weer zullen zetten in een zichtbare tekst.

![image-20240807-195824.png](27295788.png)

Aan de hand van de tekst die we zonet hebben gedeblured kunnen we gaan aanloggen met de user root. Dit zullen we gaan doen aan de hand van dit password.

![[Pasted image 20250122152931.png]]![image-20240807-200158.png](27230257.png)

Als we dan zullen navigeren naar de root subdirectory dan zullen we zien dat we daar de root flag hebben staan. Als we deze zullen gaan openen aan de hand van het cat commando, krijgen we de volgende informatie te zien.

![image-20240807-200321.png](27230264.png)
