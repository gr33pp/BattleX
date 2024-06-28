#  Task2 (crackme)

## Task2 #1
```
string: ='%+H9h/)PAOguI;b0W/=)1]K:/s#,;-7d59l!cc<,QYYA2#`<>'`e:<_#l.=''?-AQ:`Z;f+6a@TI8g<*4*@@RW,"9ggZu
```
I was thinking that this could be probably a ROT cipher, but i was wrong

I opened up [cyberchef](https://gchq.github.io/CyberChef/) and pasted the string there.

```
what is cyberchef?
CyberChef is a web-based application for performing various types of data manipulation tasks. It is designed to make data processing simple and efficient by providing a vast array of operations that can be combined in a visual and intuitive interface.
```
Immediately i pasted the string on cyberchef it suggested that the string was Base85

After decoding from Base85, it returned a Base64 string SO i decoded from base64 too

Afetr decodeing from Base64, it retunred another Base64 string. 

Finally it prompted out the flag

```
Recipie
From Base85
From Base64
From Base64
```
![image](https://github.com/gr33pp/BattleX/blob/main/Assets/crackme1.png)

```
Flag: battleX{7d793037a0760186574b0282f2f435e7}
```

