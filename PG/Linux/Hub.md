#InProgress 
```IP
192.168.223.25
```
## Nmap
```
PORT     STATE SERVICE  REASON         VERSION                                          
22/tcp   open  ssh      syn-ack ttl 61 OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)    
| ssh-hostkey:                                                                          
|   3072 c9:c3:da:15:28:3b:f1:f8:9a:36:df:4d:36:6b:a7:44 (RSA)    
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDNEbgprJqVJa8R95Wkbo3cemB4fdRzos+v750LtPEnRs+IJQn5jcg5l89Tx4junU+AXzLflrMVo55gbuKeNTDtFRU9ltlIu4AU+f7lRlUlvAHlNjUbU/z3WBZ5ZU9j7Xc9WKjh1Ov7chC0UnDdyr5EGrIwlLzgk8zrWx364+S4JqLtER2/n0rhVxa9RCw0tR/oL2
4kMep4q7rFK6dThiRtQ9nsJFhh6yw8Fmdg7r4uohqH70UJurVwVNwFqtr/86e4VSSoITlMQPZrZFVvoSsjyL8LEODt1qznoLWudMD95Eo1YFSPID5VcS0kSElfYigjSr+9bNSdlzAof1mU6xJA67BggGNu6qITWWIJySXcropehnDAt2nv4zaKAUKc/T0ij9wkIBskuXfN88cEmZbu+gObKbLgwQSRQJIpQ+B/mA8CD4
AiaTmEwGSWz1dVPp5Fgb6YVy6E4oO9ASuD9Q1JWuRmnn8uiHF/nPLs2LC2+rh3nPLXlV+MG/zUfQCrdrE=
|   256 26:03:2b:f6:da:90:1d:1b:ec:8d:8f:8d:1e:7e:3d:6b (ECDSA)   
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBCUhhvrIBs53SApXKZYHWBlpH50KO3POt8Y+WvTvHZ5YgRagAEU5eSnGkrnziCUvDWNShFhLHI7kQv+mx+4R6Wk=
|   256 fb:43:b2:b0:19:2f:d3:f6:bc:aa:60:67:ab:c1:af:37 (ED25519) 
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIN4MSEXnpONsc0ANUT6rFQPWsoVmRW4hrpSRq++xySM9
80/tcp   open  http     syn-ack ttl 61 nginx 1.18.0                                     
|_http-server-header: nginx/1.18.0                                                      
| http-methods:                                                                         
|_  Supported Methods: GET HEAD POST                                                    
|_http-title: 403 Forbidden                                                             
8082/tcp open  http     syn-ack ttl 61 Barracuda Embedded Web Server
|_http-favicon: Unknown favicon MD5: FDF624762222B41E2767954032B6F1FF
|_http-title: Home                                                                      
|_http-server-header: BarracudaServer.com (Posix)                                       
| http-webdav-scan:                                                                     
|   Allowed Methods: OPTIONS, GET, HEAD, PROPFIND, PATCH, POST, PUT, COPY, DELETE, MOVE, MKCOL, PROPFIND, PROPPATCH, LOCK, UNLOCK
|   Server Date: Wed, 09 Oct 2024 08:58:56 GMT                                          
|   WebDAV type: Unknown   
|_  Server Type: BarracudaServer.com (Posix)
| http-methods:                                        
|   Supported Methods: OPTIONS GET HEAD PROPFIND PATCH POST PUT COPY DELETE MOVE MKCOL PROPPATCH LOCK UNLOCK
|_  Potentially risky methods: PROPFIND PATCH PUT COPY DELETE MOVE MKCOL PROPPATCH LOCK UNLOCK
9999/tcp open  ssl/http syn-ack ttl 61 Barracuda Embedded Web Server
|_http-server-header: BarracudaServer.com (Posix)
| http-methods:                      
|   Supported Methods: OPTIONS GET HEAD PROPFIND PATCH POST PUT COPY DELETE MOVE MKCOL PROPPATCH LOCK UNLOCK
|_  Potentially risky methods: PROPFIND PATCH PUT COPY DELETE MOVE MKCOL PROPPATCH LOCK UNLOCK
| http-webdav-scan:                  
|   Allowed Methods: OPTIONS, GET, HEAD, PROPFIND, PATCH, POST, PUT, COPY, DELETE, MOVE, MKCOL, PROPFIND, PROPPATCH, LOCK, UNLOCK
|   Server Date: Wed, 09 Oct 2024 08:58:56 GMT
|   WebDAV type: Unknown             
|_  Server Type: BarracudaServer.com (Posix)
```

## Nmap UDP


# Enumeration

## HTTP

https://github.com/SanjinDedic/FuguHub-8.4-Authenticated-RCE-CVE-2024-27697

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241010103744.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241010103758.png?raw=true)
# PrivEsc

## Manual

## Automated

### Peass
### Mimikatz

