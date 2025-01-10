#InProgress 
```IP
192.168.181.42
```
## Nmap
polite nmap or it drops the packages
```
sudo nmap -sCV 192.168.181.42 -oA nmap/clamav -vvv -T2
```
```

```

## Nmap UDP
```
sudo nmap -sU 192.168.181.42 -oA nmap/clamav.udp -vvv --min-rate 10000
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241017121548.png?raw=true)

# Enumeration
## SMTP
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241017111737.png?raw=true)
```
root
admin
administrator
webadmin
sysadmin
netadmin
guest
user
web
test
```
## SMB
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241017113308.png?raw=true)

## HTTP 80
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241017111411.png?raw=true)
```
ifyoudontpwnmeuran00b
```
```header title
Ph33r
```
## SNMP
https://book.hacktricks.xyz/network-services-pentesting/pentesting-snmp/snmp-rce
```
snmpwalk -v2c -c public 192.168.181.42
```
```
snmpbulkwalk -v2c -c public 192.168.181.42 > snmp.bulk
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241017123442.png?raw=true)
```
sudo vim /etc/snmp/snmp.conf
```
Comment mibs line for human readable info instead of IDs
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241017125642.png?raw=true)
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241017131007.png?raw=true)
https://www.exploit-db.com/exploits/4761
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241017131637.png?raw=true)
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241017131704.png?raw=true)

# PrivEsc

## Manual

## Automated

### Peass
### Mimikatz

