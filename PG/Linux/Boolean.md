#InProgress 
```IP
192.168.180.231
```
# Enumeration
## SSH
rsa
ecdsa

## HTTP 80
redirects to login page
Other directories
register
we need email confirmation

## HTTP 33017
Development
apache http server??
```
whatweb http://192.168.180.231:33017
```
Apache 2.4.38
## 3000


# Exploitation
we can alter the data by sending parameter confirmed=True
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241120115231.png?raw=true)
## LFI
```
http://192.168.180.231/?cwd=../../../../../etc/&file=passwd&download=true
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241120122332.png?raw=true)
 we can path traversal to remi ssh directory
 ![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241120133539.png?raw=true)
 upload here a public key named authorized_keys created with the following command:
 ![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241120133620.png?raw=true)
 and we are in

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241120133518.png?raw=true)
# PrivEsc

## Manual
Doesnt seem possible to exploit CVE-2019-13272
https://github.com/jas502n/CVE-2019-13272/tree/master

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241120140346.png?raw=true)

we have root keys in .ssh/keys/root
```
ssh root@127.0.0.1 -i .ssh/keys/root
```
we are root
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241120173339.png?raw=true)

## Automated

### Peass
active ports
```
127.0.0.1:3306
127.0.0.1:3000          0.0.0.0:*               LISTEN      847/puma 5.6.5 (tcp
```
ptrace protection is disabled (0), so sudo tokens could be abused
useful software
```
/usr/bin/nc.traditional
```
mysql  Ver 15.1 Distrib 10.3.36-MariaDB, for debian-linux-gnu (x86_64) using readline 5.2
uncommon password files
```
/etc/pam.d/passwd
/etc/passwd
/usr/share/bash-completion/completions/passwd
/usr/share/lintian/overrides/passwd
```
SUID
```
/usr/bin/chfn  --->  SuSE_9.3/10
```
.sh files in path
```
/usr/bin/gettext.sh
```

ps aux
```
puma 5.6.5 (tcp://127.0.0.1:3000) [boolean]
```
### Mimikatz

# Lateral Movement
