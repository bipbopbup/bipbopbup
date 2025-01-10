#InProgress 
```IP
192.168.163.40
```
## Nmap
```

```

## Nmap UDP


# Enumeration
## SMB
nothing found except domain name
dhokkaido-aerospace.com added to /etc/hosts
computername: dc.hokkaido-aerospace.com

with kerberos users found we get something
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241104110745.png?raw=true)
```
smbclient //192.168.242.40/homes -U 'hokkaido-aerospace.com\info' --password='info'
```
there are a list of names in homes share
and this in WsusContent share
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241104111814.png?raw=true)
the file is empty
in NETLOGON we find a password
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241104114558.png?raw=true)

we get a new hit
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241104114656.png?raw=true)


## LDAP
```
ldapsearch -H ldap://192.168.242.40 -x -s base namingcontexts
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241104102215.png?raw=true)
for more info we need credentials

## HTTP
Microsoft IIS httpd 10.0
fuzz dirs
http://192.168.242.40/aspnet_client/system_web/
fuzzed DNS without success

## HTTP 8530
http://192.168.242.40:8530/inventory
fuzzed dns without success
## Kerberos
```
./kerbrute userenum --dc 192.168.242.40 -d hokkaido-aerospace.com /usr/share/seclists/Usernames/top-usernames-shortlist.txt
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241104103122.png?raw=true)
we can kerberoast now that we have some credentials
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241104124749.png?raw=true)
we get some hashes that we can crack
we get haze1988
## MSSQL
Microsoft SQL Server 2019 RTM
10.0.20348
with info:info we get in
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241104113236.png?raw=true)
we cannot impersonate other users
```
SELECT distinct b.name FROM sys.server_permissions a INNER JOIN sys.server_principals b ON a.grantor_principal_id = b.principal_id WHERE a.permission_name = 'IMPERSONATE';
```

```
select name,create_date,modify_date,type_desc as type,authentication_type_desc as authentication_type,sid from sys.database_principals where type not in ('A', 'R') order by name;
```

with user discover:Start123!
we can impersonate and get another password
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241104121522.png?raw=true)

```
Untimed$Runny
```
## RPC
now we can access
```
rpcclient -U 'hokkaido-aerospace.com\Hazel.Green' --password='haze1988' 192.168.242.40
```
Angela.Davies is domainadmin
Alexandra.Little, Molly.Smith and Hazel.Green are IT members
Molly.Smith is Tear 1 admin
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241105130648.png?raw=true)

# PrivEsc

## Manual

## Automated

### Peass
### Mimikatz

