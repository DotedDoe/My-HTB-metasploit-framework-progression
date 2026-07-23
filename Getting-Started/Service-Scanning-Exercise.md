### Sessions Exercise
 
IP: [target]
 
---
 
### Question 1:
Perform an Nmap scan of the target. What does Nmap display as the version of the service running on port 8080?
 
I started off with an Nmap scan against the target, and once that finished ran an Nmap scan targeting all ports in the background as I examined the first one's output.
 
```diff
+ $ nmap -sV -sC [target]
```
 
	PORT     STATE SERVICE     VERSION
	21/tcp   open  ftp         vsftpd 3.0.3
	22/tcp   open  ssh         OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
	80/tcp   open  http        Apache httpd 2.4.41 ((Ubuntu))
	139/tcp  open  netbios-ssn Samba smbd 4
	445/tcp  open  netbios-ssn Samba smbd 4
	2323/tcp open  telnet      Linux telnetd
	8080/tcp open  http        Apache Tomcat
 
I see that on port 8080 is an Apache Tomcat server.
 
&#x1F6A9; found **Apache Tomcat**.
 
---
 
### Question 2:
Perform an Nmap scan of the target and identify the non-default port that the telnet service is running on.
 
Along with this, I also notice that the telnet service is running on the non-default port of 2323, which answers question 2.
 
&#x1F6A9; found **2323**.
 
---
 
### Question 3:
List the SMB shares available on the target host. Connect to the available share as the bob user. Once connected, access the folder called 'flag' and submit the contents of the flag.txt file.
 
Using the credentials stated earlier in the section, I use smbclient to connect to the share as the user bob with his password and use `ls`, finding the flag directory just as the question said there would be.
 
```diff
+ $ smbclient //[target]/[share] -U bob
+ smb: \> ls
```
 
	  .                                   D        0  Thu Feb 25 18:06:52 2021
	  ..                                  D        0  Thu Feb 25 15:05:31 2021
	  flag                                D        0  Thu Feb 25 18:09:26 2021
	  bob                                 D        0  Thu Feb 25 16:42:23 2021
			4062912 blocks of size 1024. 1350212 blocks available
 
I then `cd` into flag and get the file with `get`.
 
```diff
+ smb: \> cd flag
+ smb: \flag\> ls
```
 
	  .                                   D        0  Thu Feb 25 18:09:26 2021
	  ..                                  D        0  Thu Feb 25 18:06:52 2021
	  flag.txt                            N       33  Thu Feb 25 18:09:26 2021
			4062912 blocks of size 1024. 1350212 blocks available
 
```diff
+ smb: \flag\> get flag.txt
```
 
	getting file \flag\flag.txt of size 33 as flag.txt (1.0 KiloBytes/sec) (average 1.0 KiloBytes/sec)
 
Going back to my local machine and listing the contents, I can see the flag was successfully transported and I can now read its contents.
 
```diff
+ smb: \flag\> exit
+ $ ls
+ $ cat flag.txt
```
 
	cacert.der  Documents  flag.txt  Pictures   Videos
	Desktop     Downloads  Music     Templates
 
	dceece590f3284c3866305eb2473d099
 
&#x1F6A9; found **dceece590f3284c3866305eb2473d099**.
