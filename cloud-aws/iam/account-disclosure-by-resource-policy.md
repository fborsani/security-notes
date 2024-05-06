# Account Disclosure by resource policy

## SNS publish policy

Use the assign policy command to enumerate existing accounts. Create an SNS topic and an access policy where we specify the Account ID we want to test. If we don't receive an error then we have confirmed the existence of the account

Create SNS topic

```
aws sns create-topic --name <arbitrary name>
```

Create the following file locally and save it as a JSON file

```
{
  "Version": "2012-10-17",
  "Statement": [{
    "Sid": "grant-1234-publish",
    "Effect": "Allow",
    "Principal": {
      "AWS": "<account to test>"
    },
    "Action": ["sns:Publish"],
    "Resource": "<topic arn>"
  }]
}
```

Assign the policy to the bucket

```
aws sns set-topic-attributes --topic-arn <arn> --attribute-name 'Policy' --attribute-value file://<policy file>.json
```

## Bucket policies

By trying to assign a role to a bucket we own it is possible to check if a specific user exists. Since the role is owned by a user we input the user account id we want to test (12 ciphers without hyphen) and an arbitrary role name. If the operation succeeds we have confirmed that the account exists

Create the following file locally and save it as a JSON file

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::<account to test>:role/test_role"
      },
      "Action": "s3:*",
      "Resource": "arn:aws:s3:::<bucket name>*"
    }
  ]
}
```

Assign the policy to the bucket

```
aws s3api put-bucket-policy --bucket <bucket name> --policy file://<policy file>.json
```



