# Enable RDP

## Enable RDP

Check the status of the deny connection flags

```
reg query "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections
```

If not present or set to 1 use the following command to change the key value to 0. This will also open the default RDP port (3389) and start the service.

```
reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
```

Configure firewall

```
netsh firewall add portopening TCP 3389 "Remote Desktop"
```

IF during connection the following error appears: "Account Restrictions are preventing this user from signing in." Enable restricted admin mode on the server

```
reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f
```

## Enable RDP for users <a href="#enable-rdp-for-users" id="enable-rdp-for-users"></a>

**CMD**

```
net localgroup "Remote Desktop Users" <user> /add
net localgroup "Administrators" "<user>" /add #not necessary
```

**PS**

```
Add-LocalGroupMember -Group "Remote Desktop Users" -Member "<user>"
Add-LocalGroupMember -Group "Administrators" -Member "<user>" #not necessary
```
