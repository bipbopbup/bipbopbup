#InProgress 
```IP
192.168.190.109
```
# Enumeration

## HTTP 80
apache
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241206180053.png?raw=true)
/docs
Jorani
http://192.168.190.109/docs/install/
http://192.168.190.109/docs/install/iis7/web.config
http://192.168.190.109/docs/install/lighttpd/lighttpd.conf
http://192.168.190.109/docs/install/nginx/default

navigating to a web that doesnt exist redirects us to http://192.168.190.109/session/login
the jorani default creds are bbalet:bbalet


# Exploitation

we search for vulnerabilities and we discover a RCE 
https://raw.githubusercontent.com/Orange-Cyberdefense/CVE-repository/refs/heads/master/PoCs/CVE_Jorani.py

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241206181141.png?raw=true)

we get a pseudo terminal which we will upgrade with an msfvenom reverse shell
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241206181416.png?raw=true)

# PrivEsc

## Manual
as shown in the previous screenshot, we have sudo permissions
we will first get the flag
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241206181504.png?raw=true)
we can run env without password
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241206181618.png?raw=true)

Thus we can execute the following command
```
sudo /usr/bin/env /bin/sh
```
And get root access
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241206181735.png?raw=true)
## Automated

### Peass
### Mimikatz

# Lateral Movement
