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











```
