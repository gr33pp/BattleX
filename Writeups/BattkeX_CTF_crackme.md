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
From Base85 ---> WW1GMGRHeGxXSHMzWkRjNU16QXpOMkV3TnpZd01UZzJOVGMwWWpBeU9ESm1NbVkwTXpWbE4zMD0=
From Base64 ---> YmF0dGxlWHs3ZDc5MzAzN2EwNzYwMTg2NTc0YjAyODJmMmY0MzVlN30=
From Base64 ---> battleX{7d793037a0760186574b0282f2f435e7}
```
![image](https://github.com/gr33pp/BattleX/blob/main/Assets/crackme1.png)

```
Flag: battleX{7d793037a0760186574b0282f2f435e7}
```
<hr> 

## Task2 #2

```
string: *>u_5vI=(wDH~%9>}>y;+s"a|;u<|K4K*au<+%#=~s|J};xb*;#>}?_l
```
This string is definitely a ROT cipher

I pasted the string to [cyberchef](https://gchq.github.io/CyberChef/) and tried ROT-13 cipher on it but it did not work

I tried ROT-47 on it and it gave a base64 string. I added From Base64 receipe to it and got the flag
```
ROT-47 ---> YmF0dGxlWHswOThmNmJjZDQ2MjFkMzczY2FkZTRlODMyNjI3YjRmNn0=
From Base64 ---> battleX{098f6bcd4621d373cade4e832627b4f6}
```
![image](https://github.com/gr33pp/BattleX/blob/main/Assets/crackme2.png)
```
Flag: battleX{098f6bcd4621d373cade4e832627b4f6}
```

## Task3 #3

```
string: 籥籴簿簻籥籴簿簺籥籴籀簽籥籴籀簽籥籴簿籙籥籴簿簾籥籴簾籁籥籴籀籘籥籴簼簾籥籴簿簾籥籴簿簻籥籴簿簾籥籴簼簻籥籴簼簻籥籴簼籂籥籴簼簽籥籴簿簾籥籴簿簼籥籴簿簽籥籴簼簹籥籴簿簾籥籴簼簹籥籴簿簿籥籴簼簹籥籴簼籁籥籴簿簾籥籴簿簺籥籴簿簻籥籴簼籀籥籴簼簿籥籴簼籂籥籴簼簹籥籴簿簽籥籴簼簻籥籴簿簺籥籴簼簿籥籴簿簾籥籴簿簾籥籴簼簿籥籴簼籂籥籴籀籚
```

This string looks likr chinese but it is not