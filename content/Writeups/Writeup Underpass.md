# Nmap

The first step in analyzing UnderPass was network reconnaissance using **Nmap**. This tool was used to identify open ports, services, and potential vulnerabilities. For example, use the following command:

```
nmap -p- -sCV -sU 10.129.188.174 -vvvv
```

- **-sC**: Runs default scripts.
    
- **-sV**: Detects service versions.
    

The scan results revealed the services and ports that required further investigation.

![](AD_4nXdzKh8JQOkQsvbL8ZotJgnbjrtS6RgrRA8kWTJIR9RYbbgyFjGT06esNJoTmGVa1Z0mxhJL4SyCm6V9H38bp8tkq_BR9ukvvZNJSwM-pxOxyXL8UQ08h-abrEu1-q0_gIR1hS0d4w.png)![](AD_4nXcoD3CICnF7TjfNUYBnBpmmBXrlye906dPfbnTvvFs9_sTw2x6_aWQNyeBfuxcDAXMhlACVrprQjoB4NMDgJEtEcK_4ReGuYrNK3-4Gw24FgJwbCkqXIAUwfLOjYxCqp_i38tfw.png)

The Following image below will show the output of an SNMP (Simple Network Management Protocol) walk command executed on a host named `underpass.htb`. The command used is `snmpwalk -v 2c -c public underpass.htb`, which queries the SNMP agent on the target host using SNMP version 2c and the community string `public`. The output displays various Object Identifiers (OIDs) and their corresponding values retrieved from the SNMP agent.

![](AD_4nXfA2k5EK-zfGmlgjLjkZ2mnspJAImFjNvupo8QpFjXU4ZLzykQLQUZ4HrD_vIESELxV9MplZ7vpiSxn23UyVv5i9rWpnZhAj86spbUKN8slGevHfr6enIyG5AAfBHsZAsjjUtCzSg.png)

## Directory Fuzzing

A directory fuzzing approach was used to discover hidden directories and files on the web server. This was done using tools such as **Gobuster** or **FFUF**. Use the following example command:

```
ffuf -u http://underpass.htb/daloradius/FUZZ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -e php,html
```

- **-u**: Specifies the URL of the target website.
    
- **-w**: Points to the wordlist used for brute-forcing.
    
- -e: These are the extensions we're searching for
    

Key findings such as admin panels or configuration files may result from this process.

![](AD_4nXda1eqXKPB_coor-lrdwFscDSwmc32_E4sa0NW_UDlfQEtP-TlbI35zd5UA7HGpSiGhO5tdC7nuw-BP2-H2I8sgqk2JszRYNMvnnz7hgwoNk1GNyj1A0k0QYqILp70SFo3f4burZw.png)

## Default Credentials

During the analysis, default credentials were used to gain access to the administrator interface. The credentials used were:

- **Username:** administrator
    
- **Password:** radius
    

This highlights the importance of changing default credentials to prevent unauthorized access.

![](AD_4nXcAlK9boZm0BQP-7qzZo8Nl0tbQ2xUYf7clUOjV7gluPG4Bb5xoa1hkF_pOp-5x-pIXdZKUAcMiqLzLF1Cqi23JpbJ8SnXDQU9u_ejmhq2Lzk8CMmuvEQGOMg3k4cUsX0KKr9oQVg.png)

Once we are logged in, will we take a look in the User Management. there we will see that there is 1 user active.

![](AD_4nXe3DpCggoRhmTXisCJfHX1T1JVEISpN20KKUgfVMvdai5tBClaLG1NIk9Oz6Ugx1Ci_yuQr9-WTcUzEEivsZ31iBIURx88Naf8GaY-3HpYZ1uxkF4CsGB72vAGQTcjZLMVygg_60g.png)

## Hash Analysis

A hashed value was discovered and then decrypted using **CrackingStation**, an online hash-cracking tool.

1. Enter the hash on the CrackingStation website.
    
2. Retrieve the plaintext of the hash.
    

This process confirmed the vulnerability of poorly secured passwords.

![](AD_4nXd0efOkUpy8nu18fLXxk-ZFOz0RUAJ69RwWQkmmai_qSJzVM4QuAMVKdy15928QaUSQTMooMMzNuN2KRobtK3MpgDC7fj1zNlOBaQF-0XR5WPE40oRcWkQxJAdIxWx39ZDVubyWbQ.png)

## SSH Access

Further analysis allowed logging into the SSH server with the following credentials:

```

- **Username:** svcMosh
    
- **Password:** underwaterfriends
    
```

Access to the SSH server enabled further exploration and analysis of specific services.

![](AD_4nXd3XIHWmuAOaRXeZ_Y8K90gwII-BmjszH4x-GyskJgsch7L7sP_WZHJetf8EfC1XsVI-Jw86aTTE1h9IndjmpSQHfdZ8WTU0S8cjXV2U8ktztx6-ZATOO67QMZtwLy4HHwtXj_7fA.png)

There we have the first flag

If we do the command “sudo -l“ then we can see what command we can run as root.

![](AD_4nXetzkBAh3w2hZ2xEt5ibZ6e3GNtE8LIA79gAvGNxdPbBez_M8Lows-wBfD1uZb2Ie-eW_7YvstQSsj7kapcIdiv5kMcTBghRZGZuEHGfC-Gihm7GETTXXCIxs39oDHY_zZndtiInQ.png)

## Port Configuration for Mosh

Using the **MoshServer** service required specific ports for communication. Configuration and connection were established as follows:

### Command for New Connection

```
sudo mosh-server new -p 60004 <Chosen port> 
```

Replace the port values with the correct range parameters.

This command established a stable connection as an administrator.

![](AD_4nXdgzCUp7FzHHa4BKZFKsiTt9TLZSfXEz3u4WHL2vIOQjZq-fyjIi7gt-QNoWWiqmb3BT7ZDf9Rpw1GyHBeg92lxa0429WnfO3XfKZSata_cHcCkoRHVYgN66wLa5sbXVE1oB7P7.png)

as you can see are we the root user now and did we find the second flag. (Vergeten foto te nemen van de 2de flag.)

![image-20241229-154716.png](72646675.png)
