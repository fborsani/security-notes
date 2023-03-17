---
description: Useful commands quick reference
---

# Linux

## Administration

### Users

```bash
openssl passwd <password>
useradd -p "<password hash>" <username>
```

#### Add by editing /etc/passwd

```bash
echo "<username>:$(openssl passwd <password>):0:0:/root:/root:/bin/bash" >> /etc/passwd
```

### Groups

```bash
id
groups
getent group
```

#### Add group

```
sudo groupadd <group name>
```

#### Assign user to group

```bash
usermod -a -G <group> <user>
usermod -g <group> <user>    #transfer user to group

usermod -a -G root <user>    #add to root
usermod -a -G rdp <user>     #add to rdp enabled group
usermod -a -G ftp <user>     #add to ftp enabled group
```

### Firewall

```
iptables -L
iptables -S
iptables --flush
```

## Directories and files

### List subdirs

```bash
find . -type d 2>/dev/null
find / -type d -readable -exec ls -adl {} \; 2>/dev/null
```

### List files

```bash
find . -type f -name "<name>" 2>/dev/null
find . -type f -name "*.conf" 2>/dev/null
find . -type f -name "*.txt"  2>/dev/null
find . -type f -mtime +<days> 2>/dev/null                   #files older than x days
find . -type f -name"<name>" -exec cat {} \; 2>/dev/null    #find and print file
ls -h  
ls -lah
```

## Mounting

### mount / unmount

```bash
mount    #list currently mounted resources
mount -t <type> <ip>:/<share path> <path to mount point>
umount <path to mount point>
umount -f <path>
```

### Resolve "file is busy" unmount error

```bash
fuser <path to mount point>     #list all processes accessing the mount point
fuser -k <path to mount>        #kill all processes accessing the mount point
```

## Permissions

### Permission types

| Octal | Decimal | Representation | Permission         |
| ----- | ------- | -------------- | ------------------ |
| 000   | 0       | ---            | No permission      |
| 001   | 1       | --x            | Execute            |
| 010   | 2       | -w-            | Write              |
| 011   | 3       | -wx            | Write+Execute      |
| 100   | 4       | r--            | Read               |
| 101   | 5       | r--x           | Read+Execute       |
| 110   | 6       | rw-            | Read+Write         |
| 111   | 7       | rwx            | Read+Write+Execute |

### Check permissions

```
ls -alh <file>
```

### Alter permissions

```
chmod +<repr> <file> #add permission
chmod -<repr> <file> #drop permission
chmod <decimal for user,group,others i.e. 760> <file> #set all for users, r+w for owner group, none for others

chown <user> <file>            #change file owner
chown <user>:<group> <file>    #change file owner specifying user's group
```

## Archives

### Tar

```
--Add to archive--
tar -cf <archive>.tar <file1 file2... or dir/>
tar -uvf <archive>.tar <file to add>
--List files--
tar -tvf <archive>.tar
--Compress--
tar -czvf archive.tar.gz <dir>/
--Extract--
tar -xvf <archive>.tar -C <path to extract>
tar -xzvf <archive>.tar.gz -C <path to extract>
```

### Zip

```
--Archive--
7z a -t<type>  <archive>.<type> <dir>
7z a -mhe=on -p<password> <archive>.7z <dir>
--Extract--
7z e <archive>.<type> -o <dir output>
--Extract full paths--
7z x <archive>.<7z,zip,gzip,bzip2,tar> -o <dir output>
gunzip <archive>.zip > <filename>.txt
```

## Text

### Extract

Between two delimiters (from pipe or specify filename)

```bash
sed -n '/<begin>/,<end>/{/<begin>/b;/<end>/b;p}'
```

Get string length

```bash
echo -n "<str>" | wc -c
```

Read content from .gz archive

```bash
zcat <.gz archive>
```

### Base64

```bash
echo "<string>" | base64
echo -n "<base64 string>" | base64 -d
base64 -d <file> > <decoded file>
```

### Hex

```bash
xxd -p <file> | tr -d '\n' > <hexfile>.hex
echo -n "<hex string>" | xxd -r -ps
cat <hexfile>.hex | xclip -selection clipboard
```

## Useful Commands

```
su -c '<command>' <user>    
su - <user> -c '<command>'    #may ask for password
sudo -u <user> <cmd>          #simulate user login


find -type f -name "*.txt" -exec cat "{}" \;
find -type f -name "*.txt" -exec mv "{}" <dst> \;    #useful when mv throws errors
find -exec mv "{}" <dst> \;                          #move all files and folders from here

ls | wc -l    #count files in folder
wc -l <file>  #count the lines in file
wc -w <file>  #count the words in file
```

## Networking

### Default TTL

```bash
64
```

### Typical Ports

```bash
22    SSH
111   RPC
123   NTP
2049  NFS
```
