# Task3 #1 (cat)

The first this i did was to dowload the Task file. After download the Taskfile, i navigated to the directory when the file was saved

From the name of the callenge "cat" i thought i was just to cat or strings the image file to get the flag but it was more than that.

I used stegseek on thr file to extract hidden datw from it. 
![image](https://github.com/gr33pp/BattleX/blob/main/Assets/cat.png)

I found out that the flag was hidden inside the image but was encoded with base64
![image](https://github.com/gr33pp/BattleX/blob/main/Assets/cat%202.png)

```
UEsDBAoAAAAAAOFu1FhmZvdoKgAAACoAAAAEABwAZmxhZ1VUCQADtTR0ZrU0dGZ1eAsAAQToAwAABOgDAABiYXR0bGVYezVmNGRjYzNiNWFhNzY1ZDYxZDgzMjdkZWI4ODJjZjk5fQpQSwECHgMKAAAAAADhbtRYZmb3aCoAAAAqAAAABAAYAAAAAAABAAAAtIEAAAAAZmxhZ1VUBQADtTR0ZnV4CwABBOgDAAAE6AMAAFBLBQYAAAAAAQABAEoAAABoAAAAAAA=
```
I deocoded the base65 string using base64 tool on my laptop

![image](https://github.com/gr33pp/BattleX/blob/main/Assets/cat-3.png)

```
Flag: battleX{5f4dcc3b5aa765d61d8327deb882cf99}
```
# Refences: https://github.com/RickdeJager/stegseek
