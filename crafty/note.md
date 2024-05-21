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
        

```
