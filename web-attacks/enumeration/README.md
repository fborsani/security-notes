# Enumeration

## Google Dorks

```
"Info:<url>"
site:<url>
site:<url> -inurl:https
site:<url> -filetype: .
site:<url> filetype:Log allintext:"username" "password"
site:<url> ext:pdf
```

#### Social media

```
<query> @<facebook|twitter|linkedin...>
```

### Specific dorks

#### SQL Dump

```
"index of" "database.sql.zip" 
```

#### FTP server

```
intitle:”index of” inurl:ftp
```

#### Wordpress admin page

```
intitle:"Index of" wp-admin                 
```

#### Apache2

```
intitle:"Apache2 Ubuntu Default Page: It works" 
```

#### PHPMyAdmin

```
"Index of" inurl:phpmyadmin                          #phpMyAdmin
```

#### JIRA/Kibana

```
inurl:Dashboard.jspa intext:"Atlassian Jira Project Management Software"
inurl:app/kibana intext:Loading Kibana
```

#### cPanel password reset

```
inurl:_cpanel/forgotpwd
```

## Site analysis

### WhatWeb

Advanced web scanner and spider tool. Download from [here](https://github.com/urbanadventurer/WhatWeb).

```
whatweb -v <url>                     #standard scan
whatweb -v -a3 <url>                 #aggressive scan
whatweb -v <url> -u <user>:<pass>    #basic auth
```

#### Use with proxy

```
whatweb -v <url> --proxy <url> --proxy-user <user>:<pass>
```

#### Plugins

```
whatweb -l                #list all plugins
whatweb -I <keyword>      #search by keyword and show detailed description
whatweb --dorks=<plugin>  #list google dorks used by the plugin
whatweb -i <plugin> <url> #use the specified plugin during scan
```

### Nikto

```
nikto -host <site url> -port <port> -maxtime <max time for scan>
nikto -host <site url> -port <port> -dbcheck
```

### Check for page formats

```
index.html    #static
index.php     #PHP
index.asp     #IIS
index.aspx    #IIS
index.do      #JSP based
index.jsp     #JSP based
```

### Dumping certificates

```
openssl s_client -connect <ip>
openssl x509 -in <file>.pem -text #pretty print certificate 
```

#### Create own certificate

Create and sign a certificate from a given Certificate Authority and private .pem key. After creation import the certificate in your browser.

```
openssl genrsa -out <name>.key 2048
openssl -req -new -key <name>.key -out <cert>.csr
openssl x509 -req -in <cert>.csr -CA <cert authority> -CAkey <remote key>.key -CAcreateSerial -days 1024 -sha256 -o <file>.pem
openssl pkcs12 -export -out <file>.pfx -in <file>.pem -inkey <name>.key -certfile <remote cert>.cert
```

## Specific sites

### Wordpress

#### Enumeration

```http
wpscan --url <host> --enumerate ap,at,cb,dbe
```

Remember to search listed plugins and modules for vulnerabilities.

#### Credentials dump:

This file contains plain-text username and password for database login. Sometimes these credentials can also be used to authenticate as another user on target machine.

```http
cat <wp folder>/wp-config.php
```

### Git repository

If you happen to find a .git folder on a server, you can dump all the files in the repository even if not directly accessible (403 error) with the following tool: [https://github.com/arthaud/git-dumper](https://github.com/arthaud/git-dumper)

```
python3 git-dumper.py <url> <local folder>
```
