---
title: Committed
date: 2024-03-27 13:02:12
categories: [TryHackMe]
tags: [Easy]
image: images/commit.webp
---

Geliştiricilerimizden biri yanlışlıkla bazı hassas kodları GitHub depomuza aktardı. En azından bize öyle söylediler... sorun şu ki, neyi ve nerede olduğunu hatırlamıyoruz! Yanlışlıkla yaptığımız şeyin izini sürebilir misin?

---

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/Committed/images/1.png?raw=true)

Gerekli dosyayı kaliye indirmek için makinede web sunucusunu çalıştırıyorum.

````bash
python3 -m http.server 3131
````

http://10.10.102.180:3131/ e gittiğimde dosyayı indirebilirim.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/Committed/images/2.png?raw=true)

Zipten çıkardım ve dosyanın içinde sonuc isimli bir dizin oluşturdum.

[Extractor](https://github.com/internetwache/GitTools.git) toolunu kullacağım.

````console
┌──(root㉿kali)-[~/Downloads/commited]
└─# mkdir sonuc                                     
                                                                                                    
┌──(root㉿kali)-[~/Downloads/commited]
└─# /opt/GitTools/Extractor/./extractor.sh . sonuc
###########
# Extractor is part of https://github.com/internetwache/GitTools
#
# Developed and maintained by @gehaxelt from @internetwache
#
# Use at your own risk. Usage might be illegal in certain circumstances. 
# Only for educational purposes!
###########
[+] Found commit: b0eda7db60a1cb0aea86f053816a1bfb7e2d6c67
[+] Found file: /root/Downloads/commited/sonuc/0-b0eda7db60a1cb0aea86f053816a1bfb7e2d6c67/Readme.md
[+] Found file: /root/Downloads/commited/sonuc/0-b0eda7db60a1cb0aea86f053816a1bfb7e2d6c67/main.py
[+] Found commit: 9ecdc566de145f5c13da74673fa3432773692502
[+] Found file: /root/Downloads/commited/sonuc/1-9ecdc566de145f5c13da74673fa3432773692502/Readme.md
[+] Found file: /root/Downloads/commited/sonuc/1-9ecdc566de145f5c13da74673fa3432773692502/main.py
[+] Found commit: 3a8cc16f919b8ac43651d68dceacbb28ebb9b625
[+] Found file: /root/Downloads/commited/sonuc/2-3a8cc16f919b8ac43651d68dceacbb28ebb9b625/Note
[+] Found file: /root/Downloads/commited/sonuc/2-3a8cc16f919b8ac43651d68dceacbb28ebb9b625/Readme.md
[+] Found file: /root/Downloads/commited/sonuc/2-3a8cc16f919b8ac43651d68dceacbb28ebb9b625/main.py
[+] Found commit: 28c36211be8187d4be04530e340206b856198a84
[+] Found file: /root/Downloads/commited/sonuc/3-28c36211be8187d4be04530e340206b856198a84/Readme.md
[+] Found file: /root/Downloads/commited/sonuc/3-28c36211be8187d4be04530e340206b856198a84/main.py
[+] Found commit: 26bcf1aa99094bf2fb4c9685b528a55838698fbe
[+] Found file: /root/Downloads/commited/sonuc/4-26bcf1aa99094bf2fb4c9685b528a55838698fbe/Readme.md
[+] Found file: /root/Downloads/commited/sonuc/4-26bcf1aa99094bf2fb4c9685b528a55838698fbe/main.py
[+] Found commit: 6e1ea88319ae84175bfe953b7791ec695e1ca004
[+] Found file: /root/Downloads/commited/sonuc/5-6e1ea88319ae84175bfe953b7791ec695e1ca004/Note
[+] Found file: /root/Downloads/commited/sonuc/5-6e1ea88319ae84175bfe953b7791ec695e1ca004/Readme.md
[+] Found file: /root/Downloads/commited/sonuc/5-6e1ea88319ae84175bfe953b7791ec695e1ca004/main.py
[+] Found commit: c56c470a2a9dfb5cfbd54cd614a9fdb1644412b5
[+] Found file: /root/Downloads/commited/sonuc/6-c56c470a2a9dfb5cfbd54cd614a9fdb1644412b5/Note
[+] Found file: /root/Downloads/commited/sonuc/6-c56c470a2a9dfb5cfbd54cd614a9fdb1644412b5/Readme.md
[+] Found file: /root/Downloads/commited/sonuc/6-c56c470a2a9dfb5cfbd54cd614a9fdb1644412b5/main.py
[+] Found commit: 441daaaa600aef8021f273c8c66404d5283ed83e
[+] Found file: /root/Downloads/commited/sonuc/7-441daaaa600aef8021f273c8c66404d5283ed83e/Readme.md
[+] Found file: /root/Downloads/commited/sonuc/7-441daaaa600aef8021f273c8c66404d5283ed83e/main.py
[+] Found commit: 4e16af9349ed8eaa4a29decd82a7f1f9886a32db
[+] Found file: /root/Downloads/commited/sonuc/8-4e16af9349ed8eaa4a29decd82a7f1f9886a32db/Note
[+] Found file: /root/Downloads/commited/sonuc/8-4e16af9349ed8eaa4a29decd82a7f1f9886a32db/Readme.md
[+] Found file: /root/Downloads/commited/sonuc/8-4e16af9349ed8eaa4a29decd82a7f1f9886a32db/main.py
````

sonuc dizinine gidip flag'i alabilirim.

![](https://github.com/umutsaglam/CTF-Writeups/blob/main/TryHackMe/Committed/images/3.png?raw=true)


![fsoc](/images/fsoc.gif)
