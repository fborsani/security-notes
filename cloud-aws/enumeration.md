# Enumeration

## Access Control

### Who am I?

When logged in remotely using access keys

```
aws sts get-caller-identity
```

From Cloudshell

```
aws iam get-user
aws iam get-account-summary
aws iam list-groups
aws iam list-roles
aws iam list-access-keys
```

### Access Keys

To identify users from a leaked Access Key use the following command

```
aws sts get-access-key-info --access-key-id <key id>
```

### MFA

Users using virtual MFA (TOTP)

```
aws iam list-virtual-mfa-devices
```

List of registered MFA devices

```
aws iam list-mfa-devices
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

### Policies

```
aws s3api get-bucket-policy-status --bucket <bucket name>
aws lambda get-policy --function-name <ARN> --query Policy --output text | jq
```

