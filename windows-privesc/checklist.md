# Checklist

* Check current user
  * Check owned permissions for known exploits
    * Juicy Potato
  * Check read/write permissions on files and folders
    * SAM and SECURITY backup files
    * Service config files (Wordpress, rdp, htaccess...)
  * Check for owned services
  * Check groups
* Check OS version and applied hotfixes for CVEs
* Enumerate software
  * Applications with known exploits
  * Applications with global writable files/folders
    * DLL injection
  * Applications/services listening to localhost
  * Unquoted path exploit
* Enumerate services with lax permissions
  * Enumerate with accesschk
    * Unquoted path exploit
    * Modify service configuration to execute arbitrary files/commands
* Enumerate drivers for known exploits
* Check registry configuration
  * Always install elevated
  * UAC bypass
  * Credentials stored in registry (Putty, SNMP,  VNC...)
