#InProgress 
```
192.168.208.20
```
## Nmap
```
PORT     STATE SERVICE       REASON          VERSION
21/tcp   open  ftp           syn-ack ttl 125
| fingerprint-strings:       
|   GenericLines, NULL: 
|_    220 Welcome to Simple FTP Server           
135/tcp  open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
139/tcp  open  netbios-ssn   syn-ack ttl 125 Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds? syn-ack ttl 125          
3389/tcp open  ms-wbt-server syn-ack ttl 125 Microsoft Terminal Services
|_ssl-date: 2024-10-07T14:15:52+00:00; +9s from scanner time.
| rdp-ntlm-info:                                                                                                     
|   Target_Name: OSAKA                                                                                               
|   NetBIOS_Domain_Name: OSAKA
|   NetBIOS_Computer_Name: OSAKA
|   DNS_Domain_Name: osaka                    
|   DNS_Computer_Name: osaka
|   Product_Version: 10.0.17763
|_  System_Time: 2024-10-07T14:15:39+00:00
| ssl-cert: Subject: commonName=osaka
| Issuer: commonName=osaka           
| Public Key type: rsa               
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2024-07-11T12:49:08
| Not valid after:  2025-01-10T12:49:08
| MD5:   6d59:20e2:653c:b44e:8e98:7d95:6cbd:1c7c
| SHA-1: 4eb4:bfab:7e18:da80:b8c5:f883:b19b:f713:f5fc:7885 
| -----BEGIN CERTIFICATE-----                                                                                        
| MIICzjCCAbagAwIBAgIQW5aR6yZRD6pDMi8OErtJEzANBgkqhkiG9w0BAQsFADAQ
| MQ4wDAYDVQQDEwVvc2FrYTAeFw0yNDA3MTExMjQ5MDhaFw0yNTAxMTAxMjQ5MDha  
| MBAxDjAMBgNVBAMTBW9zYWthMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKC
| AQEAsRa5JillhvcpPWDpHeE5uMkYq4sneGHVtT2Chg8gSTuB1AzMMMeQmD8QSIeg
| PjBNmy3NKmtLXvA7uOAsDcbaMQPWneHMKspJrFmJkGK1fgvSX6mOkiyEuLPs0JH1
| J48/0kdyaY72tYNplyp5d0i4fW5cwNunZW3PsuBEtbNWNX+q/iH5A8dmBDUqVXc9
| cuiYnDN+5kc6DLx2FgC8K1J/vV1bnimrPy1jU/WJlzaGpcbDDEHNkGy9r6SEj79d
| 68FyLI7mvBvDmzpkiWP5IiS/pWzKfv2MlHQiKXjhlaEoaFarhDlcdiHnn7bsutVE
| j25GuvFS2xAGvm8VABopD7il8QIDAQABoyQwIjATBgNVHSUEDDAKBggrBgEFBQcD
| ATALBgNVHQ8EBAMCBDAwDQYJKoZIhvcNAQELBQADggEBABTlEEEW2NZK3fdoX7vM
| JzTYGzjo3Z3azdOjgJZNTa5Tj9LSGHxwmmEGTDTrUJL1jGN1Am/9tEesyZHWSBgD
| K3s/gBjgxF5nkmyDXhWq2g0Rr84VbVRGsmRs92D4PhDhLe1B+EiAmh9NNUYXN3Id
| aTMk+oX6wVXC5v073ZAzHHEx9MVdXlwtha43tHlGxMyljuCdus93Kv9rURvNf6R3
| lBkngl2waP5AnyduLw3DqAU0PamR/QHPwqsZ/9Xy9Uf+NtxQI3FpXl38YMJR52mP
| cPdt6OKPwpd1aEVaLymGnqraRRnWqdCs3n2ZH2ab6A+zlMSwssOoLrjpdkW/0Czt
| WG8=
|_-----END CERTIFICATE-----
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port21-TCP:V=7.94SVN%I=7%D=10/7%Time=6703ECEB%P=x86_64-pc-linux-gnu%r(N
SF:ULL,22,"220\x20Welcome\x20to\x20Simple\x20FTP\x20Server\r\n")%r(Generic
SF:Lines,24,"220\x20Welcome\x20to\x20Simple\x20FTP\x20Server\r\n\r\n");

```

## Nmap UDP


# Enumeration
## SMB
## FTP
anonymous:anonymous

## HTTP


# PrivEsc

## Manual

## Automated

### Peass
### Mimikatz
