## Cyberspherules

![image](https://github.com/0hanif0/B2R-Writeups/assets/23289982/a97f897b-74e9-430f-9ccf-5483bca191a8)

Link: https://tryhackme.com/room/cyberspherules

Goal: flag.txt, user.txt, root.txt

## nmap
```
nmap -sC -sV 10.10.78.160
Starting Nmap 7.94 ( https://nmap.org ) at 2023-08-04 10:39 EDT
Nmap scan report for 10.10.78.160
Host is up (0.24s latency).
Not shown: 999 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Simple Resume Upload Page

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 33.65 seconds
```
- only port 80 open

## view web [port 80]

![image](https://github.com/0hanif0/B2R-Writeups/assets/23289982/1fd9b7ff-8d45-458e-8697-600e944b9498)

- look like we have to exploit file upload here

## upload php web shell

![image](https://github.com/0hanif0/B2R-Writeups/assets/23289982/e4d0234c-7f5d-4ce3-b98c-dba0838a614f)

- but failed, it show `invalid file type`, try to rename `.pHp` also failed

## source code given [here](https://pastebin.com/V75YLbQj)

![image](https://github.com/0hanif0/B2R-Writeups/assets/23289982/883b9d9d-2671-4d62-95d2-f6bd120e27a7)

- there are several file type that are on the list might be restricted 

## looking another file type of php

![image](https://github.com/0hanif0/B2R-Writeups/assets/23289982/66cca8de-b5af-4156-9fb9-f6b7716f6fa3)

- based on [hacktrick](https://book.hacktricks.xyz/pentesting-web/file-upload), these two `.module` and `.phtml` are not include in the list, so lets try

## upload with .phtml

![image](https://github.com/0hanif0/B2R-Writeups/assets/23289982/4e820c50-7b89-45b8-8967-6445250f2fcd)

- success

## setup netcat listener

![image](https://github.com/0hanif0/B2R-Writeups/assets/23289982/bc64de58-86aa-4608-9a6b-32519f549c2b)

- goto `http://10.10.78.160/uploads/rev.phtml` dont forget to open listener `nc -lnvp 1337` on terminal
- `python3 -c 'import pty;pty.spawn("/bin/bash")'` upgrade shell
- `export TERM=xterm` enable clear

## flag.txt

![image](https://github.com/0hanif0/B2R-Writeups/assets/23289982/c7ac4d29-6ed3-4e90-b720-7386338d3a4f)

## password.txt

![image](https://github.com/0hanif0/B2R-Writeups/assets/23289982/ba5e9501-71db-4251-b01b-e41ebc40cde2)

## cd /home/jack

![image](https://github.com/0hanif0/B2R-Writeups/assets/23289982/2052404c-0d31-4bee-ac71-034cea0b3f24)

## crack password.txt

![image](https://github.com/0hanif0/B2R-Writeups/assets/23289982/268cba76-aae8-4bc8-8579-51ac48cab7a3)

- found Password: jack1

## switch user to jack

![image](https://github.com/0hanif0/B2R-Writeups/assets/23289982/8175509c-31bb-43da-9a47-fdae366fb0fb)

## user.txt


