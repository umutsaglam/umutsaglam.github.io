---
title: Crack the Hash
date: 2024-03-02 12:23:12 
categories: [TryHackMe]
tags: [Easy,Hash,Cracking]
image: https://www.google.com/url?sa=i&url=https%3A%2F%2Fwww.pcworld.com%2Farticle%2F394029%2Fhow-to-create-strong-secure-passwords.html&psig=AOvVaw0Gg9-RRzFGWZdniCOiHbPr&ust=1711709791449000&source=images&cd=vfe&opi=89978449&ved=0CBIQjRxqFwoTCID64IPmloUDFQAAAAAdAAAAABAJ
---

 <a href="https://tryhackme.com/room/crackthehash">Crack the hash</a>

# Level 1

1.
İlk sorunun çözümü için CrackStation sitesine gidiyorum ve verilen hash'i çözüyorum.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/Crack_the_hash/images/a1.png?raw=true)

2.
Aynı yöntemi kullanıyorum.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/Crack_the_hash/images/a2.png?raw=true)

3.
:)

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/Crack_the_hash/images/a3.png?raw=true)

4.
Verilen hash’i önce bir hash analyzer’a sokuyorum ve hash type’ini buluyorum. Bu kısımda tunnelsup sitesini kullanıyorum.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/Crack_the_hash/images/a4.png?raw=true)

Bu işlem için hashcat kullanacağım, çözüm işleminden önce şifreyi bir .txt dosya içerisine kayıt ediyorum ve ardından işleme başlıyorum.

>hashcat -m 3200 /root/Documents/hash.txt /usr/share/wordlists/rockyou.txt

>Benim gibi kali linuxu virtual machine olarak çalıştırıyorsanız hashcat'i ana işletim sisteminizde çalıştırmanızı tavsiye ediyorum. Hashcat hashleri kırarken ekran kartınızdan yararlanır. VM kullanırken ekran kartınız yerine işlemciyle kıracak ve haliyle çok daha uzun sürecektir.

>Cevap: $2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom:bleh


5.
CrackStation

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/Crack_the_hash/images/a6.png?raw=true)

# Level 2

1.
![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/Crack_the_hash/images/a7.png?raw=true)

2.
![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/Crack_the_hash/images/a8.png?raw=true)

3.
Bu hash'in ne olduğunu öğrenmek için [Hashes](https://hashes.com/en/tools/hash_identifier) sitesini kullanıyorum.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/Crack_the_hash/images/a9.png?raw=true)

Ardından hashcat kodunu aratıyorum

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/Crack_the_hash/images/a10.png?raw=true)

Kullancağımız komut
> hashcat -m 1800 hash.txt /usr/share/wordlists/rockyou.txt


> Cevap: $6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02>waka99


Ve böylelikle bir ctf daha tamamladık gelecek yazılarımda görüşmek üzere.

