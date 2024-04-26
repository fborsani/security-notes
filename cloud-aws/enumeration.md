# Enumeration

## Access Control

### Who am i?

```
aws iam get-user
aws iam get-account-summary
aws iam list-groups
aws iam list-roles
aws iam list-access-keys
```

### Other Users

```
aws iam list-users
aws iam list-groups-for-user --user-name <user>
```

Try to get root user from organization information (the username is the same as email for root accounts)

```
aws organizations describe-organization | grep Master
```

### Groups

```
aws iam list-groups
aws iam get-group --group-name <group name>
```

### Roles

```
aws iam list-roles
aws iam get-role --role-name <role-name>
```
