## Mr Robot CTF [THM]

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

![image](https://github.com/0hanif0/B2R-Writeups/assets/23289982/cf42b083-2891-4517-88cb-18ac79e35e23)

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

## robots.txt

![image](https://github.com/0hanif0/B2R-Writeups/assets/23289982/c2a5b7b6-fc4c-4c46-b7d9-55dc34cc3ab4)

## key 1

![image](https://github.com/0hanif0/B2R-Writeups/assets/23289982/ebc8f0cc-d162-4bfc-b0bb-2f3353c27aec)

## download fsociety.dic

![image](https://github.com/0hanif0/B2R-Writeups/assets/23289982/d40f3f58-7dc7-4171-bd82-db75b4ca66f4)

## wp-login

![image](https://github.com/0hanif0/B2R-Writeups/assets/23289982/f7433ee8-b9d5-452c-a2f6-4b039608c1c1)


