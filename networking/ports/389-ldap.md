# 389 - LDAP

## Enumeration

```
nmap -n -sV --script "ldap* and not brute" <ip>  
```

### Empty login

Enumerate users and associated data. The description field may contain plaintext credentials. The subdomain is usually the hostname of the machine.

```
ldapsearch -h <ip> -x -s base -b '' "(objectClass=*)" "*"    #dump all

ldapsearch -x -h <ip> -D "" -w "" -b "DC=<subdomain>,DC=<tld>" | grep sAMAccountName
ldapsearch -x -h <ip> -D "" -w "" -b "DC=<subdomain>,DC=<tld>" | grep description
ldapsearch -x -h <ip> -D "" -w "" -b "DC=<subdomain>,DC=<tld>" | grep userpas
```

## Enumeration with credentials

```
ldapsearch -x -h <ip> -D '<domain>\<user>' -w '<pass>' -b "<CN string>,DC=<subdomain>,DC=<TLD>"
```

Set `CN string` to one of the following values:

* `CN=Users`: list all users
* `CN=Administrators,CN=Builtin`: list all administrators
* `CN=<username>,CN=Users`: get information about a specific user
* `CN=Domain Admins,CN=Users`: list domain admins
* `CN=Domain Users,CN=Users`: list domain users
* `CN=Enterprise Admins,CN=Users`: list enterprise admins
* `CN=Computers`: list machines

## Administrative password

Requires access to LDAP with a valid username and password.

```
ldapsearch -x -h <ip> -D '<domain>\<user>' -w '<pass>' -b "DC=<subdomain>,DC=<tld>" "(ms-MCS-AdmPwd=*)" ms-MCS-AdmPwd 
```

If successful it is possible to login using impacket's psexec.py script. The user is usually the local administrator.

```
psexec.py <domain>/administrator:'<pass>'@<ip>
```
