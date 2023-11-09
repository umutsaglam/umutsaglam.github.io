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
![](https://raw.githubusercontent.com/siunam321/CTF-Writeups/main/TryHackMe/Rootme/images/a1.png)
