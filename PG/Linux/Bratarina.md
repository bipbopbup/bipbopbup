#Pwned! 
```
192.168.209.71
```
## Nmap
```
PORT    STATE  SERVICE     REASON         VERSION
22/tcp  open   ssh         syn-ack ttl 61 OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:         
|   2048 db:dd:2c:ea:2f:85:c5:89:bc:fc:e9:a3:38:f0:d7:50 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDJ0GZmmFtQUJbj2HgPsye2Xccyyh9mC8fsCwIivM4x3o3mwZDNi6g+Y6nIs5SuOJj2IpS+E9O5wB71MSIv7d7XYrd6paprfvnvMCyAQ9VTn8py6CQ/OsgeOITU+JnAxoe3WQklpyAVqhJ7ASqAInZF8oHDaebr6gBKEq4nkoLOtJSZeB8xWDHhbQZjG6AY81Y2m
HPZH/LC4gSXpSmw+3h0zhlCN/kxeyhjrsrZqIVdKhg4emds8+gQyu1Wrz4AUUBGscI6Sh5rjImr+SC4rAGgn6N0MVPcZA1mS0JUplz758Y3YFXstqO2SdaHB/Qb50fkcpclcYKibSbCv5ZLNzOf
|   256 e3:b7:65:c2:a7:8e:45:29:bb:62:ec:30:1a:eb:ed:6d (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBOWgZAzhJ+plc4Rk/YyGvQ1KOKK9j31ix1uCWIAirjnZS/lKwcvYrkG+lVsJRBnBYVA+67ILSJR2YNVz9uZshPE=
|   256 d5:5b:79:5b:ce:48:d8:57:46:db:59:4f:cd:45:5d:ef (ED25519)                             
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIC2Z9krPXlbEN6Xl40sc3BiVLfhbtd+l5ZFNBKkF7pYT
25/tcp  open   smtp        syn-ack ttl 61 OpenSMTPD                                                                  
| smtp-commands: bratarina Hello nmap.scanme.org [192.168.45.213], pleased to meet you, 8BITMIME, ENHANCEDSTATUSCODES, SIZE 36700160, DSN, HELP
|_ 2.0.0 This is OpenSMTPD 2.0.0 To report bugs in the implementation, please contact bugs@openbsd.org 2.0.0 with full details 2.0.0 End of HELP info
53/tcp  closed domain      reset ttl 61                                                                                                                                                                                                    
80/tcp  open   http        syn-ack ttl 61 nginx 1.14.0 (Ubuntu)
|_http-title:         Page not found - FlaskBB        
|_http-server-header: nginx/1.14.0 (Ubuntu)   
445/tcp open   netbios-ssn syn-ack ttl 61 Samba smbd 4.7.6-Ubuntu (workgroup: COFFEECORP)
Service Info: Host: bratarina; OS: Linux; CPE: cpe:/o:linux:linux_kernel
                                                          
Host script results:                          
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user                  
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_clock-skew: mean: 1h20m03s, deviation: 2h18m36s, median: 1s
| p2p-conficker:                                                                                                     
|   Checking for Conficker.C or higher...
|   Check 1 (port 37047/tcp): CLEAN (Timeout)
|   Check 2 (port 19126/tcp): CLEAN (Timeout)
|   Check 3 (port 53085/udp): CLEAN (Timeout)
|   Check 4 (port 36001/udp): CLEAN (Timeout)                                                                        
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb2-time: 
|   date: 2024-10-04T08:21:08                                                                                        
|_  start_date: N/A
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.7.6-Ubuntu)
|   Computer name: bratarina
|   NetBIOS computer name: BRATARINA\x00
|   Domain name: \x00
|   FQDN: bratarina
|_  System time: 2024-10-04T04:21:10-04:00
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required

```

## Nmap UDP


# Enumeration
## SMB
```
netexec smb 192.168.209.71 -u '' -p '' --shares
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241004111421.png?raw=true)
```
smbclient -U '' -N \\\\192.168.209.71\\backups
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241004112129.png?raw=true)
## SMTP
```
sudo nmap -p 25 --script=smtp-enum-users,smtp-open-relay 10.10.10.10
```
Nada pero sabemos que es OpenSMTP 2.0.0 por el nmap inicial
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241004114854.png?raw=true)
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241004115133.png?raw=true)
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241004115143.png?raw=true)
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241004122100.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241004122127.png?raw=true)


## HTTP

# PrivEsc

## Manual

## Automated

### Peass
### Mimikatz


