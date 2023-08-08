# Checklist

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
