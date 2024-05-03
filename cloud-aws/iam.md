# IAM

## Structure

### Global components

#### Accounts

﻿An AWS account is a container for your AWS resources. AWS accounts are designated by a 12-digit unique number known as an Account ID (XXXX-XXXX-XX). AWS accounts are used together with IAM (access control) and AWS Identity to create a security boundary around a specific user by defining roles and allowed actions.

#### AWS Root account

To sign up for an AWS account, AWS users provide a root user e-mail address. An e-mail address can only be used as the root user e-mail address for one AWS account. The root user is considered an IAM "superuser" and has complete administrative permissions against the AWS account.&#x20;

By default, root users do not have multi-factor authentication (MFA) enforced. Furthermore, root accounts that do not have MFA enabled or a phone number set are susceptible to a weak password reset workflow attack. This is commonly the case for AWS accounts created automatically using the AWS Organizations service. If an attacker gains control of the e-mail inbox for an AWS root user without MFA or a phone number on the account, the attacker will be able to send a password reset e-mail and complete a takeover of the targeted AWS account.

### IAM

#### Authentication

The authentication procedure is composed by elements called Principals. These elements are used to define access rules to services and resources. A principal falls into one of the following categories:

* **Users**: represent a person or an application.  IAM Users are linked to a single AWS Account
* **Groups**:
* **Roles**:
* **Policies**:

<figure><img src="../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

#### Authorization procedure

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

## Authentication

### Access Key

Create Access Key

```
aws iam create-access-key    #for current user
aws iam create-access-key --user-name <user name>
```

Configure local variables

```
export AWS_SECRET_ACCESS_KEY="<access key>"
export AWS_ACCESS_KEY_ID="<key id>"
```

Alternatively these variables can be configured by running the following command on the local machine

```
aws configure
```

### Session Token

Create Session Token

```
aws sts get-session-token    #for current user
aws sts get-session-token --user-name <user name>
```

Configure local variables

```
export AWS_ACCESS_KEY_ID="<access key>"
export AWS_SECRET_ACCESS_KEY="<secret key>"
export AWS_SESSION_TOKEN="<session token>"
```

Configure with aws command

```
aws configure
aws configure set aws_session_token <session token>
```

### Assume Role

#### Gain temporary access to a resource

CLI Command. The session name is arbitrary and is requested only for logging purposes.

```
aws sts assume-role --role-arn <role arn> --role-session-name <session name>
```

Obtain temporary access to ECS instance. To be executed within the target instance

```
curl 169.254.170.2$AWS_CONTAINER_CREDENTIALS_RELATIVE_URI
```

Generate temporary credentials for Cloudshell instance. To be executed within the Cloudshell session. These credentials can be used to grant access to the shell to other users or services.

```
curl $AWS_CONTAINER_CREDENTIALS_FULL_URI -H "X-aws-ec2-metadata-token: $AWS_CONTAINER_AUTHORIZATION_TOKEN" 
```

After obtaining the credentials update the local environment

```
export AWS_ACCESS_KEY_ID="<access key>"
export AWS_SECRET_ACCESS_KEY="<secret key>"
export AWS_SESSION_TOKEN="<session token>"
```

## Access Control

### Accounts

Create account

```
aws iam create-login-profile --user <username>
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
