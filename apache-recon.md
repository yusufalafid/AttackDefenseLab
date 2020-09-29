Apache Basic Recon
==================

Machine     ==> Target
192.36.39.2     192.36.39.3

#### 1. Show apache version using nmap

```
root@attackdefense:~# nmap -sV --script banner 192.36.39.3
Starting Nmap 7.70 ( https://nmap.org ) at 2020-09-29 12:36 UTC
Nmap scan report for target-1 (192.36.39.3)
Host is up (0.000015s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
MAC Address: 02:42:C0:24:27:03 (Unknown)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 21.77 seconds
```

**Answer** : Apache/2.4.18

#### 2. Show apache version using metasploit module

```
root@attackdefense:~# msfconsole
msf5 > use auxiliary/scanner/http/http_version
msf5 auxiliary(scanner/http/http_version) > set RHOST 192.36.39.3
RHOST => 192.36.39.3
msf5 auxiliary(scanner/http/http_version) > exploit

[+] 192.36.39.3:80 Apache/2.4.18 (Ubuntu)
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
msf5 auxiliary(scanner/http/http_version) >
```

**Answer** : Apache/2.4.18

#### 3. Check what web hosted on the web server

```
root@attackdefense:~# curl 192.36.39.3

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
  <!--
    Modified from the Debian original for Ubuntu
    Last updated: 2014-03-19
    See: https://launchpad.net/bugs/1288690
  -->
  <head>
```

#### 4. Bruteforce webserver directory using metasploit module

```
msf5 > use auxiliary/scanner/http/brute_dirs
msf5 auxiliary(scanner/http/brute_dirs) > set RHOST 192.36.39.3
RHOST => 192.36.39.3
msf5 auxiliary(scanner/http/brute_dirs) > exploit

[*] Using code '404' as not found.
[+] Found http://192.36.39.3:80/dir/ 200
[+] Found http://192.36.39.3:80/src/ 200
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
msf5 auxiliary(scanner/http/brute_dirs) >
```

#### 5. Bruteforce webserver directory using dirb

```
root@attackdefense:~# dirb http://192.36.39.3 /usr/share/metasploit-framework/data/wordlists/directory.txt

-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Tue Sep 29 13:28:45 2020
URL_BASE: http://192.36.39.3/
WORDLIST_FILES: /usr/share/metasploit-framework/data/wordlists/directory.txt

-----------------

GENERATED WORDS: 24                                                            

---- Scanning URL: http://192.36.39.3/ ----
+ http://192.36.39.3//data (CODE:301|SIZE:309)                                                                                                                                                                                                                               
+ http://192.36.39.3//dir (CODE:301|SIZE:308)                                                                                                                                                                
-----------------
END_TIME: Tue Sep 29 13:28:45 2020
DOWNLOADED: 24 - FOUND: 2
root@attackdefense:~#
```
#### 6. Check robots

```
msf5 > use auxiliary/scanner/http/robots_txt
msf5 auxiliary(scanner/http/robots_txt) > set RHOST 192.36.39.3
RHOST => 192.36.39.3
msf5 auxiliary(scanner/http/robots_txt) > run

[*] [192.36.39.3] /robots.txt found
[+] Contents of Robots.txt:
User-agent: *
Disallow: /cgi-bin/
Disallow: Disallow: /junk/

User-agent: BadBot
Disallow: /no-badbot-dir/

[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
msf5 auxiliary(scanner/http/robots_txt) >
```
