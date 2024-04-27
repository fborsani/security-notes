# AWS Commands

## Access Control

### Accounts

Create account

```
aws iam create-login-profile --user TryHackMe-IAM-User
```

Change password

```
aws iam update-login-profile --user <IAM> --password '<password>'
```

### Granting access

Set password for AWS Console Access. If not present the IAM User is not allowed to login to AWS Dashboard and use GUI based services

```
aws iam update-login-profile --user <IAM> --password '<password>'
```

Create Access Key

```
aws iam create-access-key --user-name <user name>
```

&#x20;Create Session Token

```
aws sts get-session-token
```

### Manage Access Keys

Enable/disable keys

```
aws iam update-access-key --access-key-id <key id> --status Active
aws iam update-access-key --access-key-id <key id> --status Inactive
```

Delete key

```
aws iam delete-access-key --access-key-id <key id>
```

## Buckets

### Generate a presigned url

Create a presigned resource url. This allows to access the resource even if hosted in a private bucket simply by following the generated URL.

```
aws s3 presign s3://<bucket>/<path to resource> --expires-in 604800
```

For buckets created after It is necessary to provide an endpoint and a region.

```
aws s3 presign s3://<bucket>/<path to resource> --expires-in 604800 --region <region> --endpoint-url <endpoint url>
```
