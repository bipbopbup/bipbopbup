#Pwned! 
```IP
192.168.102.96
```

**This machine is currently broken so it is not possible to vulnerate!**
## Enumeration
## Nmap
As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 192.168.102.96 -vvv -p- -oN enum/full
```
The output being:
```
PORT     STATE SERVICE             REASON         VERSION
22/tcp   open  ssh                 syn-ack ttl 61 OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)      
| ssh-hostkey:
|   3072 51:56:a7:34:16:8e:3d:47:17:c8:96:d5:e6:94:46:46 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDEXg8FXWFLif7j7FQTu8UHDljVPydJScrSANssTmxaJiGrN+gwRDyUv2b+RDyMJQIh78HqKdGNtN2DyFkj7Mt78Rgb8+cHhCM4+qQAIj79w3BaA/U4776LXcoCGzJbZsaevQdLTCaVALgs7udySPJvQ+1s6dFHuNPuxmIRmRP9gf0K3rRbV04il4uUVkOD27qyP
mzdSDH/BF+L/ySHkrZqHR4jU6oss2bUtJxT2XU/MS/uxxIoJW8Mx772nL/raLMhxpa3DRKbOCW32F/Jwn3St70NIuA1NX54djYYUJX7DyNFEJEcG7dv9IaPVPtqn+06VlxqfWkuMG9++OfbxPSJcLyPQev/KqLmXGfcMtcbx5q3dt4OphXFQX2XgRpaFlUva3VWLPiuXWK1inHCCRuS4XstquGaw4fFpAhwkSRlsU/P
p+21hqcsZP0IksRddCsqThMQ7G6UwnopYPAmX9HxZhFIq3OuF2Vrsmd2RqOP6kbHBQCl6e5RSiOMAGwrS/s=
|   256 fe:76:e3:4c:2b:f6:f5:21:a2:4d:9f:59:52:39:b9:16 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBKul0mG57ikFKG/R4NkI3WOrqKfelhaucgrNRakIlWBS5kOhuds8Atr7ln/+Ibx68OAC4OmNCCIVfgCOTTeUNqY=
|   256 2c:dd:62:7d:d6:1c:f4:fd:a1:e4:c8:aa:11:ae:d6:1f (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIE8eByMdnF3xIjUD57sRJV68xTPIIajqwIJvps6f6Hu9
9090/tcp open  zeus-admin?         syn-ack ttl 61
9091/tcp open  ssl/xmltec-xmlmail? syn-ack ttl 61
```
## HTTP 9090
Openfire, Version: 4.7.3


# Exploitation

https://github.com/K3ysTr0K3R/CVE-2023-32315-EXPLOIT
CVE-2023-32315
```
python3 CVE-2023-32315.py -u http://192.168.102.96:9090 
```

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241202082623.png?raw=true)


# PrivEsc

## Manual

## Automated

### Peass
### Mimikatz

# Lateral Movement

