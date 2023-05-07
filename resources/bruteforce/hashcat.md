# Hashcat

## Settings

### Attack mode (-a)

```
  # | Mode
 ===+======
  0 | Straight
  1 | Combination
  3 | Brute-force
  6 | Hybrid Wordlist + Mask
  7 | Hybrid Mask + Wordlist
```

### Charsets

```
  ? | Charset
 ===+=========
  l | abcdefghijklmnopqrstuvwxyz
  u | ABCDEFGHIJKLMNOPQRSTUVWXYZ
  d | 0123456789
  h | 0123456789abcdef
  H | 0123456789ABCDEF
  s |  !"#$%&'()*+,-./:;<=>?@[\]^_`{|}~
  a | ?l?u?d?s
  b | 0x00 - 0xff
```

### Workload (-w)

```
  # | Performance | Runtime | Power Consumption | Desktop Impact
 ===+=============+=========+===================+=================
  1 | Low         |   2 ms  | Low               | Minimal
  2 | Default     |  12 ms  | Economic          | Noticeable
  3 | High        |  96 ms  | High              | Unresponsive
  4 | Nightmare   | 480 ms  | Insane            | Headless
```

### Formats (-m)

See hash examples at: [https://hashcat.net/wiki/doku.php?id=example\_hashes](https://hashcat.net/wiki/doku.php?id=example\_hashes)

```
      # | Name                                             | Category
  ======+==================================================+======================================
    900 | MD4                                              | Raw Hash
      0 | MD5                                              | Raw Hash
    100 | SHA1                                             | Raw Hash
   1300 | SHA2-224                                         | Raw Hash
   1400 | SHA2-256                                         | Raw Hash
  10800 | SHA2-384                                         | Raw Hash
   1700 | SHA2-512                                         | Raw Hash
  17300 | SHA3-224                                         | Raw Hash
  17400 | SHA3-256                                         | Raw Hash
  17500 | SHA3-384                                         | Raw Hash
  17600 | SHA3-512                                         | Raw Hash
   6000 | RIPEMD-160                                       | Raw Hash
    600 | BLAKE2b-512                                      | Raw Hash
  11700 | GOST R 34.11-2012 (Streebog) 256-bit, big-endian | Raw Hash
  11800 | GOST R 34.11-2012 (Streebog) 512-bit, big-endian | Raw Hash
   6900 | GOST R 34.11-94                                  | Raw Hash
   5100 | Half MD5                                         | Raw Hash
  18700 | Java Object hashCode()                           | Raw Hash
  17700 | Keccak-224                                       | Raw Hash
  17800 | Keccak-256                                       | Raw Hash
  17900 | Keccak-384                                       | Raw Hash
  18000 | Keccak-512                                       | Raw Hash
  21400 | sha256(sha256_bin($pass))                        | Raw Hash
   6100 | Whirlpool                                        | Raw Hash
  10100 | SipHash                                          | Raw Hash
  21000 | BitShares v0.x - sha512(sha512_bin(pass))        | Raw Hash
     10 | md5($pass.$salt)                                 | Raw Hash, Salted and/or Iterated
     20 | md5($salt.$pass)                                 | Raw Hash, Salted and/or Iterated
   3800 | md5($salt.$pass.$salt)                           | Raw Hash, Salted and/or Iterated
   3710 | md5($salt.md5($pass))                            | Raw Hash, Salted and/or Iterated
   4110 | md5($salt.md5($pass.$salt))                      | Raw Hash, Salted and/or Iterated
   4010 | md5($salt.md5($salt.$pass))                      | Raw Hash, Salted and/or Iterated
  21300 | md5($salt.sha1($salt.$pass))                     | Raw Hash, Salted and/or Iterated
     40 | md5($salt.utf16le($pass))                        | Raw Hash, Salted and/or Iterated
   2600 | md5(md5($pass))                                  | Raw Hash, Salted and/or Iterated
   3910 | md5(md5($pass).md5($salt))                       | Raw Hash, Salted and/or Iterated
   4400 | md5(sha1($pass))                                 | Raw Hash, Salted and/or Iterated
  20900 | md5(sha1($pass).md5($pass).sha1($pass))          | Raw Hash, Salted and/or Iterated
  21200 | md5(sha1($salt).md5($pass))                      | Raw Hash, Salted and/or Iterated
   4300 | md5(strtoupper(md5($pass)))                      | Raw Hash, Salted and/or Iterated
     30 | md5(utf16le($pass).$salt)                        | Raw Hash, Salted and/or Iterated
    110 | sha1($pass.$salt)                                | Raw Hash, Salted and/or Iterated
    120 | sha1($salt.$pass)                                | Raw Hash, Salted and/or Iterated
   4900 | sha1($salt.$pass.$salt)                          | Raw Hash, Salted and/or Iterated
   4520 | sha1($salt.sha1($pass))                          | Raw Hash, Salted and/or Iterated
    140 | sha1($salt.utf16le($pass))                       | Raw Hash, Salted and/or Iterated
  19300 | sha1($salt1.$pass.$salt2)                        | Raw Hash, Salted and/or Iterated
  14400 | sha1(CX)                                         | Raw Hash, Salted and/or Iterated
   4700 | sha1(md5($pass))                                 | Raw Hash, Salted and/or Iterated
   4710 | sha1(md5($pass).$salt)                           | Raw Hash, Salted and/or Iterated
  21100 | sha1(md5($pass.$salt))                           | Raw Hash, Salted and/or Iterated
  18500 | sha1(md5(md5($pass)))                            | Raw Hash, Salted and/or Iterated
   4500 | sha1(sha1($pass))                                | Raw Hash, Salted and/or Iterated
    130 | sha1(utf16le($pass).$salt)                       | Raw Hash, Salted and/or Iterated
   1410 | sha256($pass.$salt)                              | Raw Hash, Salted and/or Iterated
   1420 | sha256($salt.$pass)                              | Raw Hash, Salted and/or Iterated
  22300 | sha256($salt.$pass.$salt)                        | Raw Hash, Salted and/or Iterated
   1440 | sha256($salt.utf16le($pass))                     | Raw Hash, Salted and/or Iterated
  20800 | sha256(md5($pass))                               | Raw Hash, Salted and/or Iterated
  20710 | sha256(sha256($pass).$salt)                      | Raw Hash, Salted and/or Iterated
   1430 | sha256(utf16le($pass).$salt)                     | Raw Hash, Salted and/or Iterated
   1710 | sha512($pass.$salt)                              | Raw Hash, Salted and/or Iterated
   1720 | sha512($salt.$pass)                              | Raw Hash, Salted and/or Iterated
   1740 | sha512($salt.utf16le($pass))                     | Raw Hash, Salted and/or Iterated
   1730 | sha512(utf16le($pass).$salt)                     | Raw Hash, Salted and/or Iterated
  19500 | Ruby on Rails Restful-Authentication             | Raw Hash, Salted and/or Iterated
     50 | HMAC-MD5 (key = $pass)                           | Raw Hash, Authenticated
     60 | HMAC-MD5 (key = $salt)                           | Raw Hash, Authenticated
    150 | HMAC-SHA1 (key = $pass)                          | Raw Hash, Authenticated
    160 | HMAC-SHA1 (key = $salt)                          | Raw Hash, Authenticated
   1450 | HMAC-SHA256 (key = $pass)                        | Raw Hash, Authenticated
   1460 | HMAC-SHA256 (key = $salt)                        | Raw Hash, Authenticated
   1750 | HMAC-SHA512 (key = $pass)                        | Raw Hash, Authenticated
   1760 | HMAC-SHA512 (key = $salt)                        | Raw Hash, Authenticated
  11750 | HMAC-Streebog-256 (key = $pass), big-endian      | Raw Hash, Authenticated
  11760 | HMAC-Streebog-256 (key = $salt), big-endian      | Raw Hash, Authenticated
  11850 | HMAC-Streebog-512 (key = $pass), big-endian      | Raw Hash, Authenticated
  11860 | HMAC-Streebog-512 (key = $salt), big-endian      | Raw Hash, Authenticated
  11500 | CRC32                                            | Raw Checksum
  14100 | 3DES (PT = $salt, key = $pass)                   | Raw Cipher, Known-Plaintext attack
  14000 | DES (PT = $salt, key = $pass)                    | Raw Cipher, Known-Plaintext attack
  15400 | ChaCha20                                         | Raw Cipher, Known-Plaintext attack
  14900 | Skip32 (PT = $salt, key = $pass)                 | Raw Cipher, Known-Plaintext attack
  11900 | PBKDF2-HMAC-MD5                                  | Generic KDF
  12000 | PBKDF2-HMAC-SHA1                                 | Generic KDF
  10900 | PBKDF2-HMAC-SHA256                               | Generic KDF
  12100 | PBKDF2-HMAC-SHA512                               | Generic KDF
   8900 | scrypt                                           | Generic KDF
    400 | phpass                                           | Generic KDF
  16900 | Ansible Vault                                    | Generic KDF
  12001 | Atlassian (PBKDF2-HMAC-SHA1)                     | Generic KDF
  20200 | Python passlib pbkdf2-sha512                     | Generic KDF
  20300 | Python passlib pbkdf2-sha256                     | Generic KDF
  20400 | Python passlib pbkdf2-sha1                       | Generic KDF
  16100 | TACACS+                                          | Network Protocols
  11400 | SIP digest authentication (MD5)                  | Network Protocols
   5300 | IKE-PSK MD5                                      | Network Protocols
   5400 | IKE-PSK SHA1                                     | Network Protocols
  23200 | XMPP SCRAM PBKDF2-SHA1                           | Network Protocols
   2500 | WPA-EAPOL-PBKDF2                                 | Network Protocols
   2501 | WPA-EAPOL-PMK                                    | Network Protocols
  22000 | WPA-PBKDF2-PMKID+EAPOL                           | Network Protocols
  22001 | WPA-PMK-PMKID+EAPOL                              | Network Protocols
  16800 | WPA-PMKID-PBKDF2                                 | Network Protocols
  16801 | WPA-PMKID-PMK                                    | Network Protocols
   7300 | IPMI2 RAKP HMAC-SHA1                             | Network Protocols
  10200 | CRAM-MD5                                         | Network Protocols
   4800 | iSCSI CHAP authentication, MD5(CHAP)             | Network Protocols
  16500 | JWT (JSON Web Token)                             | Network Protocols
  22600 | Telegram Desktop App Passcode (PBKDF2-HMAC-SHA1) | Network Protocols
  22301 | Telegram Mobile App Passcode (SHA256)            | Network Protocols
   7500 | Kerberos 5, etype 23, AS-REQ Pre-Auth            | Network Protocols
  13100 | Kerberos 5, etype 23, TGS-REP                    | Network Protocols
  18200 | Kerberos 5, etype 23, AS-REP                     | Network Protocols
  19600 | Kerberos 5, etype 17, TGS-REP                    | Network Protocols
  19700 | Kerberos 5, etype 18, TGS-REP                    | Network Protocols
  19800 | Kerberos 5, etype 17, Pre-Auth                   | Network Protocols
  19900 | Kerberos 5, etype 18, Pre-Auth                   | Network Protocols
   5500 | NetNTLMv1 / NetNTLMv1+ESS                        | Network Protocols
   5600 | NetNTLMv2                                        | Network Protocols
     23 | Skype                                            | Network Protocols
  11100 | PostgreSQL CRAM (MD5)                            | Network Protocols
  11200 | MySQL CRAM (SHA1)                                | Network Protocols
   8500 | RACF                                             | Operating System
   6300 | AIX {smd5}                                       | Operating System
   6700 | AIX {ssha1}                                      | Operating System
   6400 | AIX {ssha256}                                    | Operating System
   6500 | AIX {ssha512}                                    | Operating System
   3000 | LM                                               | Operating System
  19000 | QNX /etc/shadow (MD5)                            | Operating System
  19100 | QNX /etc/shadow (SHA256)                         | Operating System
  19200 | QNX /etc/shadow (SHA512)                         | Operating System
  15300 | DPAPI masterkey file v1                          | Operating System
  15900 | DPAPI masterkey file v2                          | Operating System
   7200 | GRUB 2                                           | Operating System
  12800 | MS-AzureSync PBKDF2-HMAC-SHA256                  | Operating System
  12400 | BSDi Crypt, Extended DES                         | Operating System
   1000 | NTLM                                             | Operating System
    122 | macOS v10.4, macOS v10.5, MacOS v10.6            | Operating System
   1722 | macOS v10.7                                      | Operating System
   7100 | macOS v10.8+ (PBKDF2-SHA512)                     | Operating System
   9900 | Radmin2                                          | Operating System
   5800 | Samsung Android Password/PIN                     | Operating System
   3200 | bcrypt $2*$, Blowfish (Unix)                     | Operating System
    500 | md5crypt, MD5 (Unix), Cisco-IOS $1$ (MD5)        | Operating System
   1500 | descrypt, DES (Unix), Traditional DES            | Operating System
   7400 | sha256crypt $5$, SHA256 (Unix)                   | Operating System
   1800 | sha512crypt $6$, SHA512 (Unix)                   | Operating System
  13800 | Windows Phone 8+ PIN/password                    | Operating System
   2410 | Cisco-ASA MD5                                    | Operating System
   9200 | Cisco-IOS $8$ (PBKDF2-SHA256)                    | Operating System
   9300 | Cisco-IOS $9$ (scrypt)                           | Operating System
   5700 | Cisco-IOS type 4 (SHA256)                        | Operating System
   2400 | Cisco-PIX MD5                                    | Operating System
   8100 | Citrix NetScaler (SHA1)                          | Operating System
  22200 | Citrix NetScaler (SHA512)                        | Operating System
   1100 | Domain Cached Credentials (DCC), MS Cache        | Operating System
   2100 | Domain Cached Credentials 2 (DCC2), MS Cache 2   | Operating System
   7000 | FortiGate (FortiOS)                              | Operating System
    125 | ArubaOS                                          | Operating System
    501 | Juniper IVE                                      | Operating System
     22 | Juniper NetScreen/SSG (ScreenOS)                 | Operating System
  15100 | Juniper/NetBSD sha1crypt                         | Operating System
    131 | MSSQL (2000)                                     | Database Server
    132 | MSSQL (2005)                                     | Database Server
   1731 | MSSQL (2012, 2014)                               | Database Server
     12 | PostgreSQL                                       | Database Server
   3100 | Oracle H: Type (Oracle 7+)                       | Database Server
    112 | Oracle S: Type (Oracle 11+)                      | Database Server
  12300 | Oracle T: Type (Oracle 12+)                      | Database Server
   7401 | MySQL $A$ (sha256crypt)                          | Database Server
    200 | MySQL323                                         | Database Server
    300 | MySQL4.1/MySQL5                                  | Database Server
   8000 | Sybase ASE                                       | Database Server
   1421 | hMailServer                                      | FTP, HTTP, SMTP, LDAP Server
   8300 | DNSSEC (NSEC3)                                   | FTP, HTTP, SMTP, LDAP Server
  16400 | CRAM-MD5 Dovecot                                 | FTP, HTTP, SMTP, LDAP Server
   1411 | SSHA-256(Base64), LDAP {SSHA256}                 | FTP, HTTP, SMTP, LDAP Server
   1711 | SSHA-512(Base64), LDAP {SSHA512}                 | FTP, HTTP, SMTP, LDAP Server
  10901 | RedHat 389-DS LDAP (PBKDF2-HMAC-SHA256)          | FTP, HTTP, SMTP, LDAP Server
  15000 | FileZilla Server >= 0.9.55                       | FTP, HTTP, SMTP, LDAP Server
  12600 | ColdFusion 10+                                   | FTP, HTTP, SMTP, LDAP Server
   1600 | Apache $apr1$ MD5, md5apr1, MD5 (APR)            | FTP, HTTP, SMTP, LDAP Server
    141 | Episerver 6.x < .NET 4                           | FTP, HTTP, SMTP, LDAP Server
   1441 | Episerver 6.x >= .NET 4                          | FTP, HTTP, SMTP, LDAP Server
    101 | nsldap, SHA-1(Base64), Netscape LDAP SHA         | FTP, HTTP, SMTP, LDAP Server
    111 | nsldaps, SSHA-1(Base64), Netscape LDAP SSHA      | FTP, HTTP, SMTP, LDAP Server
   7700 | SAP CODVN B (BCODE)                              | Enterprise Application Software (EAS)
   7701 | SAP CODVN B (BCODE) from RFC_READ_TABLE          | Enterprise Application Software (EAS)
   7800 | SAP CODVN F/G (PASSCODE)                         | Enterprise Application Software (EAS)
   7801 | SAP CODVN F/G (PASSCODE) from RFC_READ_TABLE     | Enterprise Application Software (EAS)
  10300 | SAP CODVN H (PWDSALTEDHASH) iSSHA-1              | Enterprise Application Software (EAS)
    133 | PeopleSoft                                       | Enterprise Application Software (EAS)
  13500 | PeopleSoft PS_TOKEN                              | Enterprise Application Software (EAS)
  21500 | SolarWinds Orion                                 | Enterprise Application Software (EAS)
   8600 | Lotus Notes/Domino 5                             | Enterprise Application Software (EAS)
   8700 | Lotus Notes/Domino 6                             | Enterprise Application Software (EAS)
   9100 | Lotus Notes/Domino 8                             | Enterprise Application Software (EAS)
  20600 | Oracle Transportation Management (SHA256)        | Enterprise Application Software (EAS)
   4711 | Huawei sha1(md5($pass).$salt)                    | Enterprise Application Software (EAS)
  20711 | AuthMe sha256                                    | Enterprise Application Software (EAS)
  12200 | eCryptfs                                         | Full-Disk Encryption (FDE)
  22400 | AES Crypt (SHA256)                               | Full-Disk Encryption (FDE)
  14600 | LUKS                                             | Full-Disk Encryption (FDE)
  13711 | VeraCrypt RIPEMD160 + XTS 512 bit                | Full-Disk Encryption (FDE)
  13712 | VeraCrypt RIPEMD160 + XTS 1024 bit               | Full-Disk Encryption (FDE)
  13713 | VeraCrypt RIPEMD160 + XTS 1536 bit               | Full-Disk Encryption (FDE)
  13741 | VeraCrypt RIPEMD160 + XTS 512 bit + boot-mode    | Full-Disk Encryption (FDE)
  13742 | VeraCrypt RIPEMD160 + XTS 1024 bit + boot-mode   | Full-Disk Encryption (FDE)
  13743 | VeraCrypt RIPEMD160 + XTS 1536 bit + boot-mode   | Full-Disk Encryption (FDE)
  13751 | VeraCrypt SHA256 + XTS 512 bit                   | Full-Disk Encryption (FDE)
  13752 | VeraCrypt SHA256 + XTS 1024 bit                  | Full-Disk Encryption (FDE)
  13753 | VeraCrypt SHA256 + XTS 1536 bit                  | Full-Disk Encryption (FDE)
  13761 | VeraCrypt SHA256 + XTS 512 bit + boot-mode       | Full-Disk Encryption (FDE)
  13762 | VeraCrypt SHA256 + XTS 1024 bit + boot-mode      | Full-Disk Encryption (FDE)
  13763 | VeraCrypt SHA256 + XTS 1536 bit + boot-mode      | Full-Disk Encryption (FDE)
  13721 | VeraCrypt SHA512 + XTS 512 bit                   | Full-Disk Encryption (FDE)
  13722 | VeraCrypt SHA512 + XTS 1024 bit                  | Full-Disk Encryption (FDE)
  13723 | VeraCrypt SHA512 + XTS 1536 bit                  | Full-Disk Encryption (FDE)
  13771 | VeraCrypt Streebog-512 + XTS 512 bit             | Full-Disk Encryption (FDE)
  13772 | VeraCrypt Streebog-512 + XTS 1024 bit            | Full-Disk Encryption (FDE)
  13773 | VeraCrypt Streebog-512 + XTS 1536 bit            | Full-Disk Encryption (FDE)
  13731 | VeraCrypt Whirlpool + XTS 512 bit                | Full-Disk Encryption (FDE)
  13732 | VeraCrypt Whirlpool + XTS 1024 bit               | Full-Disk Encryption (FDE)
  13733 | VeraCrypt Whirlpool + XTS 1536 bit               | Full-Disk Encryption (FDE)
  16700 | FileVault 2                                      | Full-Disk Encryption (FDE)
  20011 | DiskCryptor SHA512 + XTS 512 bit                 | Full-Disk Encryption (FDE)
  20012 | DiskCryptor SHA512 + XTS 1024 bit                | Full-Disk Encryption (FDE)
  20013 | DiskCryptor SHA512 + XTS 1536 bit                | Full-Disk Encryption (FDE)
  22100 | BitLocker                                        | Full-Disk Encryption (FDE)
  12900 | Android FDE (Samsung DEK)                        | Full-Disk Encryption (FDE)
   8800 | Android FDE <= 4.3                               | Full-Disk Encryption (FDE)
  18300 | Apple File System (APFS)                         | Full-Disk Encryption (FDE)
   6211 | TrueCrypt RIPEMD160 + XTS 512 bit                | Full-Disk Encryption (FDE)
   6212 | TrueCrypt RIPEMD160 + XTS 1024 bit               | Full-Disk Encryption (FDE)
   6213 | TrueCrypt RIPEMD160 + XTS 1536 bit               | Full-Disk Encryption (FDE)
   6241 | TrueCrypt RIPEMD160 + XTS 512 bit + boot-mode    | Full-Disk Encryption (FDE)
   6242 | TrueCrypt RIPEMD160 + XTS 1024 bit + boot-mode   | Full-Disk Encryption (FDE)
   6243 | TrueCrypt RIPEMD160 + XTS 1536 bit + boot-mode   | Full-Disk Encryption (FDE)
   6221 | TrueCrypt SHA512 + XTS 512 bit                   | Full-Disk Encryption (FDE)
   6222 | TrueCrypt SHA512 + XTS 1024 bit                  | Full-Disk Encryption (FDE)
   6223 | TrueCrypt SHA512 + XTS 1536 bit                  | Full-Disk Encryption (FDE)
   6231 | TrueCrypt Whirlpool + XTS 512 bit                | Full-Disk Encryption (FDE)
   6232 | TrueCrypt Whirlpool + XTS 1024 bit               | Full-Disk Encryption (FDE)
   6233 | TrueCrypt Whirlpool + XTS 1536 bit               | Full-Disk Encryption (FDE)
  10400 | PDF 1.1 - 1.3 (Acrobat 2 - 4)                    | Documents
  10410 | PDF 1.1 - 1.3 (Acrobat 2 - 4), collider #1       | Documents
  10420 | PDF 1.1 - 1.3 (Acrobat 2 - 4), collider #2       | Documents
  10500 | PDF 1.4 - 1.6 (Acrobat 5 - 8)                    | Documents
  10600 | PDF 1.7 Level 3 (Acrobat 9)                      | Documents
  10700 | PDF 1.7 Level 8 (Acrobat 10 - 11)                | Documents
   9400 | MS Office 2007                                   | Documents
   9500 | MS Office 2010                                   | Documents
   9600 | MS Office 2013                                   | Documents
   9700 | MS Office <= 2003 $0/$1, MD5 + RC4               | Documents
   9710 | MS Office <= 2003 $0/$1, MD5 + RC4, collider #1  | Documents
   9720 | MS Office <= 2003 $0/$1, MD5 + RC4, collider #2  | Documents
   9800 | MS Office <= 2003 $3/$4, SHA1 + RC4              | Documents
   9810 | MS Office <= 2003 $3, SHA1 + RC4, collider #1    | Documents
   9820 | MS Office <= 2003 $3, SHA1 + RC4, collider #2    | Documents
  18400 | Open Document Format (ODF) 1.2 (SHA-256, AES)    | Documents
  18600 | Open Document Format (ODF) 1.1 (SHA-1, Blowfish) | Documents
  16200 | Apple Secure Notes                               | Documents
  15500 | JKS Java Key Store Private Keys (SHA1)           | Password Managers
   6600 | 1Password, agilekeychain                         | Password Managers
   8200 | 1Password, cloudkeychain                         | Password Managers
   9000 | Password Safe v2                                 | Password Managers
   5200 | Password Safe v3                                 | Password Managers
   6800 | LastPass + LastPass sniffed                      | Password Managers
  13400 | KeePass 1 (AES/Twofish) and KeePass 2 (AES)      | Password Managers
  11300 | Bitcoin/Litecoin wallet.dat                      | Password Managers
  16600 | Electrum Wallet (Salt-Type 1-3)                  | Password Managers
  21700 | Electrum Wallet (Salt-Type 4)                    | Password Managers
  21800 | Electrum Wallet (Salt-Type 5)                    | Password Managers
  12700 | Blockchain, My Wallet                            | Password Managers
  15200 | Blockchain, My Wallet, V2                        | Password Managers
  18800 | Blockchain, My Wallet, Second Password (SHA256)  | Password Managers
  23100 | Apple Keychain                                   | Password Managers
  16300 | Ethereum Pre-Sale Wallet, PBKDF2-HMAC-SHA256     | Password Managers
  15600 | Ethereum Wallet, PBKDF2-HMAC-SHA256              | Password Managers
  15700 | Ethereum Wallet, SCRYPT                          | Password Managers
  22500 | MultiBit Classic .key (MD5)                      | Password Managers
  22700 | MultiBit HD (scrypt)                             | Password Managers
  11600 | 7-Zip                                            | Archives
  12500 | RAR3-hp                                          | Archives
  13000 | RAR5                                             | Archives
  17200 | PKZIP (Compressed)                               | Archives
  17220 | PKZIP (Compressed Multi-File)                    | Archives
  17225 | PKZIP (Mixed Multi-File)                         | Archives
  17230 | PKZIP (Mixed Multi-File Checksum-Only)           | Archives
  17210 | PKZIP (Uncompressed)                             | Archives
  20500 | PKZIP Master Key                                 | Archives
  20510 | PKZIP Master Key (6 byte optimization)           | Archives
  14700 | iTunes backup < 10.0                             | Archives
  14800 | iTunes backup >= 10.0                            | Archives
  23001 | SecureZIP AES-128                                | Archives
  23002 | SecureZIP AES-192                                | Archives
  23003 | SecureZIP AES-256                                | Archives
  13600 | WinZip                                           | Archives
  18900 | Android Backup                                   | Archives
  13200 | AxCrypt                                          | Archives
  13300 | AxCrypt in-memory SHA1                           | Archives
   8400 | WBB3 (Woltlab Burning Board)                     | Forums, CMS, E-Commerce
   2611 | vBulletin < v3.8.5                               | Forums, CMS, E-Commerce
   2711 | vBulletin >= v3.8.5                              | Forums, CMS, E-Commerce
   2612 | PHPS                                             | Forums, CMS, E-Commerce
    121 | SMF (Simple Machines Forum) > v1.1               | Forums, CMS, E-Commerce
   3711 | MediaWiki B type                                 | Forums, CMS, E-Commerce
   4521 | Redmine                                          | Forums, CMS, E-Commerce
     11 | Joomla < 2.5.18                                  | Forums, CMS, E-Commerce
  13900 | OpenCart                                         | Forums, CMS, E-Commerce
  11000 | PrestaShop                                       | Forums, CMS, E-Commerce
  16000 | Tripcode                                         | Forums, CMS, E-Commerce
   7900 | Drupal7                                          | Forums, CMS, E-Commerce
     21 | osCommerce, xt:Commerce                          | Forums, CMS, E-Commerce
   4522 | PunBB                                            | Forums, CMS, E-Commerce
   2811 | MyBB 1.2+, IPB2+ (Invision Power Board)          | Forums, CMS, E-Commerce
  18100 | TOTP (HMAC-SHA1)                                 | One-Time Passwords
   2000 | STDOUT                                           | Plaintext
  99999 | Plaintext                                        | Plaintext
  21600 | Web2py pbkdf2-sha512                             | Framework
  10000 | Django (PBKDF2-SHA256)                           | Framework
    124 | Django (SHA-1)                                   | Framework

```

## Presets

### .htpasswd

```
hashcat -w 2 -a 0 -m 1600 --username <file> <dict>
```

### GNUPG private key

```
gpg2john <file>.priv > creds.txt
john --wordlist=<dict> creds.txt

#use key to extract plaintext credentials
echo "<pkey>" | gpg --batch --yes --decrypt --passphrase-fd 0 <file>.gpg
```

### NTLM

```
hashcat -w 2 -a 0 -m 1000 <file> <dict>
hashcat -w 2 -a 3 -m 1000 <file> ?l?l?l?l?l
```

### /etc/shadow

```
hashcat -w 2 -a 0 -m 19000 --username <file> <dict>    #MD5
hashcat -w 3 -a 0 -m 19100 --username <file> <dict>    #SHA-256
hashcat -w 3 -a 0 -m 19200 --username <file> <dict>    #SHA-512
```

### MySQL

```
hashcat -w 3 -a 0 -m 200 <file> <dict>    #MySQL 323
hashcat -w 3 -a 0 -m 300 <file> <dict>    #MySQL 4.1/5
hashcat -w 3 -a 0 -m 11200 <file> <dict>  #MySQL SHA-1
```

### Archives

use rar2john to extract hashes from archives

```
rar2john <file>.rar > creds.txt
hashcat -w 2 -a 0 -m 13600 <file> <dict>    #Winzip
hashcat -w 2 -a 0 -m 11600 <file> <dict>    #7-zip
hashcat -w 2 -a 0 -m 23700 <file> <dict>    #RAR3-p compressed
hashcat -w 2 -a 0 -m 23800 <file> <dict>    #RAR3-p uncompressed
hashcat -w 2 -a 0 -m 12500 <file> <dict>    #RAR3-hp
hashcat -w 2 -a 0 -m 13000 <file> <dict>    #RAR5
```
