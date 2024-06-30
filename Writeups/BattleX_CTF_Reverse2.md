# Task 5

First of all i downloaded the Task file and checked what type of file it is

![image](https://github.com/gr33pp/BattleX/blob/main/Assets/reverse-2.png)

I tried using the string command again and saw a string under “Enter the input.”

![image](https://github.com/gr33pp/BattleX/blob/main/Assets/rev3.png)

When I entered the string, the output was “Input does not match the required reversed value.” 
![image](https://github.com/gr33pp/BattleX/blob/main/Assets/rev-5.png)

So, I tried reversing the string before entering it again, and it gave me the flag.


To reverse the string, i wrote a smal python script

![image](https://github.com/gr33pp/BattleX/blob/main/Assets/rev4.png)

```
battlex ➤ python3                                                                                                                                                                                                              
Python 3.11.9 (main, Apr 10 2024, 13:16:36) [GCC 13.2.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> a = "3TWQfd2245njBEWrTX"
>>> b = a[::-1]
>>> print(b)
XTrWEBjn5422dfQWT3
>>> 
```

```
string: 3TWQfd2245njBEWrTX
reversed-string: XTrWEBjn5422dfQWT3
```

![image](https://github.com/gr33pp/BattleX/blob/main/Assets/rev6.png)

```
Flag:battleX{6c569aabbf7775ef8fc570e228c16b98}
```



