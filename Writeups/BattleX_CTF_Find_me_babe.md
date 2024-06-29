## BattleX CTF: Find me babe 
#### Category: Forensics
#### Score: 80
#### Number of Solves: 4 [First Blood 🩸] 

### Description

> put me here :)

**Files Attached:** rootfs-1719533608029.squashfs

### TL;DR

Purpose is to unsquash the Squashfs dump and extract the flag.

    
### Detecting and extracting

I downloaded the attached file and proceeded to check the file type using `file` command to be sure it was in fact, a Squashfs filesystem file. CTFs can be wild lol.

```
gr33pp >> file rootfs-1719533608029.squashfs
rootfs-1719533608029.squashfs: Squashfs filesystem, little endian, version 4.0, zlib compressed, 155264170 bytes, 14701 inodes, blocksize: 131072 bytes, created: Fri Jun 28 00:09:30 2024
```
Next, I proceeded to use `unsquashfs` command.

```
sudo unsquashfs rootfs-1719533608029.squashfs

Parallel unsquashfs: Using 4 processors
13359 inodes (13136 blocks) to write

[====================================================================\] 26495/26495 100%

created 11552 files
created 1346 directories
created 1795 symlinks
created 8 devices
created 0 fifos
created 0 sockets
created 4 hardlinks
```
This decompresses the Squashfs file and extracts its content to my current directory. The `-d` flag would help if you wish to specify a directory for extraction.

### Finding the babe

Now that the Squashfs is extracted (mine extracted to `squashfs-root` in my current directory), I chrooted into it using `chroot` command.

```
sudo chroot squashfs-root

root@archlinux:/# ls -lah
total 68K
drwxrwxr-x 17 1000 1000 4.0K Jun 27 23:34 .
drwxrwxr-x 17 1000 1000 4.0K Jun 27 23:34 ..
lrwxrwxrwx  1 root root    7 Jun 27 22:59 bin -> usr/bin
drwxr-xr-x  2 root root 4.0K Apr 15  2020 boot
[break]
```
The Boom! I was dropped into the chroot. To find the flag was pretty easy. I `grep` recursively to find anything like `battlex{` as it is the flag pattern.

```
root@archlinux:/# grep -r "battlex{" / 2>/dev/null
/etc/sysctl.d/README.sysctl:battlex{1bc29b36f623ba82aaf6724fd3b16718}
```
Flag gotten.

Flag: `battlex{1bc29b36f623ba82aaf6724fd3b16718}`

![Image](https://i.gifer.com/7DSJ.gif)


### Learning resources

1. [https://en.wikipedia.org/wiki/SquashFS](https://en.wikipedia.org/wiki/SquashFS)