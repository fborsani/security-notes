# Gaining access checklist

## Gaining access

* FTP
  * Anonymous login
  * Login with enumerated credentials
  * Service version specific vulnerabilities
* SMB
  * Enumerate available shares
  * Anonymous login
  * Login with enumerated credentials
  * On Windows > 7: DeepBlue remote exploit
* SMTP/IMAP/POP3
  * Enumerate users
  * Check emails for useful information
  * Service version specific vulnerabilities
* DNS
  * Use dig command with net transfer to enumerate domains
  * MX records can reveal potential usernames
* NFS
  * Mount shared folders
  * no\_root\_squash exploit
* SNMP
  * Dump tree structure for information
    * User information
    * Installed applications
    * OS information
* LDAP
  * Query for useful information
    * Users
      * SAM account name
      * Description field
      * Password field (if present)
    * Dump administrative password and login with PsExec
    * Machine information
* Database
  * Login with empty password
  * Login with enumerated credentials
    * Check for data to allow pivoting (usernames, passwords, hashes...)
    * Version specific exploitable capabilities
      * Unrestricted file access
      * Unrestricted file write
      * Code execution
    * Version specific exploits

## Chaining exploits

* Unrestricted file read
  * SSH: copy private key
  * FTP/SMB: enumerate content and retrieve files
  * HTTP server:
    * Get config files (.htpasswd, sql config files,...)
    * Get source code of pages for further analysis
    * Get application specific configuration files
    * Get files in upload folder
  * Access OS specific files like /etc/passwd
  * Access user specific files such as RDP config files, .ssh folder files, .gpg folder files
* Unrestricted file write
  * SSH: edit the authorized\_keys file
  * FTP/SMB: use the storage folders to upload exploits on the machine
  * HTTP server: inject a webshell or a page containing a reverse shell
  * Edit OS specific config files like /etc/passwd to gain access
