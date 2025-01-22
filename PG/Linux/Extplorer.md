#Pwned! 
```IP
192.168.168.16
```
## Enumeration
## Nmap
As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 192.168.168.16 -vvv -p- -oN enum/full
```
The output being:
```
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 61 OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 98:4e:5d:e1:e6:97:29:6f:d9:e0:d4:82:a8:f6:4f:3f (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCmPOfERLKCxx+ufQz7eRTNuEEkJ+GX/hKPNPpCWlTiTgegmjYoXQ7MA5ibTRoJ6vxpPEggzNszJKbBrSVAbRuT2sBg4o7ywiGUy7vsDBpObMrBMsdKuue3gpkaNF8DL2pB3v/XAxtavq1Mh4vz4yj99cc2pX1GhSjpQTWlsK8Rl9DmBKp7t0XxEWwq3juQ9JiN5yAttMrbTDjwMNxcipsYv0pMudDBE6g4gQyiZGwuUfBn+HirxnfRr7KkxmBaEpZgukXSJ7fXYgpQVgNP2cvd2sy/PYe0kL7lOfYwG/DSLWV917RPIdsPPQYr+rqrBL7XQA2Qll30Ms9iAX1m9S6pT/vkaw6JQCgDwFSwPXrknf627jCS7vQ8mh8UL07nPO7Hkko3fnHIcxyJggi/BoAAi3GseOl7vCZl28+waWlNdbR8gaiZhDR1rLvimcm3pg3nv9m+0qfVRIs9fxq97cOEFeXhaGHXvQL6LYGK14ZG+jVXtPavID6txymiBOUsj8M=
|   256 57:23:57:1f:fd:77:06:be:25:66:61:14:6d:ae:5e:98 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBAweAzke7+zPt3Untb06RlI4MEp+vsEJICUG+0GgPMp+vxOdxEhcsVY0VGyuC+plTRlqNi0zNv1Y0Jj0BYRMSUw=
|   256 c7:9b:aa:d5:a6:33:35:91:34:1e:ef:cf:61:a8:30:1c (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIPJP5z2Scxa02tfhI1SClflg5QtVdhMImHwY7GugVtfY
80/tcp open  http    syn-ack ttl 61 Apache httpd 2.4.41 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

## HTTP 80
We are redirected to a wordpress database connetion functionality
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241126105319.png?raw=true)
fuzzing we can see the following directory
http://192.168.168.16/filemanager/
which we can access with admin:admin
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241126120234.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241126120453.png?raw=true)


# Exploitation
upload reverse shell in php and execute
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241126122826.png?raw=true)
we can also crack dora's hash
```
john --wordlist=/usr/share/wordlists/rockyou.txt files/dora.hash
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241126122854.png?raw=true)
```
doraemon
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241126123017.png?raw=true)
# PrivEsc

## Manual

we are part of disk group, so we can abuse debugfs to read /etc/shadow
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241126124350.png?raw=true)
and we crack root hash
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241126124405.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241126124440.png?raw=true)

## Automated

### Peass
### Mimikatz

# Lateral Movement

