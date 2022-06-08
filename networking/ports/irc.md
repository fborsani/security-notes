# IRC

## Login

### Standard user

```
nc -nCv <ip> <port>
USER foo * :foo
NICK foo
```

### Operator

```
nc -nCv <ip> <port>
OPER <username> <password>
```

## Commands

```
HELP
INFO
USERS
ADMIN
VERSION
STATS a    #privileged
NAMES
WHOIS
```
