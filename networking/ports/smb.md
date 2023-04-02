---
description: SMB Enumeration and login
---

# SMB

## Usage

### Login

```
smbclient \\\\<ip>\\<share>                #anonymous
smbclient -L \\\\<ip>\\<share> -U ‘<user>’ #with user
```

### Commands

```
? <command>    displays a brief informative message about the specified command
! <command>    will execute a shell locally and run the specified command
cd <dir>       change the remote directory to the directory specified, if run with no args show current dir    
del <mask>     attempt to delete all files matching mask from the current working directory on the server
dir <mask>     retrieves a list of files matching mask in current directory
exit           terminate the connection with the server and exit from the program
get <file>     copy the selected file to local machine
help <comm>    same as ? <command>
lcd <dir>      set local machine working directory, if run with no args show current local working dir
lowercase      toggle lowercasing of file names for the get and mget commands
ls <mask>      same as dir <mask>
mask <mask>    set a mask to be used during recursive operations
md <dir>       create a new directory with the given name
mget <mask>    copy all files matching mask in current directory to local machine
mkdir <dir>    same as md <dir>
mput <mask>    copy all files matching mask in local directory to smb server
prompt         toggle user confirmation when copying multiple files
put <file>     upload a file to the remote server
pwd            print current server folder, same as cd with no args
quit           same as exit
rd <dir>       removes remote directory
recurse        toggle recurse for mget and mput commands, uses the mask specified by the mask command to match files
rm <mask>      removes all files matching mask in current dir
rmdir <dir>    same as rd <dir>
```

### Download all

```
mask ""
recurse ON
prompt OFF
mget *
```

## Fix problems with older samba versions

```
nano /etc/samba/smb.conf
#under global section add the following line
client min protocol = LANMAN1
#save and restart
service smbd restart
```

## Enumeration

### NetBIOS

```
nmblookup -A <IP>    #enumerate all from given IP
nbtstat <ip>
sudo nmap -sU -sV  --script nbstat.nse -p 137 -Pn -n <IP>

nmap -v -p 139,445 --script=smb-os-discovery <ip addr>    #OS
nmap --script smb-vuln* -p 139,445 <ip addr>              #vulnerabilities
```

### Existing users

```
nmap --script smb-enum-users.nse -p445 <ip>
nmap -sU -sS --script smb-enum-users.nse -p U:137,T:139 <ip>
```

### Anonymous login

```
smbclient -N -L \\<ip>
rpcclient -N -L <ip>
```

### Shares

```
smbmap -H <ip>                                  #anonymous access
echo exit | smbclient -L \\\\<ip>               #anonymous access
bmap -H <ip> -u <root or username> -p ''        #user with empty password
smbmap -H <ip> -u <root or username> -p <pass>  #with user
nmap --script smb-enum-shares -p 139,445 <ip>
```
