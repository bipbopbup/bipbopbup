#Pwned! 
```IP
192.168.190.109
```
## Enumeration
## Nmap
As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 192.168.190.109 -vvv -p- -oN enum/full
```
The output being:
```
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 61 OpenSSH 9.6p1 Ubuntu 3ubuntu13.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 76:18:f1:19:6b:29:db:da:3d:f6:7b:ab:f4:b5:63:e0 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBMeGcI7LXAgYpdcxsbgmDh+FrFwBJxUEPxSU4XODxVs1CWLxFnxl1/SZ0ReciCentljLQxi9LqNYvR//3y6kAms=
|   256 cb:d8:d6:ef:82:77:8a:25:32:08:dd:91:96:8d:ab:7d (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAILE9A0DdfM97fpb5q8N9nmI/9/8rqT8ADRWK8KBegxYM
80/tcp open  http    syn-ack ttl 61 Apache httpd 2.4.58 ((Ubuntu))
|_http-trane-info: Problem with XML parsing of /evox/about
| http-methods: 
|_  Supported Methods: POST OPTIONS HEAD GET
| http-robots.txt: 1 disallowed entry 
|_/
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.58 (Ubuntu)
|_http-favicon: Unknown favicon MD5: 96C540E05EFE5C9E11F15DD5CE70BB0F
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
## HTTP 80
apache
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241206180053.png?raw=true)
/docs
Jorani
http://192.168.190.109/docs/install/
http://192.168.190.109/docs/install/iis7/web.config
http://192.168.190.109/docs/install/lighttpd/lighttpd.conf
http://192.168.190.109/docs/install/nginx/default

navigating to a web that doesnt exist redirects us to http://192.168.190.109/session/login
the jorani default creds are bbalet:bbalet


# Exploitation

we search for vulnerabilities and we discover a RCE 
https://raw.githubusercontent.com/Orange-Cyberdefense/CVE-repository/refs/heads/master/PoCs/CVE_Jorani.py

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241206181141.png?raw=true)

we get a pseudo terminal which we will upgrade with an msfvenom reverse shell
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241206181416.png?raw=true)

# PrivEsc

## Manual
as shown in the previous screenshot, we have sudo permissions
we will first get the flag
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241206181504.png?raw=true)
we can run env without password
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241206181618.png?raw=true)

Thus we can execute the following command
```
sudo /usr/bin/env /bin/sh
```
And get root access
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241206181735.png?raw=true)
## Automated

### Peass
### Mimikatz

# Lateral Movement

