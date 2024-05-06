---
title: Query Gate
date: 2024-03-27 12:23:12 
categories: [Hackviser]
tags: [MySQL, Warmups]  
image: /images/querygate.webp
---

MySQL, verileri yönetmek ve işlemek için Structured Query Language (SQL) kullanan bir ilişkisel veritabanı yönetim sistemidir (RDBMS). MySQL, web uygulamalarının veritabanları için yaygın olarak kullanılan açık kaynaklı bir sistemdir.


<a href="https://app.hackviser.com/warmups/query-gate">Query Gate</a>


Hangi portlar açık?

````
┌──(root㉿kali)-[~]
└─# nmap 172.20.4.161
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-28 06:13 EDT
Nmap scan report for 172.20.4.161 (172.20.4.161)
Host is up (0.073s latency).
Not shown: 999 closed tcp ports (reset)
PORT     STATE SERVICE
3306/tcp open  mysql
```` 


- 3306


Çalışan servisin adı nedir?

- MySQL

MySQL'e bağlanmak için kullanabileceğimiz en yetkili kullanıcı adı nedir?

>En yetkili kullanıcı root kullanıcısıdır.
{: .prompt-info }

Hedef makinede çalışan MySQL'e bağlanmak için komut satırı aracında hostname i belirtmek için hangi parametre kullanılır?

> -h

Bağlandığınız MySQL sunucusunda kaç veritabanı var?

````
MySQL [(none)]> show databases;
+---------------------+
| Database            |
+---------------------+
| detective_inspector |
| information_schema  |
| mysql               |
| performance_schema  |
| sys                 |
+---------------------+
5 rows in set (0.071 sec)
````

- 5 

Hangi komutla bir veritabanı seçebiliriz?

- use

detective_inspector veritabanındaki tablonun adı nedir?

````
MySQL [(none)]> use detective_inspector;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MySQL [detective_inspector]> show tables;
+-------------------------------+
| Tables_in_detective_inspector |
+-------------------------------+
| hacker_list                   |
+-------------------------------+
1 row in set (0.069 sec)
````

- hacker_list


Beyaz şapkalı hacker'ın kullanıcı adı nedir?

````
MySQL [detective_inspector]> select * from hacker_list;
+------+-----------+-----------+------------+-----------+
| id   | firstName | lastName  | nickname   | type      |
+------+-----------+-----------+------------+-----------+
| 1001 | Jed       | Meadows   | sp1d3r     | gray-hat  |
| 1002 | Melissa   | Gamble    | c0c0net    | gray-hat  |
| 1003 | Frank     | Netsi     | v3nus      | gray-hat  |
| 1004 | Nancy     | Melton    | s1torml09  | black-hat |
| 1005 | Jack      | Dunn      | psyod3d    | black-hat |
| 1006 | Arron     | Eden      | r4nd0myfff | black-hat |
| 1007 | Lea       | Wells     | pumq7eggy7 | black-hat |
| 1008 | Hackviser | Hackviser | h4ckv1s3r  | white-hat |
| 1009 | Xavier    | Klein     | oricy4l33  | black-hat |
+------+-----------+-----------+------------+-----------+
9 rows in set (0.070 sec)
````

`h4ckv1s3r`

![fsoc](/images/fsoc.gif)
