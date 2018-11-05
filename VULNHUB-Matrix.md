# Write-UP Matrix

## Enumeration :

```nmap -p- 192.168.1.19```


## Port 80

It says to follow the white rabbit ... at the top bottom we can find a image of a white rabbit. When we click on it we can see it name :

p0rt_31337.png

## Port 31337

It's another website, same design as the first one. In the source code we find :

```ZWNobyAiVGhlbiB5b3UnbGwgc2VlLCB0aGF0IGl0IGlzIG5vdCB0aGUgc3Bvb24gdGhhdCBiZW5kcywgaXQgaXMgb25seSB5b3Vyc2VsZi4gIiA+IEN5cGhlci5tYXRyaXg= ```

It's Base 64, which decoded is :

```echo "Then you'll see, that it is not the spoon that bends, it is only yourself. " > Cypher.matrix```

At this point, I started to feel blocked. After minutes of unsuccessful enumeration, I found a file call Cypher.matrix at the root of the website.

## Cypher.matrix

The Cypher is actually braincode, when interprate it gives :

```You can enter into matrix as guest, with password k1ll0rXX
Note: Actually, I forget last two characters so I have replaced with XX try your luck and find correct string of password```
