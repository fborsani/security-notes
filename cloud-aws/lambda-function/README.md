# Lambda Function

## Enumeration

Get function list

```
aws lambda list-functions
```

Get function information

```
aws lambda list-functions --query "Functions[].[FunctionName, FunctionArn, Description, Role, Runtime, Handler, VpcConfig.VpcId, VpcConfig.SubnetIds[], VpcConfig.SecurityGroupIds[]]"
```

Get full function information

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

Get Roles and Security Policies for each function

```bash
for f in $(aws lambda list-functions --query "Functions[].FunctionName" --output text); do
    ROLE=`aws lambda get-function --function-name $f --query Configuration.Role --output text | awk -F\/ '{print $NF}'`
    echo "$f has $ROLE with these managed policies:"
    aws iam list-attached-role-policies --role-name $ROLE
    for p in `aws iam list-role-policies  --role-name $ROLE --query PolicyNames --output text` ; do
        echo "$ROLE for $f has inline policy $p:"
        aws iam get-role-policy --role-name $ROLE --policy-name $p
    done
done
```

Download source code of all functions

```bash
dump_folder="./aws_lambda_dump"
mkdir -p $dump_folder
for f in $(aws lambda list-functions --query "Functions[].FunctionName" --output text); do
    wget $(aws lambda get-function --function-name "$f" --query Code.Location --output text) -O "$dump_folder/$f.zip"
    mkdir "$dump_folder/$f"
    unzip "$dump_folder/$f.zip" -d "$dump_folder/$f"
done
```
