---
title: Discover Lernaean
date: 2024-03-27 12:23:12 
categories: [Hackviser]
tags: [Brute Force, Warmups]  
image: /images/discover.webp
---

Bu ısınma makinesi, Apache ve SSH servisleri üzerinde dizin taraması, brute-force saldırıları ve yaygın uygulama güvenlik açıklarının nasıl zincirleme kullanılabileceğini öğretmeye odaklanır.

Web sunucuları ve SSH protokollerinde güvenlik zaafiyetlerinin nasıl keşfedilebileceği ve bu zafiyetlerin nasıl sömürülebileceği ile ilgili alıştırmalar yapmak için önerilir.


<a href="https://app.hackviser.com/warmups/discover-lernaean">Discover Lernaean</a>

Hangi portlar açık?


````
┌──(root㉿kali)-[~]
└─# nmap -T4 -sV 172.20.4.157
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-28 06:27 EDT
Nmap scan report for 172.20.4.157 (172.20.4.157)
Host is up (0.072s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
80/tcp open  http    Apache httpd 2.4.56 ((Debian))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
````

- 22,80

80 portunda çalışan servisin versiyonu nedir?

- 2.4.56

Dizin tarama aracını kullanarak bulduğunuz dizin nedir?

````
┌──(root㉿kali)-[~]
└─# gobuster dir --wordlist=/usr/share/wordlists/dirb/common.txt --url http://172.20.4.157  -t40  
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://172.20.4.157
[+] Method:                  GET
[+] Threads:                 40
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.htpasswd            (Status: 403) [Size: 277]
/.hta                 (Status: 403) [Size: 277]
/.htaccess            (Status: 403) [Size: 277]
/filemanager          (Status: 301) [Size: 318] [--> http://172.20.4.157/filemanager/]
/index.html           (Status: 200) [Size: 10701]
/server-status        (Status: 403) [Size: 277]
Progress: 4614 / 4615 (99.98%)
===============================================================
Finished
===============================================================
````

- filemanager

File manager'a giriş yapmak için kullandığınız username:password nedir?

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/Hackviser/Discover/images/1.png?raw=true)
 


file manager dizininde bizi bu sayfa karşılıyor. Tiny File Manager kullanıldığını gördüm ve github reposunda default username:password u buldum.


- user:12345

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/Hackviser/Discover/images/2.png?raw=true)
 

Bilgisayara eklenen son kullanıcı adı nedir?

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/Hackviser/Discover/images/3.png?raw=true)


- rock 


rock kullanıcısının parolası nedir?

Port taramasında SSH servisi çalıştığını görmüştüm. Bulduğum rock kullanıcısıyla SSH servisine brute force deneyebilirim.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/Hackviser/Discover/images/4.png?raw=true)
 

- 7777777

rock kullanıcısı tarafından çalıştırılan ilk komut nedir?

Bunu öğrenmek için ssh ile makineye girmemiz ve rock kullanıcısının dizininde bulunan .bash_history dosyasını okumamız gerekiyor.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/Hackviser/Discover/images/5.png?raw=true)


- cd 

![fsoc](/images/fsoc.gif)


