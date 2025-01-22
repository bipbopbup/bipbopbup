#Pwned
```IP
192.168.181.42
```
## Enumeration
## Nmap

As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 192.168.181.42 -vvv -p- -oN enum/full
```
In this case we will need a polite nmap or it will drops the packages:
```
sudo nmap -sCV 192.168.181.42 -oA nmap/clamav -vvv -T2
```
The output being:
```
PORT    STATE SERVICE     REASON         VERSION
22/tcp  open  ssh         syn-ack ttl 61 OpenSSH 3.8.1p1 Debian 8.sarge.6 (protocol 2.0)
| ssh-hostkey:      
|   1024 30:3e:a4:13:5f:9a:32:c0:8e:46:eb:26:b3:5e:ee:6d (DSA)
| ssh-dss AAAAB3NzaC1kc3MAAACBALr/RyBq802QXa1Bh4SQEUHqD+p9TEx3SUvPHACbT0tQqR3aali+ifDiOpqMToVaRfWzYOOsoM2Neg0EPa4KsJIwSTkFqjd/3Ynp3Yzus0nN+gtmbQRKzo8QfStr6IGt6kaI6viXl4z3ww6ryEkjNnb74KCooHOjyeGPi3o89GVnAAAAFQDSg0dwMrSn9juW/XPvo8S8kVOhD
QAAAIARaqFuvZCqiTY8i/PITsr5WvyZm8mQ0nuqB6gW6y1h4jDAvtHO4TIZEMJ5vtPst0w9mVSYGVFlukhCqhbJdBigqH1WB1p7kwC78M9k23zZmzuwbnzYPiLHpEdfFEWdO62ZoCSFBXWOqe1IZaTaRCgUZPeB1QFXRCQ96VrJizPLUAAAAIEArOALxR78fZrUqmUcYOs5tf8wu5xChAUqAfh1ElJ6r3EjcWwXId12
jo1uAz0JmCTluUQhjhNDJB6XIgUzoFzW1NZPjGCkex7s1+2+TUTmqFr6Nr97k2RIy91Bpuxwg5jzE83cKPCOoWVbYlfzAqNkF4xxznfC3fRtmj2e/L9chzg=
|   1024 af:a2:49:3e:d8:f2:26:12:4a:a0:b5:ee:62:76:b0:18 (RSA)
|_ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAIEAviGcDkDxKzv7w++DXy6q+5AJDpG/q8Um8j4BheW9fgwsOvQCuDvLcPUIKMYEz4aUgkt/sSCXu29XTlu79pEkb48+BnaRCKrHLH/YWM79GT6Q5ie9jP47HjjJeCCBI/c02qpkH/fjz9FK4HQPC7WtXY9EgW4IMB+pzX2KZxK2PF0=
25/tcp  open  smtp        syn-ack ttl 61 Sendmail 8.13.4/8.13.4/Debian-3sarge3
| smtp-commands: localhost.localdomain Hello [192.168.45.152], pleased to meet you, ENHANCEDSTATUSCODES, PIPELINING, EXPN, VERB, 8BITMIME, SIZE, DSN, ETRN, DELIVERBY, HELP
|_ 2.0.0 This is sendmail version 8.13.4 2.0.0 Topics: 2.0.0 HELO EHLO MAIL RCPT DATA 2.0.0 RSET NOOP QUIT HELP VRFY 2.0.0 EXPN VERB ETRN DSN AUTH 2.0.0 STARTTLS 2.0.0 For more info use "HELP <topic>". 2.0.0 To report bugs in the imple
mentation send email to 2.0.0 sendmail-bugs@sendmail.org. 2.0.0 For local information send email to Postmaster at your site. 2.0.0 End of HELP info
80/tcp  open  http        syn-ack ttl 61 Apache httpd 1.3.33 ((Debian GNU/Linux))
|_http-title: Ph33r
|_http-server-header: Apache/1.3.33 (Debian GNU/Linux)
| http-methods: 
|   Supported Methods: GET HEAD OPTIONS TRACE
|_  Potentially risky methods: TRACE
139/tcp open  netbios-ssn syn-ack ttl 61 Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
199/tcp open  smux        syn-ack ttl 61 Linux SNMP multiplexer
445/tcp open  netbios-ssn syn-ack ttl 61 Samba smbd 3.0.14a-Debian (workgroup: WORKGROUP)
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


