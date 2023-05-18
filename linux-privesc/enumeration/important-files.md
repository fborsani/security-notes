# Important files

## Globally readable/writable files and folders

### Files

Globally readable

```
find / \( -wholename '/home/*' -prune \) -o \( -type d -perm -0002 \) -exec ls -ld '{}' \; 2>/dev/null 
find / -xdev -type d \( -perm -0002 -a ! -perm -1000 \) 2>/dev/null
```

No owner

```
find / -xdev \( -nouser -o -nogroup \) -print 2>/dev/null
```

### Folders

Writable by current user or anyone

```
find / -writable -type d -exec ls -ld '{}' \; 2>/dev/null
```

## User files enumeration

Enumerate home folders

```
find /home/*/ -type f 2>/dev/null
ls -lah /home/*
ls -lah /home/*/*
```

Search files globally by user

```
find / -type f -exec ls -lah '{}' \; 2>/dev/null | grep $(whoami)
```

Search globally for user-owned folders

```
find / -type d -exec ls -ld '{}' \; 2>/dev/null | grep $(whoami);
```

Writable by me

```
find / -writable -type f -exec ls -lah '{}' \; 2>/dev/null
```

### Interesting files

```
cat ~/.bash_history
cat ~/.bashrc
cat ~/.profile

cat ~/.nano_history
cat ~/.atftp_history
cat ~/.mysql_history
cat ~/.php_history

cat ~/.ssh/authorized_keys
cat ~/.ssh/identity.pub
cat ~/.ssh/identity
cat ~/.ssh/id_rsa.pub
cat ~/.ssh/id_rsa
cat ~/.ssh/id_dsa.pub
cat ~/.ssh/id_dsa
```

## SSH keys

```
cat /etc/ssh/ssh_config
cat /etc/ssh/sshd_config
cat /etc/ssh/ssh_host_dsa_key.pub
cat /etc/ssh/ssh_host_dsa_key
cat /etc/ssh/ssh_host_rsa_key.pub
cat /etc/ssh/ssh_host_rsa_key
cat /etc/ssh/ssh_host_key.pub
cat /etc/ssh/ssh_host_key
```

Find SSH keys system-wide

```
find / -type f -and \( -name "*.key" -or -name "*.pub" -or -name "*rsa*" -or -name "*dsa*" \) -exec ls -lah '{}' \; 2>/dev/null
```

## Config files

Dump config files in current folder

```
find . -type f -readable -and \( -name "*db*" -or -name ".ht*" -or -name "*conf*" -or -name "*.cnf" -or -name "*.ini" -or -name "*.json" \) -exec ls -lah '{}' \; 2>/dev/null
```

Search for passwords in config files

```
find . -type f -readable -and \( -name "*db*" -or -name ".ht*" -or -name "*conf*" -or -name "*.cnf" -or -name "*.ini" -or -name "*.json" \) -exec grep -E ".*pass.*|.*pwd.*|.*cred.*" '{}' \; 2>/dev/null
```

Search for hashes in config files. Finds md5, SHA1, SHA256, SHA512 with or without salt

```
find . -type f -readable -and \( -name "*db*" -or -name ".ht*" -or -name "*conf*" -or -name "*.cnf" -or -name "*.ini" -or -name "*.json" \) -exec grep -E "(^|[^a-zA-Z0-9])[a-fA-F0-9=_/\.$]{8,256}([^a-zA-Z0-9]|$)" '{}' \; 2>/dev/null
```

## Log files

```
ls -lah cat /var/log/
ls -lah /var/log/*/
ls -lah /var/backup/
```

dump log files in current folder

```
find . -type f -readable -and \( -name "*.log" -or -name "*log*" \) -exec ls -lah '{}' \; 2>/dev/null
```



