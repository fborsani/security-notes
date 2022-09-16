# Enumeration

## Users

```
whoami
whoami /groups
whoami /priv
net user <username>
net user /domain
net group /domain
echo %username% %userdomain%
%WINDIR%\System32\dllcache\query.exe session
```

## OS

```
hostname
systeminfo
systeminfo | findstr /B /C:”OS Name” /C:”OS Version” /C:”System Type”
wmic qfe get Caption, Description, HotFixID, InstalledOn
wmic os get Caption,BuildNumber,Version
```

## Drivers

```
driverquery /v
sc qc <service>
--powershell--
driverquery.exe /v /fo csv | ConvertFrom-CSV | Select-Object ‘Display Name’, ‘Start Mode’, Path
Get-WmiObject Win32_PnPSignedDriver | Select-Object DeviceName, DriverVersion, Manufacturer | Where-Object {$_.DeviceName -like "*<Driver Name>*"}
```

## Processes

```
tasklist /SVC
tasklist /fi "pid eq <PID>"
wmic service list brief
tasklist /s 127.0.0.1 /u <user> /p <pass if required>
```

### Scheduled Tasks

```
schtasks /query /fo LIST /v
schtasks /query /fo LIST 2>nul | findstr <task>
schtasks /query /fo LIST /v > schtasks.txt; cat schtask.txt | grep "SYSTEM\|Task To Run" | grep -B 1 SYSTEM
```

### Startup Tasks

```
wmic startup get caption,command
reg query HKLM\Software\Microsoft\Windows\CurrentVersion\R
reg query HKCU\Software\Microsoft\Windows\CurrentVersion\Run
reg query HKCU\Software\Microsoft\Windows\CurrentVersion\RunOnce
dir "C:\Documents and Settings\All Users\Start Menu\Programs\Startup"
dir "C:\Documents and Settings\%username%\Start Menu\Programs\Startup"
```

### Applications

```
wmic product get name, version, vendor
```

## Network

```
ipconfig /all
route print
netstat -ano
```

### Firewall

```
netsh advfirewall show currentprofile
netsh advfirewall show rule name=all
netsh advfirewall show rule name=<name>
```

## Writable Directories

```
accesschk.exe /accepteula -uqws “Everyone” * #requires installation of accesschk.exe
--default writable folders--
C:\Windows\System32\Microsoft\Crypto\RSA\MachineKeys
C:\Windows\System32\spool\drivers\color
C:\Windows\Tasks
C:\windows\tracing
```

## Self-elevating binaries

```
reg query HKEY_CURRENT_USER\Software\Policies\Microsoft\Windows\Installer
reg query HKEY_LOCAL_MACHINE\Software\Policies\Microsoft\Windows\Installer
```

## Drives

```
mountvol
```

###
