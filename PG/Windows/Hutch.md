#Pwned! 
```IP
192.168.169.122
```
## Nmap
```

```
AS REP Roasting with fmcsorley:CrabSharkJellyfish192
Kerberoasting  with fmcsorley:CrabSharkJellyfish192
doesnt work

## Nmap UDP


# Enumeration

## SMB
## RPC
we dont have much permissions
```
rpcclient -U '' 192.168.169.122 -N
```
## LDAP
we get credentials
fmcsorley:CrabSharkJellyfish192
agitthouse
cluddy
eaburrow
jfrarey
avictoria
jmckendry
oknee
jsparwell
## HTTP 
webdav server
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241114111704.png?raw=true)


![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241114112620.png?raw=true)

we can upload a shell with credentials fmcsorley:CrabSharkJellyfish192
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241114112720.png?raw=true)

# PrivEsc

## Manual
whoami /priv reveals impersonate token so we execute godpotato
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241114113321.png?raw=true)
we get nt authority system
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241114113405.png?raw=true)
## Automated

### Peass
### Mimikatz


