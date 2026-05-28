
# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
nmap -p- 10.129.49.45             
Starting Nmap 7.95 ( https://nmap.org ) at 2025-03-07 11:09 CET
Stats: 0:00:00 elapsed; 0 hosts completed (0 up), 1 undergoing Ping Scan
Ping Scan Timing: About 100.00% done; ETC: 11:09 (0:00:00 remaining)
Nmap scan report for 10.129.49.45
Host is up (0.066s latency).
Not shown: 65526 closed tcp ports (reset)
PORT      STATE SERVICE
22/tcp    open  ssh
80/tcp    open  http
111/tcp   open  rpcbind
2049/tcp  open  nfs
41669/tcp open  unknown
43131/tcp open  unknown
45201/tcp open  unknown
57421/tcp open  unknown
58097/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 8.96 seconds
```
### Detailed port scan

At the gedatialized port scan go to get more information from the services dien are open the ip address.

```
nmap -p22,80,111,2049,41669,43131,45201,57421,58097 -sCV 10.129.49.45 -vvvv
Starting Nmap 7.95 ( https://nmap.org ) at 2025-03-07 11:11 CET
NSE: Loaded 157 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 11:11
Completed NSE at 11:11, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 11:11
Completed NSE at 11:11, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 11:11
Completed NSE at 11:11, 0.00s elapsed
Initiating Ping Scan at 11:11
Scanning 10.129.49.45 [4 ports]
Completed Ping Scan at 11:11, 0.05s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 11:11
Completed Parallel DNS resolution of 1 host. at 11:11, 0.02s elapsed
DNS resolution of 1 IPs took 0.02s. Mode: Async [#: 1, OK: 0, NX: 1, DR: 0, SF: 0, TR: 1, CN: 0]
Initiating SYN Stealth Scan at 11:11
Scanning 10.129.49.45 [9 ports]
Discovered open port 80/tcp on 10.129.49.45
Discovered open port 45201/tcp on 10.129.49.45
Discovered open port 111/tcp on 10.129.49.45
Discovered open port 2049/tcp on 10.129.49.45
Discovered open port 43131/tcp on 10.129.49.45
Discovered open port 58097/tcp on 10.129.49.45
Discovered open port 22/tcp on 10.129.49.45
Discovered open port 57421/tcp on 10.129.49.45
Discovered open port 41669/tcp on 10.129.49.45
Completed SYN Stealth Scan at 11:11, 0.07s elapsed (9 total ports)
Initiating Service scan at 11:11
Scanning 9 services on 10.129.49.45
Completed Service scan at 11:11, 6.50s elapsed (9 services on 1 host)
NSE: Script scanning 10.129.49.45.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 11:11
Completed NSE at 11:11, 1.82s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 11:11
Completed NSE at 11:11, 0.44s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 11:11
Completed NSE at 11:11, 0.00s elapsed
Nmap scan report for 10.129.49.45
Host is up, received echo-reply ttl 63 (0.043s latency).
Scanned at 2025-03-07 11:11:12 CET for 9s

PORT      STATE SERVICE  REASON         VERSION
22/tcp    open  ssh      syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 89:d7:39:34:58:a0:ea:a1:db:c1:3d:14:ec:5d:5a:92 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBO8nDXVOrF/vxCNHYMVULY8wShEwVH5Hy3Bs9s9o/WCwsV52AV5K8pMvcQ9E7JzxrXkUOgIV4I+8hI0iNLGXTVY=
|   256 b4:da:8d:af:65:9c:bb:f0:71:d5:13:50:ed:d8:11:30 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIAjDCjag/Rh72Z4zXCLADSXbGjSPTH8LtkbgATATvbzv
80/tcp    open  http     syn-ack ttl 63 Apache httpd 2.4.52 ((Ubuntu))
|_http-server-header: Apache/2.4.52 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Did not follow redirect to http://clicker.htb/
111/tcp   open  rpcbind  syn-ack ttl 63 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100003  3,4         2049/tcp   nfs
|   100003  3,4         2049/tcp6  nfs
|   100005  1,2,3      43131/tcp   mountd
|   100005  1,2,3      43613/tcp6  mountd
|   100005  1,2,3      57682/udp6  mountd
|_  100005  1,2,3      60912/udp   mountd
2049/tcp  open  nfs      syn-ack ttl 63 3-4 (RPC #100003)
41669/tcp open  mountd   syn-ack ttl 63 1-3 (RPC #100005)
43131/tcp open  mountd   syn-ack ttl 63 1-3 (RPC #100005)
45201/tcp open  nlockmgr syn-ack ttl 63 1-4 (RPC #100021)
57421/tcp open  mountd   syn-ack ttl 63 1-3 (RPC #100005)
58097/tcp open  status   syn-ack ttl 63 1 (RPC #100024)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 11:11
Completed NSE at 11:11, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 11:11
Completed NSE at 11:11, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 11:11
Completed NSE at 11:11, 0.00s elapsed
Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.16 seconds
           Raw packets sent: 13 (548B) | Rcvd: 10 (424B)
```

There we can see that the is Network File System (NFS) ports are open. NFS enables access to remote file shares. Using showmount, we can quickly list available NFS shares. We can use the following command.

```
showmount -e clicker.htb
Export list for clicker.htb:
/mnt/backups *
```

Now we will mount this folder to our own machine by doing the following instructions. First we will make in our folder a now folder where we can mount the folder. 

```
mkdir mount_folder
```

For mounting the folder will we use the following command:

```
sudo mount -t nfs 10.129.49.45:/ ./mount_folder -o nolock
```

