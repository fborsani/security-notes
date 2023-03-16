# Enumeration

## Users

### Who am i?

```
whoami
id
sudo -l
sudo --version
echo $PATH
printenv
(env || set) 2>/dev/null
```

### Other users

```
w
who -a
cat /etc/passwd
cat /etc/sudoers
cat /etc/group
```

### Try logging in

```
su <user>    #with or without password, try stupid things like root - root
sudo su      #switch to root, requires sudo permission and knowledge of the user's password
```

## OS

### Arch and kernel

```
hostname
cat /etc/issue
cat /etc/*-release
uname -a
uname -m
```

### Env and modules

```
sudo -V
lsmod
/sbin/modinfo <module name>
```

### Enumerate possible defenses

#### AppArmor

```bash
if [ `which aa-status 2>/dev/null` ]; then
    aa-status
  elif [ `which apparmor_status 2>/dev/null` ]; then
    apparmor_status
  elif [ `ls -d /etc/apparmor* 2>/dev/null` ]; then
    ls -d /etc/apparmor*
  else
    echo "Not found AppArmor"
fi
```

#### Grsecurity

```bash
((uname -r | grep "\-grsec" >/dev/null 2>&1 || grep "grsecurity" /etc/sysctl.conf >/dev/null 2>&1) && echo "Yes" || echo "Not found grsecurity")
```

#### PaX

```bash
(which paxctl-ng paxctl >/dev/null 2>&1 && echo "Yes" || echo "Not found PaX")
```

#### Execshield

```bash
(grep "exec-shield" /etc/sysctl.conf || echo "Not found Execshield")
```

#### SElinux

```bash
 (sestatus 2>/dev/null || echo "Not found sestatus")
```

#### ASLR

```bash
cat /proc/sys/kernel/randomize_va_space 2>/dev/null
#If 0, not enabled
```

## Processes

### Running

```
ps aux

#list processes without ps or top
readlink -f /proc/*/exe
cat /proc/*/comm
```

### Scheduled tasks

```
ls -lah /etc/cron*
cat /etc/crontab
cat /etc/cron-hourly
cat /etc/cron-daily
cat /etc/cron-weekly
cat /etc/cron-monthly
```

### Services

Enumerate service manager type

```
pstree | head -3    #check first line. It may display either systemd or init
```

Enumerate init.d services

```
service --status-all                     #list all services
service --status-all | grep '\[ + \]'    #list running services
service <name> status                    #service info
ls -l /etc/init.d/*                      #list all service conf files
```

Enumerate systemd services

<pre><code>systemctl list-units --type=service                                  #list all services
systemctl --type=service --state=&#x3C;active|running>                    #list running services
systemctl status &#x3C;service>                                           #service info
<strong>systemctl list-unit-files --state=enabled                            #list conf files of active services
</strong>ls -l /etc/systemd/system /usr/lib/systemd/service | grep .service   #list service conf files
</code></pre>

### Installed

```
dpkg -l
rpm -qa
pacman -Q
```

### Self-elevating binaries

```
find / -perm -u=s -type f 2>/dev/null
find / -perm -g=s -type f 2>/dev/null
getcap -r / | grep cap_setuid
```

## Network

### Interfaces

```
ifconfig -a
ip a
```

### Processes (reveal localhost services)

```
netstat -antp
ss -antp
lsof -i
```

### Dump

```
cat /sbin/route(l)
cat /sbin/tables
cat /proc/net/*
cat /etc/aliases
```

### Firewall

```
grep -Hs iptables /etc/*
cat /etc/iptables-backup
sudo cat /etc/iptables/*
```

## Drives

```
mount
cat /etc/fstab
/bin/lsblk
ls -alhtr /mnt
ls -alhtr /media
```

