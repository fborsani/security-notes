# Checklist

* Escape from limited shell and/or obtain full functionality
* Verify if the current shell is from a docker instance
* Get user information
  * Can use sudo
    * Sudo without password
    * Sudo rules with wildcards
    * Allowed commands can spawn elevated shells
    * Sudo version exploits
    * LD\_PRELOAD exploit
  * Docker/lxd groups exploit
  * Can switch to another user without password or using other known credentials
  * Check user history and config files in home directory
  * Readable or Writable sensitive files
    * Editable sudoers file
    * Editable passwd file
    * Editable .service files
    * Credentials or config files
  * Check environment variables
* Enumerate OS version for common vulnerabilities
  * Test for Shellshock exploit
* Enumerate sensitive files and folders
  * Globally readable/writable files
  * Binaries with SUID bit set
* Enumerate tasks and processes
  * Running processes
  * Ask the package manager (apt, dnf, yum, pacman...) for a list of installed software&#x20;
  * Check installed software for known CVEs
  * Check running services
  * Check crontab files for scheduled scripts
    * Edit scheduled scripts to execute arbitrary code
  * Expose processes listening on localhost by rerouting traffic through a SSH Reverse Proxy
* Enumerate and mount other drives or partitions
* Enumerate custom executables/scripts
  * Missing library hijack exploit
  * Relative path hijack exploit
