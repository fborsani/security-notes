# Lambda Function

## Enumeration

Get function information

```
aws lambda get-function --function-name <function arn>
```

Get function source code

```
wget $(aws lambda get-function --function-name <function arn> --query 'Code.Location' --output text)
```

Get security policies

```
aws lambda get-policy --query Policy --output text --function-name <function arn>
```

