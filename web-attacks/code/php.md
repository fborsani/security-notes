# PHP

## Run code

### Execute code

```
echo exec("<cmd>");
echo passthru("<cmd>");
echo system("<cmd>");
echo shell_exec("<cmd>");
echo `<cmd>`;
echo fread(popen("<cmd>", "<args>"), 4096);
proc_close(proc_open("<cmd>",array(),$something));
<?php preg_replace('/.*/e', 'system("<cmd>");', ''); ?>
pcntl_exec("<cmd>", ["arg1","arg2"]);

eval('<php code>')     #php delimiters are not required
assert('<php code>')
```

### File inclusion

```
include '<url>'
include_once '<url>'
require '<url>'
require_once '<url>'
```

## Information disclosure

```
phpinfo();
posix_mkfifo();
posix_getlogin();
posix_ttyname();
getenv();
get_current_user();
proc_get_status();
get_cfg_var();
disk_free_space();
disk_total_space();
diskfreespace();
getcwd();
getlastmo();
getmygid();
getmyinode();
getmypid();
getmyuid();
```

## Read/Write files

### System handlers

```
fopen("<file>","r")
tmpfile
bzopen
gzopen
```

### Read files

```
file_exists
file
fileatime
filectime
filegroup
fileinode
filemtime
fileowner
fileperms
filesize
filetype
glob
is_dir
is_executable
is_file
is_link
is_readable
is_uploaded_file
is_writable
is_writeable
linkinfo
lstat
parse_ini_file
pathinfo
readfile
readlink
realpath
stat
gzfile
readgzfile
getimagesize
imagecreatefromgif
imagecreatefromjpeg
imagecreatefrompng
imagecreatefromwbmp
imagecreatefromxbm
imagecreatefromxpm
ftp_put
ftp_nb_put
exif_read_data
read_exif_data
exif_thumbnail
exif_imagetype
hash_file
hash_hmac_file
hash_update_file
md5_file
sha1_file
```

### Write files

```
chgrp
chmod
chown
copy
file_put_contents
lchgrp
lchown
link
mkdir
move_uploaded_file
rename
rmdir
symlink
tempnam
touch
unlink
iptcembed
ftp_get
ftp_nb_get
scandir
```



