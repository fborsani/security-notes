# Windows

## Administration

### Users

```
net user <username> <password> /add
psexec.exe -accepteula -i -s %SystemRoot%\system32\cmd.exe    #escalate to SYSTEM
```

### Groups

```
net localgroups
whoami /all
```

#### Add group

```
net localgroup "<groupname>" <username> /add
net localgroup "Administrators" <username> /add
net localgroup "Remote Desktop Users" <username> /add
```

### Shares

#### List shares

```
net share
```

#### Create an unrestricted share

```
md C:\Users\<path>\share
net share <share name>=C:\Users\<path>\share /grant:everyone,full
```

#### Connect to a share

```
net use \\<ip>\<share name>            --connect
net use \\<ip>\<share name> /delete    --disconnect
```

#### Transfer files

```
xcopy <file>* \\<ip>\<share>\<file>*    #asterisks are needed to avoid prompts
xcopy \\<ip>\<share>\<file>* "C:\<path>\<file>*"
```

### Firewall

```
netsh firewall show state
netsh advfirewall firewall show rule name=all
netsh firewall show config
netsh firewall set opmode disable

Netsh Advfirewall show allprofiles
NetSh Advfirewall set allprofiles state off  
NetSh Advfirewall set allprofiles state on
```

#### Manage ports

```
netsh advfirewall firewall add rule name="NetBIOS <TCP/UDP> Port <port>" dir=out action=allow protocol=<TCP/UDP> localport=<port>
netsh firewall add portopening TCP 3389 "Remote Desktop"
```

#### Enable RDP

```
netsh advfirewall set all profiles state off
reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
netsh firewall add portopening TCP 3389 "Remote Desktop"
```

## Directories

### List subdirs

```
dir /ad /s /b
dir /ad /s
tree /a
```

### List files

```
dir *.txt /a-d /s /b <dir>
dir *.txt /ah /s /b <dir> #show hidden files ONLY
tree /a /f
```

#### Powershell

```
dir -Path C:\<folder> -Filter <filename>.* -Recurse -Force | %{$_.FullName}
dir -Path C:\<folder> -Filter *.txt -Recurse -Force | %{$_.FullName}
```

## Permissions

### Permission types

Permissions for each file:

* **N**: no access.
* **F**: full access.
* **M**: modify access.
* **RX**: read and execute access.
* **R**: read-only access.
* **W**: write-only access.
* **D**: delete access.

Inheritance types, shown only on folders:

* **(OI)**: object inherit.
* **(CI)**: container inherit.
* **(IO)**: inherit only.
* **(NP)**: don't propagate inherit.
* **(I)**: permission inherited from parent container.

### Check permissions

```
cacls <file>
icacls <file>
```

### Alter permissions

```
icacls <filename> /grant <user>:(<perm1, perm2,...>) #grants permissions to given user on specified file

icacls <filename> /setowner <owner> /T /L /Q /C    #change owner on recursive match
icacls <filename> /setowner <owner> /L /Q /C       #change owner to file
```

## Networking

### **Default TTL**

```
128
```

### Typical ports

```
23     Telnet
88     Kerberos
135    NetBIOS
139    MS-RPC
389    LDAP
445    SMB
8530   Windows Update Service
```









