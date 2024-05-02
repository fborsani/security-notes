# Bucket S3

## Enumeration

List all buckets

```
aws s3 ls
```

List content of bucket

```
aws s3 ls s3:<bucket name>    #root level
aws s3 ls s3:<bucket name>/<path>   
```

Get bucket security policy

```
aws s3api get-bucket-policy --bucket <bucket name> --query Policy --output text | jq .
```

## Commands

### Download

Download single item

```
aws s3 cp s3://<bucket>/<path to file> .
```

Download folder

```
aws s3 sync s3://<bucket> .    #all
aws s3 sync s3://<bucket>/<path> . #all from specified path
```

### Generate a presigned url

Create a presigned resource url. This allows to access the resource even if hosted in a private bucket simply by following the generated URL.

```
aws s3 presign s3://<bucket>/<path to resource> --expires-in 604800
```

For buckets created after It is necessary to provide an endpoint and a region.

```
aws s3 presign s3://<bucket>/<path to resource> --expires-in 604800 --region <region> --endpoint-url <endpoint url>
```
