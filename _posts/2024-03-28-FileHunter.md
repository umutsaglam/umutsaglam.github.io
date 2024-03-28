---
title: File Hunter
date: 2024-03-27 12:13:12 
categories: [Hackviser]
tags: [FTP, Warmups]  
image: https://storage.hackviser.com/file/hackviser-prod/scenarios/dcbe1ab63023486fb3937b9a6bb18153.webp
---

FTP (File Transfer Protocol), dosya aktarımlarını internet üzerinden yapmak için kullanılan bir protokoldür. Bu protokol, bir bilgisayarın dosyalarını diğer bir bilgisayara yüklemek veya indirmek için kullanılır.

FTP servisi ile ilgili temel alıştırmalar yapmak için önerilir.

<a href="https://app.hackviser.com/warmups/file-hunter">File Hunter</a>


Hangi portlar açık?

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/Hackviser/FileHunter/images/1.png?raw=true)


- 21 

FTP'nin açılımı nedir?

- File Transfer Protocol

FTP'ye hangi kullanıcı adı ile bağlandınız?

anonymous ile giriş yapmayı deniyorum.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/Hackviser/FileHunter/images/2.png?raw=true)

- anonymous

Hangi komut FTP sunucusunda hangi komutları kullanabileceğimizi gösterir?

- help

FTP sunucusundaki dosyanın adı nedir?

````
ftp> ls
229 Entering Extended Passive Mode (|||16299|)
150 Here comes the directory listing.
-rw-r--r--    1 ftp      ftp            25 Sep 08  2023 userlist
226 Directory send OK.
ftp> 
````
- userlist 

Bir FTP sunucusundan dosya indirmek için kullanabileceğimiz komut nedir?

- get 

Dosyada hangi kullanıcıların bilgileri vardır?

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/Hackviser/FileHunter/images/3.png?raw=true)


- jack 
