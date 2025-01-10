#InProgress 
```IP
192.168.168.16
```
# Enumeration

## HTTP 80
We are redirected to a wordpress database connetion functionality
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241126105319.png?raw=true)
fuzzing we can see the following directory
http://192.168.168.16/filemanager/
which we can access with admin:admin
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241126120234.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241126120453.png?raw=true)


# Exploitation
upload reverse shell in php and execute
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241126122826.png?raw=true)
we can also crack dora's hash
```
john --wordlist=/usr/share/wordlists/rockyou.txt files/dora.hash
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241126122854.png?raw=true)
```
doraemon
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241126123017.png?raw=true)
# PrivEsc

## Manual

we are part of disk group, so we can abuse debugfs to read /etc/shadow
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241126124350.png?raw=true)
and we crack root hash
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241126124405.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241126124440.png?raw=true)

## Automated

### Peass
### Mimikatz

# Lateral Movement
