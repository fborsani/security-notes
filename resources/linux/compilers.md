# Compilers

## Binaries

### Debugger

```
edb
```

### EXE package maker

```
upx -9 <file>.exe
```

### .PY to EXE converter

```
pip install pyinstaller
pyinstaller --onefile -w 'filename.py'
```

### GCC

```
gcc <file>.c -o <filename>.o
gcc <file> -mtune=<i386,i486,i586,i686,generic,native>
gcc <file> -m32
gcc <file> -m64
```

#### Make a shared library

```
gcc -c -Wall -Werror -fpic <file>.c
gcc -shared -o <lib shared>.so <file>.o
```

#### Compile for older kernels

```
 gcc -Wall -Wl,--hash-style=both -o <file>.o <file>.c
```

## Cross compile

### To windows

```
i686-w64-mingw32-gcc <c file>.c -o <filename>.exe -<lib arg1> -<lib arg n> 
x86_64-w64-mingw32-gcc <c file>.c -o <filename>.exe -<lib arg1> -<lib arg n> 
```

#### Setup

```
sudo apt-get install mingw-w64 git-core wget quilt
```

### To \*nix

```
gcc <file> -m32 -mtune=<i386,i486,i586,i686,generic,native> -s --static
gcc -Wall -Wl,--hash-style=both -o <file>.o <file>.c -s --static
gcc <file> -m32 -mtune=<i386,i486,i586,i686,generic,native> -<package>
```

#### Setup

```
sudo dpkg --add-architecture i386
sudo dpkg --add-architecture i686
apt update
apt install <package>-dev:<arch>
```
