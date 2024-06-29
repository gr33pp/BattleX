## BattleX CTF: forensics 
#### Category: Forensics
#### Score: 30
#### Number of Solves: 5 
### Description

> what is the flag?

**Files Attached:** hacksparo-1719532531350.pcapng

### TL; DR

Network log file (pcapng) anaysis using Wireshark, Profit.
    
### Log analysis with Wireshark

I downloaded the attachment and it turned out to be a network capture file, probably made with wireshark. So, I proceeded to open it with wireshark.

![wireshark hacksparo-1719532531350.pcapng](https://i.imgur.com/31HzB92.png)

_Regular Wireshark Screen_

I srolled down a bit and saw HTTP requests, whhich ended up worth checking. I applied HTTP filter to bring up related stuff.

![http_request_filtered](https://i.imgur.com/nMuy25w.png)

_Marked suspicious http request on entry 8998_

On inspection, I saw a post request from ip 172.20.10.3 to 172.20.10.8 which is the server. The request contained text that resembles base64. I proceeded to export it as object and try to make sense out of it. To do this is easy. 

File > Export Objects > HTTP... 

![export_upload](https://i.imgur.com/Q3by0mN.png)

```
-----------------------------401050937020745333952489344779
Content-Disposition: form-data; name="file"; filename="tazine"
Content-Type: application/octet-stream

iVBORw0KGgoAAAANSUhEUgAAAVEAAAAcCAYAAADYx4xEAAAABHNCSVQICAgIfAhkiAAAABl0RVh0U29mdHdhcmUAZ25vbWUtc2NyZWVuc2hvdO8Dvz4AAAAmdEVYdENyZWF0aW9uIFRpbWUAVGh1IDI3IEp1biAyMDI0IDIzOjM1OjAzCR0IsAAAFFtJREFUeJztnXtczfcfx5/ndHRRuSYp1dCaiuXOzzDzG2P4MSzEXHMnhhiihOU+ueReW0xMmkRuM3NJllvKXdGMLlapdTvUqd8fR0dHdb5fD8Y23+fj0ePROe/v9/N+f17nc/t+Pp/zObJ3GzYt5jnk+sU4r7yBkZWSwmwFMZMceJKleP4yLZydG7NrRyDXr98gJHQfVlaWNGrkwIiREzA1Meb0ySPExMThv34zHTq0Y9hQV5KSk/lfr4Fk52QD4OTkyI8h29m//
[snip]
```
_upload.php_

![profit](https://i.imgur.com/9mTHku1.png)

Using https://base64.guru , I was able to convert that to a file as its clearly not a plain text. 

Flag: `battlex{e99a18c428cb38d5f260853678922e03}`

### References

1. [https://base64.guru/converter/decode/file](https://base64.guru/converter/decode/file)
