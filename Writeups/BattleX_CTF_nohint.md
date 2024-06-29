## BattleX CTF: nohint 
#### Category: Steganography
#### Score: 30
#### Number of Solves: 3 
### Description

> flag

**Files Attached:** photo1719581039-1719584076332.jpeg

### TL;DR

Fix _file magic_ and use steghide to extract the file

    
### Magician

This attached file doesn't render like a picture and doesn't show up as one, even with file command:
```
file photo1719581039-1719584076332.jpeg

photo1719581039-1719584076332.jpeg: data
```
Data... nah, there should be more to it. I proceeded to run `hexedit` on the file and I saw that it has a broken header. Headers are important in files to actually distinguish them from other file type.

![magic](https://i.imgur.com/GTnWlnK.png)

The remnant of the header gave me the clue that it was an actual JPEG file. Next I retrieved the correct _magic_ on Wikipedia to overwrite and repair the file. 

![file magic from wikipedia](https://i.imgur.com/vmkDiJL.png)

Repair done. 

![repair done](https://i.imgur.com/ekl5wjt.png)

Resulting image

![Resulting image](https://i.imgur.com/7iPb4Ql.jpg)

_No flag in it???_

### Steghide

I was expecting the flag to show up in the photo but I was wrong. Next thing is to try steganography tools like steghide or stegseek (fastest steghide bruteforcer). It turned out to be the perfect thing 

```
stegseek photo1719581039-1719584076332.jpeg

StegSeek 0.6 - https://github.com/RickdeJager/StegSeek

[i] Found passphrase: "mustang"
[i] Original filename: "fccccccc.bin".
[i] Extracting to "photo1719581039-1719584076332.jpeg.out".
```
I chose stegseek over steghide because it is fast and will bruteforce when password is used. Now, lets see the result

```
cat photo1719581039-1719584076332.jpeg.out

SP01 battlex{5f4dcc3b5aa765d61d8327deb882cf99}
```
Yay, the flag

Flag: `battlex{5f4dcc3b5aa765d61d8327deb882cf99}`

### Bonus 

![Illustration](https://i.imgur.com/F7WSZKo.jpg)

### References

1. [https://en.wikipedia.org/wiki/List_of_file_signatures](https://en.wikipedia.org/wiki/List_of_file_signatures)
2. [Stegseek](https://github.com/RickdeJager/StegSeek)

