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

## Buckets

List all buckets

```
aws s3 ls
```

List content of bucket

<pre><code>aws s3 ls s3:&#x3C;bucket name>    #root level
<strong>aws s3 ls s3:&#x3C;bucket name>/&#x3C;path>   
</strong></code></pre>
