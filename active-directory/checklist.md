# Checklist

## Harvesting

* Dump local credentials
  * Plaintext in memory
  * SAM dump
    * Shadow volume exploit
  * DPAPI
  * Vault
  * LAPS
* ASREPRoast a remote machine for NTLM hashes
* Dump NTLM hashes
  * Password to hash conversion
  * PTH
  * Request Kerberos ticket
    * OPTH
      * Try for all users
    * PTT
  * PTK from NTLM
* Dump Kerberos tickets
  * Silver Key + PTT
  * Kerberoast for plaintext credentials
* Dump AES keys
  * PTK with AES128/AES256

## Lateral Movement

<table><thead><tr><th width="300.3333333333333">Service</th><th width="192">Port</th><th>Technique</th></tr></thead><tbody><tr><td>RDP</td><td>3389</td><td>Plaintext credentials<br>PTH</td></tr><tr><td>SMB</td><td>445</td><td>Plaintext credentials<br>Silver Ticket</td></tr><tr><td>MSSQL</td><td>1433</td><td>Silver Ticket</td></tr><tr><td>PS Remoting/WMIC/RunAs</td><td>445</td><td>Plaintext credentials<br>PTH<br>PTK</td></tr></tbody></table>

