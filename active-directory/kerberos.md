# Kerberos

## Kerbrute

Kerberos enumeration can be performed by using Kerbrute. This tool can be downloaded [here](https://github.com/ropnop/kerbrute/releases).

```
./kerbrute userenum --dc <DC> -d <domain> <userlist file>
./kerbrute bruteuser --dc <DC> -d <domain> <passwords file> <username>
./kerbrute passwordspray --dc <DC> -d <domain> <userlist file> <password>
./kerbrute bruteforce --dc <DC> -d <domain> <credentials file> #credentials are stored as user:pass
```
