---
description: FTP usage and enumeration
---

# FTP

## Usage

### Login

```
ftp <username>@<ip>
```

Anonymous login:

```
ftp <domain>
user: anonymous
password: mail@mail.com
```

### Commands

```
?        to request help or information about the FTP commands
ascii    to set the mode of file transfer to ASCII (this is the default and transmits seven bits per character)
binary   to set the mode of file transfer to binary
bye      to exit the FTP environment (same as quit)
cd       to change directory on the remote machine
close    to terminate a connection with another computer but still leaves you within the FTP environment
delete   to delete (remove) a file in the current remote directory (same as rm in UNIX)
get      to copy one file from the remote machine to the local machine
help     to request a list of all available FTP commands
lcd      to change directory on your local machine (same as UNIX cd)
ls       to list the names of the files in the current remote directory
mkdir    to make a new directory within the current remote directory
mget     to copy multiple files from the remote machine to the local machine
mput     to copy multiple files from the local machine to the remote machine
open     to open a connection with another computer
passive  to toggle passive mode in order to avoid problems with client-side firewalls
put      to copy one file from the local machine to the remote machine
pwd      to find out the pathname of the current directory on the remote machine
quit     to exit the FTP environment (same as bye)
rmdir    to remove (delete) a directory in the current remote directory
```

### Download all

```
wget -m --user="<user>@<ip>" --password="<pass>" ftp://<ip or host>/
wget -m ftp://anonymous@<ip>/
```
