---
title: Secure Command
date: 2024-03-27 12:23:12 
categories: [Hackviser]
tags: [SSH, Warmups]  
image: /images/securecommand.webp
---

SSH (Secure Shell), bir ağ üzerindeki cihazlara güvenli bir şekilde erişmek ve yönetmek için kullanılan bir protokoldür. Gizliliği ve bütünlüğü korumak için verileri şifreler, bu da SSH'ı uzaktan yönetim için Telnet'e göre tercih edilen bir seçenek haline getirir.

SSH servisi ile ilgili temel alıştırmalar yapmak için önerilir.

<a href="https://app.hackviser.com/warmups/secure-command">Secure Command</a>


Hangi portlar açık?

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/Hackviser/SecureCommand/images/1.png?raw=true)


- 22

Çalışan hizmetin adı nedir?

- ssh

SSH'a hackviser:hackviser oturum bilgileri ile bağlanırken "Master's Message" nedir?

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/Hackviser/SecureCommand/images/2.png?raw=true)
 

- W3lc0m3 t0 h4ck1ng w0rld


Linux'ta kullanıcı değiştirmek için kullanılan komut nedir?

- su 

Root kullanıcısının parolası nedir?

İpucunda çok basit düşünmemiz istenmiş :)

- root 

ls komutunun gizli dosyaları gösteren parametresi nedir?

- -a 

Master’in tavsiyesi nedir?

root dizininde ls -la ile .advice_of_the_master dosyasını buldum. 

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/Hackviser/SecureCommand/images/3.png?raw=true)


- st4y cur10us

---

![fsoc](/images/fsoc.gif)
