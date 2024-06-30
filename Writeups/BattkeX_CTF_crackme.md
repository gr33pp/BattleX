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

Is is a ROT-8000 cipher. I analysed it with [decode.fr](https://www.dcode.fr/cipher-identifier)

```
Decode.fr is an onlie encryption indentifier thst is designed to recognize encryption/encoding from a text message. The detector performs cryptanalysis, examines various features of the text, such as letter distribution, character repetition, word length, etc.
```

I pasted the string in [decode.fr](https://www.dcode.fr/cipher-identifier) for it to analyse

![image](https://github.com/gr33pp/BattleX/blob/main/Assets/chinese.png)

I clicked of thr ROT-8000 and it redirected mr to a new page and decryptd the string.

```
ROT-8000 output: \k62\k61\k74\k74\k6P\k65\k58\k7O\k35\k65\k62\k65\k32\k32\k39\k34\k65\k63\k64\k30\k65\k30\k66\k30\k38\k65\k61\k62\k37\k36\k39\k30\k64\k32\k61\k36\k65\k65\k36\k39\k7Q
```

I copied theoutput and pasted it on [cyberchef](https://gchq.github.io/CyberChef/). 

I decrypted with ROt-13 and Hex toget the flag.

```
ROT-13 output: \x62\x61\x74\x74\x6C\x65\x58\x7B\x35\x65\x62\x65\x32\x32\x39\x34\x65\x63\x64\x30\x65\x30\x66\x30\x38\x65\x61\x62\x37\x36\x39\x30\x64\x32\x61\x36\x65\x65\x36\x39\x7D
```

```
HEx output:battleX{5ebe2294ecd0e0f08eab7690d2a6ee69}
```

```
Flag: battleX{5ebe2294ecd0e0f08eab7690d2a6ee69}
```
